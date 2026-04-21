# External partner event stream (Server-Sent Events)

Partners can open a **long-lived HTTP connection** that receives **push notifications** when certain backend actions occur. The API uses **Server-Sent Events (SSE)** with `Content-Type: text/event-stream`.

## Endpoint

| Item | Value |
|------|--------|
| **Method / path** | `GET /api/v1/external/events` |
| **Base URL** | Your deployed API host (e.g. `https://api.example.com`) |
| **Authentication** | Same **`WEBHOOK_API_KEY`** as other external APIs (`process.env.WEBHOOK_API_KEY` on the server) |

## Authentication

The middleware accepts the API key in this order:

1. Header **`x-api-key`**
2. Header **`api-key`**
3. Header **`Authorization`** (value compared as-is to the configured key)
4. **GET only:** query parameter **`api_key`** (required for browser `EventSource`, which cannot set custom headers)

Invalid or missing key returns **401 JSON** (not an SSE stream).

## SSE wire format

Each pushed message uses the standard SSE shape:

```text
event: <event_name>
data: <json_payload>

```

- **`event:`** — logical event name (see below). Omit parsing as generic `message` only if you subscribe to the default channel; prefer listening by `event` name.
- **`data:`** — single-line JSON object (stringified).
- Blank line — message terminator.

**Comment lines** (ignored by clients, used for keepalive):

```text
: keepalive

```

## Connection lifecycle

1. Client opens `GET /api/v1/external/events` with a valid API key.
2. Server responds with `text/event-stream` and immediately sends **`event: connected`** once.
3. Approximately every **30 seconds**, the server may send **`: keepalive`** so intermediaries (proxies, load balancers) do not close idle connections.
4. When the server emits a business event, all **currently connected** clients receive it (broadcast fan-out).
5. When the client disconnects, the server closes the response.

## Event reference

### `connected`

Sent once when the subscription is registered.

**`data` JSON:**

| Field | Type | Description |
|-------|------|-------------|
| `message` | string | e.g. `"subscribed"` |
| `timestamp` | string | ISO-8601 |

### `order_fulfilled`

Emitted when an order is **fully** warehouse-fulfilled (`POST /api/v1/fulfillOrder` completes with all lines satisfied, after the order is saved). Includes the full order document.

**`data` JSON:**

| Field | Type | Description |
|-------|------|-------------|
| `orderStatus` | string | Order status after save (e.g. `rider_assigned` when fully fulfilled). |
| `order` | object | Full order as plain JSON (`toObject`-style). |
| `orderId` | string | Business order id (e.g. `GUD-ORD-0001`). |
| `riderId` | string \| null | Assigned rider Mongo id string, if nearest-rider assignment succeeded. |
| `distanceKm` | number \| null | Haversine distance used for assignment, if a rider was assigned. |
| `timestamp` | string | ISO-8601 |

### `order_delivered`

Emitted when a rider marks delivery complete and the order is updated to **delivered** (delivery status update flow).

**`data` JSON:**

| Field | Type | Description |
|-------|------|-------------|
| `orderStatus` | string | e.g. `delivered` |
| `order` | object | Full order after update. |
| `orderId` | string | Business order id. |
| `riderId` | string | Rider id from the request. |
| `timestamp` | string | ISO-8601 |

### `inventory_inward`

Emitted when a batch **inventory inward** (QR/EAN scan inward) succeeds, **before** the inventory activity log row is written. Aligns with the same product lines and notes used for logging.

**`data` JSON:**

| Field | Type | Description |
|-------|------|-------------|
| `activity_type` | string | `INVENTORY_INWARD` |
| `destination_location` | string | `INVENTORY` |
| `productDetails` | array | Line items: `product_id`, `sku_code`, `quantity`, `unit_of_measure`, `qr_codes`, `scanned_codes_breakdown`. |
| `notes` | string | Human-readable summary (QR/EAN scan counts). |
| `timestamp` | string | ISO-8601 |

> **Note:** Additional fields may be added later; clients should tolerate unknown properties.

## Client examples

### curl (manual testing)

Use **`curl.exe`** on Windows so you get real curl (not PowerShell aliases). **`-N`** disables buffering so events print as they arrive:

```bash
curl.exe -N -H "x-api-key: YOUR_WEBHOOK_API_KEY" "https://YOUR_HOST/api/v1/external/events"
```

### Browser (`EventSource` + query key)

```javascript
const base = "https://YOUR_HOST";
const key = encodeURIComponent("YOUR_WEBHOOK_API_KEY");
const es = new EventSource(`${base}/api/v1/external/events?api_key=${key}`);

es.addEventListener("connected", (e) => {
  console.log("connected", JSON.parse(e.data));
});

es.addEventListener("order_fulfilled", (e) => {
  console.log("order_fulfilled", JSON.parse(e.data));
});

es.addEventListener("order_delivered", (e) => {
  console.log("order_delivered", JSON.parse(e.data));
});

es.addEventListener("inventory_inward", (e) => {
  console.log("inventory_inward", JSON.parse(e.data));
});

es.onerror = (err) => {
  console.error("SSE error", err);
};
```

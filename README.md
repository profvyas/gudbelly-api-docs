# GudBelly External API Guide



This guide explains how to integrate with the GudBelly External API to access product and category data, product availability (stock snapshots), order live tracking, and to create products, categories, and orders using the API key.

## Endpoint Status (Last tested: April 3, 2026)

| Endpoint | Method | Status |
|----------|--------|--------|
| `/external/products` | GET | ✅ Live |
| `/external/products/sku/:sku` | GET | ✅ Live |
| `/external/categories` | GET | ✅ Live |
| `/external/categories/:id/products` | GET | ✅ Live |
| `/external/orders/:id/live-tracking` | GET | ✅ Live |
| `/external/products/availability` | GET | ⚠️ Not yet deployed |
| `/createNewProductExternal` | POST | ✅ Live |
| `/createOrderExternal` | POST | ✅ Live |
| `/createCategoryExternal` | POST | ⚠️ Not yet deployed |



## Base URL



```

https://backend.gudbelly.filflo.in/api/v1/external

```



## Authentication



All API requests require an API key passed in the `x-api-key` header.



```bash

curl -H "x-api-key: YOUR_API_KEY" https://backend.gudbelly.filflo.in/api/v1/external/products

```



**Note:** Contact GudBelly to obtain your API key.



### Authentication Errors



| Status Code | Description |

|-------------|-------------|

| 401 | Missing or invalid API key |



## Response Format



All endpoints return JSON responses with the following structure:



```json

{

  "success": true,

  "message": "Description of the result",

  "timestamp": "2026-02-04T07:40:34.210Z",

  "data": [ ... ]

}

```



### Response Fields



| Field | Type | Description |

|-------|------|-------------|

| `success` | boolean | Indicates if the request was successful |

| `message` | string | Human-readable description of the result |

| `timestamp` | string | ISO 8601 timestamp of the response |

| `data` | array/object | The requested data |



---



## Endpoints



### 1. Get All Products



Retrieves a paginated list of all products.



**Endpoint:** `GET /products`



**Query Parameters:**



| Parameter | Type | Default | Description |

|-----------|------|---------|-------------|

| `page` | integer | 1 | Page number |

| `limit` | integer | 100 | Items per page |



**Example Request:**



```bash

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/products?page=1&limit=50"

```



**Example Response:**



```json

{

  "success": true,

  "message": "Products fetched successfully",

  "timestamp": "2026-02-04T07:40:34.210Z",

  "pagination": {

    "current_page": 1,

    "per_page": 100,

    "total_count": 256,

    "total_pages": 3

  },

  "count": 100,

  "data": [

    {

      "_id": "692a7ad9e27f04764e471c71",

      "product_name": "Wood-Pressed Sunflower Oil",

      "product_description": "Pantry Oil - Sunflower/Soy/Blend",

      "sku_code": "AV-OIL-002",

      "category": "Pantry - Oil - Sunflower/Soy/Blend",

      "category_id": "692a7ad8e27f04764e471c6c",

      "ppu": 550,

      "hsn_code": "7113",

      "tax_slab": "TAX-05",

      "unit_of_measure": "units",

      "case_size": 1,

      "weight": "0.5",

      "current_stock": 0,

      "image_url": null,

      "image_alt_text": null,

      "is_active": true,

      "created_at": "2025-11-29T04:47:21.128Z",

      "updated_at": "2026-02-02T10:48:50.113Z"

    }

  ]

}

```



**Product Fields:**



| Field | Type | Description |

|-------|------|-------------|

| `_id` | string | Unique product identifier |

| `product_name` | string | Product display name |

| `product_description` | string | Product description |

| `sku_code` | string | Stock Keeping Unit code |

| `category` | string | Category name |

| `category_id` | string | Category identifier |

| `ppu` | number | Price per unit |

| `hsn_code` | string | HSN code for taxation |

| `tax_slab` | string | Tax slab identifier |

| `unit_of_measure` | string | Unit of measurement |

| `case_size` | number | Units per case |

| `weight` | string | Product weight |

| `current_stock` | number | Current stock level |

| `image_url` | string | Product image URL (nullable) |

| `is_active` | boolean | Whether product is active |

| `created_at` | string | Creation timestamp |

| `updated_at` | string | Last update timestamp |



---



### 2. Get Product by SKU



Retrieves a single product by its SKU code.



**Endpoint:** `GET /products/sku/:sku`



**Path Parameters:**



| Parameter | Type | Description |

|-----------|------|-------------|

| `sku` | string | The product SKU code |



**Example Request:**



```bash

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/products/sku/AV-OIL-002"

```



**Example Response:**



```json

{

  "success": true,

  "message": "Product fetched successfully",

  "timestamp": "2026-02-04T07:45:00.000Z",

  "data": {

    "_id": "692a7ad9e27f04764e471c71",

    "product_name": "Wood-Pressed Sunflower Oil",

    "sku_code": "AV-OIL-002",

    ...

  }

}

```



---



### 3. Get All Categories



Retrieves all product categories.



**Endpoint:** `GET /categories`



**Example Request:**



```bash

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/categories"

```



**Example Response:**



```json

{

  "success": true,

  "message": "Categories fetched successfully",

  "timestamp": "2026-02-04T07:45:34.942Z",

  "count": 10,

  "data": [

    {

      "_id": "692a7ad9e27f04764e471c77",

      "category_name": "Beverages - Functional Soda / Sparkling Tonic",

      "category_code": null,

      "created_at": "2025-11-29T04:47:21.297Z",

      "updated_at": "2025-11-29T04:47:21.297Z"

    },

    {

      "_id": "692a7ad9e27f04764e471c82",

      "category_name": "Beverages - Kombucha",

      "category_code": null,

      "created_at": "2025-11-29T04:47:21.607Z",

      "updated_at": "2025-11-29T04:47:21.607Z"

    }

  ]

}

```



**Category Fields:**



| Field | Type | Description |

|-------|------|-------------|

| `_id` | string | Unique category identifier |

| `category_name` | string | Category display name |

| `category_code` | string | Category code (nullable) |

| `created_at` | string | Creation timestamp |

| `updated_at` | string | Last update timestamp |



---



### 4. Get Products by Category



Retrieves all products within a specific category.



**Endpoint:** `GET /categories/:categoryId/products`



**Path Parameters:**



| Parameter | Type | Description |

|-----------|------|-------------|

| `categoryId` | string | The category ID |



**Query Parameters:**



| Parameter | Type | Default | Description |

|-----------|------|---------|-------------|

| `page` | integer | 1 | Page number |

| `limit` | integer | 100 | Items per page |



**Example Request:**



```bash

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/categories/692a7ad9e27f04764e471c77/products"

```



**Example Response:**



```json

{

  "success": true,

  "message": "Products fetched successfully",

  "timestamp": "2026-02-04T07:50:00.000Z",

  "pagination": {

    "current_page": 1,

    "per_page": 100,

    "total_count": 15,

    "total_pages": 1

  },

  "count": 15,

  "data": [

    {

      "_id": "...",

      "product_name": "...",

      "sku_code": "...",

      ...

    }

  ]

}

```



---



### 5. Get live tracking by order



Returns the **most recent** delivery tracking task for a given business **order id** (same logic as `GET /api/v1/getOrderLiveTracking/:orderId`), authenticated with the API key instead of a rider/admin JWT.



| Item        | Value |

|------------|--------|

| **Method** | `GET` |

| **Path**   | `/api/v1/external/orders/:orderId/live-tracking` |



**Path parameters:**



| Param     | Description |

|-----------|-------------|

| `orderId` | The **orderâs business id** (same string as `orderId` on the order and on the `DeliveryTracking` document). This is **not** the MongoDB `_id` of the order unless your system uses that as the business id. |



If multiple tracking records exist for the same `orderId`, the server returns the newest by `createdAt`.



**Success response**



- **Status:** `200`

- **Body:** `{ "success": true, "message": "Order live tracking fetched successfully.", "data": { ...delivery tracking document } }`



**Useful fields on `data`:**



| Field | Notes |

|-------|--------|

| `status` | `assigned`, `pickedup`, `in_transit`, or `dropped` |

| `orderId`, `riderId` | Business order id; assigned rider (populated object below) |

| `pickupLocation`, `dropLocation` | `{ latitude, longitude, address? }` |

| `currentLocation` | On the **task**: `{ latitude?, longitude?, recordedAt? }` when live transit updates have been written to this record |

| `locationHistory` | Array of `{ latitude, longitude, recordedAt }` for the task |

| `pickedupAt`, `droppedAt`, `assignedAt` | ISO dates when set |

| `riderId` (populated) | `name`, `phone`, `vehicleNumber`, `isAvailable`, `lastLocationUpdatedAt`, **`currentLocation`** â rider position as GeoJSON: `{ type: "Point", coordinates: [longitude, latitude] }` |



**Example request**



There is no JSON body. Send the business `orderId` in the path and the API key in a header.



```http

GET /api/v1/external/orders/ORD-2025-00042/live-tracking HTTP/1.1

Host: backend.gudbelly.filflo.in

x-api-key: YOUR_API_KEY

```



```bash

curl -sS "https://backend.gudbelly.filflo.in/api/v1/external/orders/ORD-2025-00042/live-tracking" \

  -H "x-api-key: YOUR_API_KEY"

```



For **Node.js**, use `getOrderLiveTracking` in [Code Examples](#code-examples) â **JavaScript (Node.js with Axios)**. For **Python**, use `get_order_live_tracking` in **Python (requests)** there (same `base_url` and headers as the other snippets).



Replace `ORD-2025-00042` with the real order id. You can use `api-key` or `Authorization` instead of `x-api-key` (see [Authentication](#authentication)).



**Example response (`200`)**



Shape matches a saved `DeliveryTracking` document plus populated `riderId`. Values below are illustrative.



```json

{

  "success": true,

  "message": "Order live tracking fetched successfully.",

  "data": {

    "_id": "674a1b2c3d4e5f6789abcdef",

    "orderId": "ORD-2025-00042",

    "riderId": {

      "_id": "674a1b2c3d4e5f6789abc111",

      "name": "Ravi Kumar",

      "phone": "+919876543210",

      "vehicleNumber": "KA01AB1234",

      "isAvailable": false,

      "lastLocationUpdatedAt": "2026-03-29T10:15:00.000Z",

      "currentLocation": {

        "type": "Point",

        "coordinates": [77.5946, 12.9716]

      }

    },

    "status": "in_transit",

    "pickupLocation": {

      "latitude": 12.98,

      "longitude": 77.6,

      "address": "Gudbelly Warehouse"

    },

    "dropLocation": {

      "latitude": 12.95,

      "longitude": 77.58,

      "address": "Customer drop address"

    },

    "currentLocation": {

      "latitude": 12.97,

      "longitude": 77.59,

      "recordedAt": "2026-03-29T10:14:30.000Z"

    },

    "locationHistory": [

      {

        "latitude": 12.968,

        "longitude": 77.592,

        "recordedAt": "2026-03-29T10:10:00.000Z"

      }

    ],

    "assignedAt": "2026-03-29T09:00:00.000Z",

    "pickedupAt": "2026-03-29T09:45:00.000Z",

    "droppedAt": null,

    "createdAt": "2026-03-29T09:00:00.000Z",

    "updatedAt": "2026-03-29T10:15:00.000Z",

    "__v": 0

  }

}

```



`data.currentLocation` / `data.locationHistory` may be empty or omitted until live transit points are recorded on the task. `data.riderId.currentLocation` uses GeoJSON: **`coordinates` are `[longitude, latitude]`**.



**Error examples**



| Status | When |

|--------|------|

| `404`  | No `DeliveryTracking` row for that `orderId` |

| `401`  | Missing or invalid API key |

| `500`  | Server error |



**Example `404` body**



```json

{

  "success": false,

  "message": "No delivery tracking found for this order."

}

```



---



### Product availability (stock snapshot)

> **⚠️ Not yet deployed.** This endpoint is documented for upcoming release but currently returns 404 on production.



Returns **active** products with warehouse-oriented fields: `sku_code`, `product_description`, `current_stock`, and computed **`available`** (accounts for rack linkage and open order reservations on the server).



| | |

|---|---|

| **Method / path** | `GET /api/v1/external/products/availability` |

| **Query (optional)** | `sku_code` â filter by one SKU or several (comma-separated), e.g. `?sku_code=SKU-1` or `?sku_code=SKU-1,SKU-2` |



**Example request**



```bash

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/products/availability?sku_code=SKU-001,SKU-002"

```



Omit `sku_code` to return all active products with availability fields.



### Success response â HTTP 200



```json

{

  "success": true,

  "message": "Successfully fetched products with availability",

  "data": [

    {

      "sku_code": "SKU-001",

      "product_description": "Example item",

      "current_stock": 100,

      "available": 85

    }

  ]

}

```



When no matching active products exist, `data` is an empty array and `message` may be `"No active products found"`.



### Error responses



| Status | When |

|--------|------|

| `401` | Missing or invalid API key. |

| `500` | Server error. |



---



## 6. Create Product API (external â API key)



Creates a **Product** record (and a matching **SKU** when one does not already exist for the same `sku_code`). Intended for **external integrations** authenticated with the API key.



### Endpoint



| Method | Path |

|--------|------|

| `POST` | `/api/v1/createNewProductExternal` |



**Authentication:** Use your issued external API key (see Headers below; must match what the server expects for these routes).



Replace `{base_url}` with your API base through `/api/v1` (e.g. `https://your-host.com/api/v1`), same as the `base_url` variable in the [Code Examples](#code-examples).



### Query parameters



**None.** This operation does not read URL query strings.



### Headers



| Header | Required | Description |

|--------|----------|-------------|

| `Content-Type` | **Yes** | Must be `application/json` |

| `x-api-key` | **Yes** | API key (must match `YOUR_API_KEY`) |



### Request body (JSON)



#### Required fields



| Field | Type | Description |

|-------|------|-------------|

| `product_name` | string | Display name of the product |

| `sku_code` | string | Unique SKU; must not already exist on another product |

| `hsn_code` | string | HSN code |

| `tax_slab` | string | One of: `TAX-00`, `TAX-05`, `TAX-12`, `TAX-18` |

| `ppu` | number | Price per unit; must be â¥ 0 |



#### Optional fields



| Field | Type | Description |

|-------|------|-------------|

| `product_description` | string | Longer description |

| `product_category_id` | string | MongoDB ObjectId of a **Category** |

| `unit_of_measure` | string | `kilograms`, `litres`, or `units` (default: `units`) |

| `current_stock` | number | Default `0` if omitted |

| `current_count` | number | Stored if provided |

| `case_size` | number | Default in schema is `1` if omitted |

| `ean_code` | string | EAN / barcode |

| `cogs` | number | Cost of goods sold; default `0` if omitted |

| `blinkit_sku_code` | string | Saved only if non-empty after trim |

| `swiggy_sku_code` | string | Saved only if non-empty after trim |

| `zepto_sku_code` | string | Saved only if non-empty after trim |

| `amazon_sku_code` | string | Saved only if non-empty after trim |

| `easyecom_sku_code` | string | Saved only if non-empty after trim |

| `asin_code` | string | Saved only if non-empty after trim |

| `image_url` | string | Saved only if non-empty after trim |

| `image_alt_text` | string | Saved only if non-empty after trim |

| `additional_images` | string[] | Array of image URLs (only applied if value is an array) |



### Example request



```http

POST {base_url}/createNewProductExternal HTTP/1.1

Content-Type: application/json

x-api-key: YOUR_API_KEY

```



```json

{

  "product_name": "Example item",

  "product_description": "Short description",

  "product_category_id": "679f98c4eb05c298726ab7cc",

  "sku_code": "SKU-EXAMPLE-001",

  "unit_of_measure": "units",

  "current_stock": 0,

  "current_count": 0,

  "hsn_code": "7113",

  "ean_code": "8901234567890",

  "tax_slab": "TAX-05",

  "ppu": 199,

  "case_size": 1,

  "cogs": 0,

  "blinkit_sku_code": "BL-123",

  "image_url": "https://example.com/image.jpg"

}

```



**Minimal body (required fields only)**



```json

{

  "product_name": "Example item",

  "sku_code": "SKU-EXAMPLE-001",

  "hsn_code": "7113",

  "tax_slab": "TAX-05",

  "ppu": 199

}

```



### Example responses



#### 200 â Created



```json

{

  "success": true,

  "message": "Successfully created the Product",

  "timestamp": "2026-02-04T07:40:34.210Z",

  "data": [

    {

      "_id": "67a1b2c3d4e5f6789012345",

      "product_name": "Example item",

      "sku_code": "SKU-EXAMPLE-001",

      "hsn_code": "7113",

      "tax_slab": "TAX-05",

      "ppu": 199,

      "unit_of_measure": "units",

      "current_stock": 0,

      "sku_reference_id": "67a1b2c3d4e5f6789012346",

      "createdAt": "2026-02-04T07:40:34.200Z",

      "updatedAt": "2026-02-04T07:40:34.200Z"

    }

  ]

}

```



The first element of `data` is the created product document; other fields may appear depending on what was sent and Mongoose defaults.



#### 400 â Duplicate SKU



```json

{

  "success": false,

  "message": "SKU code \"SKU-EXAMPLE-001\" already exists.",

  "timestamp": "2026-02-04T07:40:34.210Z",

  "data": []

}

```



#### 401 â Missing or invalid API key



```json

{

  "success": false,

  "message": "API key is required"

}

```



or



```json

{

  "success": false,

  "message": "Invalid API key",

  "timestamp": "2026-02-04T07:40:34.210Z"

}

```



#### 500 â Server error



```json

{

  "success": false,

  "message": "Internal server error",

  "timestamp": "2026-02-04T07:40:34.210Z",

  "data": []

}

```



Validation errors (e.g. invalid `tax_slab` or missing required fields) may also surface as **500** with the generic message, depending on Mongoose error handling.



---



## 7. Create order (external)



Creates an order using the same handler as `POST /api/v1/createOrder`; only authentication differs. **No warehouse user JWT is used**, so `created_by` on the order is stored as `null` unless you extend the server to map API keys to users.



**Authentication:** `Content-Type: application/json` and `x-api-key` (same API key pattern as **Create Product API** above). `POST` to `{base_url}/createOrderExternal` (see `{base_url}` under **Create Product API** above).



| Item        | Value |

|------------|--------|

| **Method** | `POST` |

| **Path**   | `/api/v1/createOrderExternal` |



### Required body fields



| Field              | Type   | Description |

|--------------------|--------|-------------|

| `customerName`     | string | Customer name |

| `shippingAddress`  | string **or** object | Delivery address. If object: must include `address` (string). Optional: `dropLatitude`, `dropLongitude` (numbers). |

| `listOfProducts`   | array  | At least one line item (see below). |



### Line item (`listOfProducts[]`)



Each item must identify a product **either** by MongoDB id **or** by SKU:



| Field         | Type   | Required | Description |

|---------------|--------|----------|-------------|

| `product_id`  | string | one of   | MongoDB `_id` of the product |

| `skuCode`     | string | one of   | Product `sku_code` in the catalog |

| `quantity`    | number | yes      | Ordered quantity |

| `ppu`         | number | no       | Price per unit for this line; if omitted, productâs `ppu` is used |



### Optional body fields



| Field                   | Type   | Default / notes |

|-------------------------|--------|------------------|

| `orderId`               | string | If omitted, server generates next id; must not already exist |

| `orderType`             | string | `Direct` |

| `poType`                | string | |

| `orderReceivedDate`     | string | |

| `scheduledDispatchDate` | string | |

| `expectedDeliveryDate`  | string | |

| `customerEmail`         | string | |

| `customerPhone`         | string | |

| `modeOfTransport`       | string | |

| `vehicleNumber`         | string | |

| `paymentStatus`         | string | `unpaid` |

| `paymentMethod`         | string | |

| `platform`              | string | `Custom` |

| `shippingPartner`       | string | |

| `awbNumber`             | string | |

| `orderPriority`         | string | `Standard` |

| `appointmentDate`       | string | |

| `poExpiryDate`          | string | |

| `documentType`          | string | `invoice` |

| `remarks`               | string | |

| `location`              | string | `Gudbelly Warehouse` |

| `latitude`, `longitude` | number | Sets `currentLocation` when both present |

| `currentLocation`       | object | `{ "latitude": number, "longitude": number }` |

| `meta`                  | object | `{}` |



New orders are created with status `pending`.



### Example request



```json

{

  "customerName": "Acme Retail",

  "customerEmail": "orders@acme.example",

  "customerPhone": "+919999999999",

  "shippingAddress": {

    "address": "Plot 12, Industrial Area, Bengaluru 560001",

    "dropLatitude": 12.97,

    "dropLongitude": 77.59

  },

  "listOfProducts": [

    { "skuCode": "GB-SKU-001", "quantity": 10, "ppu": 120.5 },

    { "skuCode": "GB-SKU-002", "quantity": 2 }

  ],

  "platform": "Custom",

  "paymentStatus": "unpaid"

}

```



### Success response



- **Status:** `201`

- **Body:** `{ "success": true, "message": "Order created successfully.", "data": { ...order document } }`



### Error examples



| Status | When |

|--------|------|

| `400`  | Missing `customerName`, invalid/empty `shippingAddress`, empty `listOfProducts`, or duplicate `orderId` |

| `404`  | A line itemâs `skuCode` / `product_id` does not match any product |

| `500`  | Server error |



---



## 8. Create category (external)

> **⚠️ Not yet deployed.** This endpoint is documented for upcoming release but currently returns 404 on production.



Creates a new product category.



**Authentication:** `Content-Type: application/json` and `x-api-key` (same pattern as **Create Product API**). `POST` to `{base_url}/createCategoryExternal` (see `{base_url}` under **Create Product API** above).



| | |

|---|---|

| **Method / path** | `POST /api/v1/createCategoryExternal` |

| **Content-Type** | `application/json` |



### Request body (JSON)



| Field | Type | Required | Description |

|-------|------|----------|-------------|

| `category_name` | string | Yes | Name of the category |

| `category_description` | string | No | Optional description |



### Example request



```json

{

  "category_name": "Pantry - Oils",

  "category_description": "Cooking oils"

}

```



### Success response â HTTP 200



```json

{

  "success": true,

  "message": "SuccessFully created the Category",

  "data": {

    "_id": "...",

    "category_name": "...",

    "category_description": "...",

    "createdAt": "...",

    "updatedAt": "..."

  }

}

```



### Error responses



| Status | When |

|--------|------|

| `404` | Category could not be created (rare). |

| `500` | Server error. |



---



## Pagination



Endpoints that return lists support pagination through the following response structure:



```json

{

  "pagination": {

    "current_page": 1,

    "per_page": 100,

    "total_count": 256,

    "total_pages": 3

  }

}

```



| Field | Description |

|-------|-------------|

| `current_page` | Current page number |

| `per_page` | Number of items per page |

| `total_count` | Total number of items |

| `total_pages` | Total number of pages |



---



## Error Handling



### HTTP Status Codes



| Status Code | Description |

|-------------|-------------|

| 200 | Success |

| 401 | Unauthorized - Invalid or missing API key |

| 404 | Not Found - Resource does not exist |

| 500 | Internal Server Error |



### Error Response Format



```json

{

  "success": false,

  "message": "Error description",

  "timestamp": "2026-02-04T07:40:34.210Z"

}

```



---



## Code Examples



The snippets below share one `base_url`, one API key (`YOUR_API_KEY`), and one HTTP client pattern. They cover the read endpoints **1â4**, **Product availability** (`getProductsAvailability` / `get_products_availability`), **Get live tracking by order** (endpoint **5**, `getOrderLiveTracking` / `get_order_live_tracking`), plus **Create Product**, **Create order**, and **Create category** (`POST` to `createNewProductExternal`, `createOrderExternal`, and `createCategoryExternal`; see sections **6**, **7**, and **8** above).



### JavaScript (Node.js with Axios)



```javascript

const axios = require('axios');



const base_url = 'https://backend.gudbelly.filflo.in/api/v1';



const client = axios.create({

  headers: {

    'Content-Type': 'application/json',

    'x-api-key': 'YOUR_API_KEY'

  }

});



// Get all products

async function getProducts(page = 1, limit = 100) {

  const response = await client.get(`${base_url}/external/products`, {

    params: { page, limit }

  });

  return response.data;

}



// Get product by SKU

async function getProductBySku(sku) {

  const response = await client.get(`${base_url}/external/products/sku/${sku}`);

  return response.data;

}



// Product availability (optional comma-separated sku_code query)

async function getProductsAvailability(skuCodes) {

  const params = skuCodes ? { sku_code: skuCodes } : {};

  const response = await client.get(`${base_url}/external/products/availability`, {

    params

  });

  return response.data;

}



// Get all categories

async function getCategories() {

  const response = await client.get(`${base_url}/external/categories`);

  return response.data;

}



// Get products by category

async function getProductsByCategory(categoryId, page = 1, limit = 100) {

  const response = await client.get(

    `${base_url}/external/categories/${categoryId}/products`,

    { params: { page, limit } }

  );

  return response.data;

}



// Get live tracking by business order id

async function getOrderLiveTracking(orderId) {

  const response = await client.get(

    `${base_url}/external/orders/${encodeURIComponent(orderId)}/live-tracking`

  );

  return response.data;

}



// Create product

async function createProduct(body) {

  const response = await client.post(`${base_url}/createNewProductExternal`, body);

  return response.data;

}



// Create order

async function createOrder(body) {

  const response = await client.post(`${base_url}/createOrderExternal`, body);

  return response.data;

}



// Create category

async function createCategory(body) {

  const response = await client.post(`${base_url}/createCategoryExternal`, body);

  return response.data;

}

```



### Python (with Requests)



```python

import requests

from urllib.parse import quote



base_url = 'https://backend.gudbelly.filflo.in/api/v1'

API_KEY = 'YOUR_API_KEY'



headers = {

    'x-api-key': API_KEY,

    'Content-Type': 'application/json',

}



# Get all products

def get_products(page=1, limit=100):

    response = requests.get(

        f'{base_url}/external/products',

        headers=headers,

        params={'page': page, 'limit': limit}

    )

    return response.json()



# Get product by SKU

def get_product_by_sku(sku):

    response = requests.get(

        f'{base_url}/external/products/sku/{sku}',

        headers=headers

    )

    return response.json()



# Product availability (sku_codes: optional str, e.g. "SKU-1" or "SKU-1,SKU-2")

def get_products_availability(sku_codes=None):

    kwargs = {'headers': headers}

    if sku_codes:

        kwargs['params'] = {'sku_code': sku_codes}

    response = requests.get(

        f'{base_url}/external/products/availability',

        **kwargs,

    )

    return response.json()



# Get all categories

def get_categories():

    response = requests.get(

        f'{base_url}/external/categories',

        headers=headers

    )

    return response.json()



# Get products by category

def get_products_by_category(category_id, page=1, limit=100):

    response = requests.get(

        f'{base_url}/external/categories/{category_id}/products',

        headers=headers,

        params={'page': page, 'limit': limit}

    )

    return response.json()



# Get live tracking by business order id

def get_order_live_tracking(order_id):

    response = requests.get(

        f'{base_url}/external/orders/{quote(order_id, safe="")}/live-tracking',

        headers=headers,

    )

    return response.json()



# Create product

def create_product(body):

    response = requests.post(

        f'{base_url}/createNewProductExternal',

        headers=headers,

        json=body

    )

    return response.json()



# Create order

def create_order(body):

    response = requests.post(

        f'{base_url}/createOrderExternal',

        headers=headers,

        json=body

    )

    return response.json()



# Create category

def create_category(body):

    response = requests.post(

        f'{base_url}/createCategoryExternal',

        headers=headers,

        json=body

    )

    return response.json()

```



### cURL



```bash

# Get all products

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/products"



# Get product by SKU

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/products/sku/AV-OIL-002"



# Product availability (all active products)

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/products/availability"



# Product availability (filter by SKU, comma-separated)

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/products/availability?sku_code=SKU-001,SKU-002"



# Get all categories

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/categories"



# Get products by category

curl -H "x-api-key: YOUR_API_KEY" \

  "https://backend.gudbelly.filflo.in/api/v1/external/categories/692a7ad9e27f04764e471c77/products"



# Get live tracking by order (replace ORD-2025-00042 with business orderId)

curl -sS "https://backend.gudbelly.filflo.in/api/v1/external/orders/ORD-2025-00042/live-tracking" \

  -H "x-api-key: YOUR_API_KEY"



# Create product

curl -X POST \

  -H "Content-Type: application/json" \

  -H "x-api-key: YOUR_API_KEY" \

  -d "{\"product_name\":\"Example item\",\"sku_code\":\"SKU-EXAMPLE-001\",\"hsn_code\":\"7113\",\"tax_slab\":\"TAX-05\",\"ppu\":199}" \

  "https://backend.gudbelly.filflo.in/api/v1/createNewProductExternal"



# Create order

curl -X POST \

  -H "Content-Type: application/json" \

  -H "x-api-key: YOUR_API_KEY" \

  -d "{\"customerName\":\"Acme Retail\",\"shippingAddress\":{\"address\":\"Plot 12, Bengaluru\"},\"listOfProducts\":[{\"skuCode\":\"GB-SKU-001\",\"quantity\":10}]}" \

  "https://backend.gudbelly.filflo.in/api/v1/createOrderExternal"



# Create category

curl -X POST \

  -H "Content-Type: application/json" \

  -H "x-api-key: YOUR_API_KEY" \

  -d "{\"category_name\":\"Pantry - Oils\",\"category_description\":\"Cooking oils\"}" \

  "https://backend.gudbelly.filflo.in/api/v1/createCategoryExternal"

```



---



## Rate Limiting



Please contact GudBelly for information about rate limits applicable to your API key.



---



## Support



For API support or to request an API key, please contact GudBelly at your designated support channel.


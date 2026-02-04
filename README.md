# GudBelly External API Guide

This guide explains how to integrate with the GudBelly External API to access product and category data.

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

### JavaScript (Node.js with Axios)

```javascript
const axios = require('axios');

const client = axios.create({
  baseURL: 'https://backend.gudbelly.filflo.in/api/v1/external',
  headers: {
    'x-api-key': 'YOUR_API_KEY'
  }
});

// Get all products
async function getProducts(page = 1, limit = 100) {
  const response = await client.get('/products', {
    params: { page, limit }
  });
  return response.data;
}

// Get product by SKU
async function getProductBySku(sku) {
  const response = await client.get(`/products/sku/${sku}`);
  return response.data;
}

// Get all categories
async function getCategories() {
  const response = await client.get('/categories');
  return response.data;
}

// Get products by category
async function getProductsByCategory(categoryId, page = 1, limit = 100) {
  const response = await client.get(`/categories/${categoryId}/products`, {
    params: { page, limit }
  });
  return response.data;
}
```

### Python (with Requests)

```python
import requests

BASE_URL = 'https://backend.gudbelly.filflo.in/api/v1/external'
API_KEY = 'YOUR_API_KEY'

headers = {
    'x-api-key': API_KEY
}

# Get all products
def get_products(page=1, limit=100):
    response = requests.get(
        f'{BASE_URL}/products',
        headers=headers,
        params={'page': page, 'limit': limit}
    )
    return response.json()

# Get product by SKU
def get_product_by_sku(sku):
    response = requests.get(
        f'{BASE_URL}/products/sku/{sku}',
        headers=headers
    )
    return response.json()

# Get all categories
def get_categories():
    response = requests.get(
        f'{BASE_URL}/categories',
        headers=headers
    )
    return response.json()

# Get products by category
def get_products_by_category(category_id, page=1, limit=100):
    response = requests.get(
        f'{BASE_URL}/categories/{category_id}/products',
        headers=headers,
        params={'page': page, 'limit': limit}
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

# Get all categories
curl -H "x-api-key: YOUR_API_KEY" \
  "https://backend.gudbelly.filflo.in/api/v1/external/categories"

# Get products by category
curl -H "x-api-key: YOUR_API_KEY" \
  "https://backend.gudbelly.filflo.in/api/v1/external/categories/692a7ad9e27f04764e471c77/products"
```

---

## Rate Limiting

Please contact GudBelly for information about rate limits applicable to your API key.

---

## Support

For API support or to request an API key, please contact GudBelly at your designated support channel.

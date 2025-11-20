---
name: rest-api-designer
description: Design RESTful API endpoints with proper resource modeling, HTTP methods, and URL structure. Use when creating REST APIs, designing endpoints, or structuring API resources.
---

# REST API Designer

Design RESTful APIs with proper resource modeling and endpoint structure.

## Quick Start

Use resource-based URLs with plural nouns, proper HTTP methods, and consistent patterns.

## Instructions

### Core REST Principles

**Resources, not actions:**
```
Good: GET /users, POST /users
Bad: GET /getUsers, POST /createUser
```

**Use HTTP methods correctly:**
- GET: Retrieve (safe, idempotent)
- POST: Create (not idempotent)
- PUT: Replace (idempotent)
- PATCH: Partial update (not idempotent)
- DELETE: Remove (idempotent)

**Use plural nouns:**
```
Good: /products, /orders
Bad: /product, /order
```

### Resource Modeling

**Identify resources:**
1. List main entities (users, products, orders)
2. Identify relationships (user has orders, order has items)
3. Determine hierarchies (posts have comments)

**Design URL structure:**
```
/api/v1/resources
/api/v1/resources/{id}
/api/v1/resources/{id}/sub-resources
```

**Example - Blog API:**
```
GET    /api/v1/posts           # List posts
POST   /api/v1/posts           # Create post
GET    /api/v1/posts/{id}      # Get post
PUT    /api/v1/posts/{id}      # Replace post
PATCH  /api/v1/posts/{id}      # Update post
DELETE /api/v1/posts/{id}      # Delete post

GET    /api/v1/posts/{id}/comments    # List comments
POST   /api/v1/posts/{id}/comments    # Create comment
```

### HTTP Methods

**GET - Retrieve resources:**
```
GET /api/v1/users           # List all users
GET /api/v1/users/{id}      # Get specific user
GET /api/v1/users?role=admin # Filter users
```

Response:
```json
{
  "data": [
    { "id": 1, "name": "John" }
  ],
  "meta": {
    "total": 100,
    "page": 1
  }
}
```

**POST - Create resources:**
```
POST /api/v1/users
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

Response (201 Created):
```json
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "created_at": "2024-01-01T00:00:00Z"
}
```

**PUT - Replace resource:**
```
PUT /api/v1/users/{id}
Content-Type: application/json

{
  "name": "John Smith",
  "email": "john.smith@example.com"
}
```

**PATCH - Partial update:**
```
PATCH /api/v1/users/{id}
Content-Type: application/json

{
  "email": "newemail@example.com"
}
```

**DELETE - Remove resource:**
```
DELETE /api/v1/users/{id}
```

Response (204 No Content or 200 with confirmation)

### Query Parameters

**Filtering:**
```
GET /api/v1/products?category=electronics&price_min=100
```

**Sorting:**
```
GET /api/v1/products?sort=price&order=desc
GET /api/v1/products?sort=-price  # Descending
```

**Pagination:**
```
GET /api/v1/products?page=2&per_page=20
GET /api/v1/products?offset=20&limit=20
```

**Field selection:**
```
GET /api/v1/users?fields=id,name,email
```

**Search:**
```
GET /api/v1/products?q=laptop
```

### Nested Resources

**One level (recommended):**
```
GET /api/v1/users/{id}/orders
POST /api/v1/users/{id}/orders
```

**Avoid deep nesting:**
```
Bad: /api/v1/users/{id}/orders/{id}/items/{id}/reviews
Better: /api/v1/reviews?item_id={id}
```

**Alternative - flat with filters:**
```
GET /api/v1/orders?user_id={id}
GET /api/v1/comments?post_id={id}
```

### Response Format

**Success response:**
```json
{
  "data": {
    "id": 1,
    "name": "Product"
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z"
  }
}
```

**List response:**
```json
{
  "data": [
    { "id": 1, "name": "Item 1" },
    { "id": 2, "name": "Item 2" }
  ],
  "meta": {
    "total": 100,
    "page": 1,
    "per_page": 20
  },
  "links": {
    "self": "/api/v1/items?page=1",
    "next": "/api/v1/items?page=2",
    "prev": null
  }
}
```

**Error response:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

### Status Codes

**Success:**
- 200 OK: Successful GET, PUT, PATCH, DELETE
- 201 Created: Successful POST
- 204 No Content: Successful DELETE with no response body

**Client errors:**
- 400 Bad Request: Invalid input
- 401 Unauthorized: Missing/invalid auth
- 403 Forbidden: Insufficient permissions
- 404 Not Found: Resource doesn't exist
- 409 Conflict: Resource conflict
- 422 Unprocessable Entity: Validation error

**Server errors:**
- 500 Internal Server Error: Server error
- 503 Service Unavailable: Temporary unavailability

### Versioning

**URL versioning (recommended):**
```
/api/v1/users
/api/v2/users
```

**Header versioning:**
```
GET /api/users
Accept: application/vnd.myapi.v1+json
```

**Query parameter:**
```
GET /api/users?version=1
```

### Authentication

**JWT Bearer token:**
```
GET /api/v1/users
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**API Key:**
```
GET /api/v1/users
X-API-Key: your-api-key-here
```

**Basic Auth:**
```
GET /api/v1/users
Authorization: Basic base64(username:password)
```

### Rate Limiting

**Include headers:**
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

**Response when exceeded (429):**
```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests",
    "retry_after": 60
  }
}
```

### HATEOAS (Optional)

Include links to related resources:
```json
{
  "id": 1,
  "name": "Product",
  "links": {
    "self": "/api/v1/products/1",
    "category": "/api/v1/categories/5",
    "reviews": "/api/v1/products/1/reviews"
  }
}
```

## Common Patterns

### Bulk Operations

**Bulk create:**
```
POST /api/v1/users/bulk
Content-Type: application/json

{
  "users": [
    { "name": "User 1" },
    { "name": "User 2" }
  ]
}
```

**Bulk update:**
```
PATCH /api/v1/users/bulk
Content-Type: application/json

{
  "updates": [
    { "id": 1, "status": "active" },
    { "id": 2, "status": "inactive" }
  ]
}
```

### Async Operations

**Long-running operation:**
```
POST /api/v1/reports
Response: 202 Accepted

{
  "job_id": "abc123",
  "status": "processing",
  "status_url": "/api/v1/jobs/abc123"
}
```

**Check status:**
```
GET /api/v1/jobs/abc123

{
  "status": "completed",
  "result_url": "/api/v1/reports/xyz789"
}
```

### File Upload

**Single file:**
```
POST /api/v1/files
Content-Type: multipart/form-data

file: [binary data]
```

**With metadata:**
```
POST /api/v1/documents
Content-Type: multipart/form-data

file: [binary data]
title: "Document Title"
category: "reports"
```

### Webhooks

**Register webhook:**
```
POST /api/v1/webhooks

{
  "url": "https://example.com/webhook",
  "events": ["order.created", "order.updated"]
}
```

### Health Check

```
GET /api/health

{
  "status": "healthy",
  "version": "1.0.0",
  "timestamp": "2024-01-01T00:00:00Z"
}
```

## Example API Design

### E-commerce API

**Products:**
```
GET    /api/v1/products
POST   /api/v1/products
GET    /api/v1/products/{id}
PUT    /api/v1/products/{id}
DELETE /api/v1/products/{id}
GET    /api/v1/products/{id}/reviews
```

**Orders:**
```
GET    /api/v1/orders
POST   /api/v1/orders
GET    /api/v1/orders/{id}
PATCH  /api/v1/orders/{id}
GET    /api/v1/orders/{id}/items
```

**Customers:**
```
GET    /api/v1/customers
POST   /api/v1/customers
GET    /api/v1/customers/{id}
GET    /api/v1/customers/{id}/orders
```

**Cart:**
```
GET    /api/v1/cart
POST   /api/v1/cart/items
DELETE /api/v1/cart/items/{id}
POST   /api/v1/cart/checkout
```

## Best Practices

**Use consistent naming:**
- Lowercase URLs
- Hyphens for multi-word resources: `/order-items`
- Consistent date formats: ISO 8601

**Include metadata:**
- Timestamps (created_at, updated_at)
- Pagination info
- API version

**Validate input:**
- Check required fields
- Validate formats
- Sanitize data

**Handle errors gracefully:**
- Clear error messages
- Appropriate status codes
- Include error codes for client handling

**Document everything:**
- Use OpenAPI/Swagger
- Include examples
- Document rate limits

## Troubleshooting

**Unclear resource boundaries:**
- Focus on nouns, not verbs
- Think about data entities
- Consider client needs

**Deep nesting:**
- Limit to one level
- Use query parameters for filtering
- Create separate endpoints

**Inconsistent responses:**
- Use response templates
- Standardize error format
- Document response structure

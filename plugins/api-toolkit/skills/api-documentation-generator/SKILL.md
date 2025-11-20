---
name: api-documentation-generator
description: Generate OpenAPI/Swagger specifications and API documentation from code or design. Use when creating API docs, generating OpenAPI specs, or documenting REST APIs.
---

# API Documentation Generator

Generate OpenAPI/Swagger specifications and comprehensive API documentation.

## Quick Start

Create OpenAPI 3.0 specs with paths, schemas, and examples for complete API documentation.

## Instructions

### OpenAPI 3.0 Structure

**Basic structure:**
```yaml
openapi: 3.0.0
info:
  title: API Name
  version: 1.0.0
  description: API description
servers:
  - url: https://api.example.com/v1
paths:
  /users:
    get:
      summary: List users
      responses:
        '200':
          description: Success
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
```

### Info Section

```yaml
info:
  title: E-commerce API
  version: 1.0.0
  description: |
    REST API for e-commerce platform.
    
    ## Authentication
    Use Bearer token in Authorization header.
    
    ## Rate Limiting
    1000 requests per hour per API key.
  contact:
    name: API Support
    email: api@example.com
    url: https://example.com/support
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT
```

### Servers

```yaml
servers:
  - url: https://api.example.com/v1
    description: Production
  - url: https://staging-api.example.com/v1
    description: Staging
  - url: http://localhost:3000/v1
    description: Development
```

### Paths and Operations

**GET endpoint:**
```yaml
paths:
  /users:
    get:
      summary: List users
      description: Retrieve a paginated list of users
      tags:
        - Users
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            default: 1
        - name: per_page
          in: query
          schema:
            type: integer
            default: 20
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
                  meta:
                    $ref: '#/components/schemas/PaginationMeta'
```

**POST endpoint:**
```yaml
  /users:
    post:
      summary: Create user
      tags:
        - Users
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
            example:
              name: John Doe
              email: john@example.com
      responses:
        '201':
          description: User created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '400':
          $ref: '#/components/responses/BadRequest'
```

**Path parameters:**
```yaml
  /users/{id}:
    get:
      summary: Get user by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
          description: User ID
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          $ref: '#/components/responses/NotFound'
```

### Components - Schemas

**Simple schema:**
```yaml
components:
  schemas:
    User:
      type: object
      required:
        - id
        - email
      properties:
        id:
          type: integer
          example: 1
        name:
          type: string
          example: John Doe
        email:
          type: string
          format: email
          example: john@example.com
        created_at:
          type: string
          format: date-time
```

**Nested schema:**
```yaml
    Order:
      type: object
      properties:
        id:
          type: integer
        customer:
          $ref: '#/components/schemas/User'
        items:
          type: array
          items:
            $ref: '#/components/schemas/OrderItem'
        total:
          type: number
          format: float
```

**Enum:**
```yaml
    OrderStatus:
      type: string
      enum:
        - pending
        - processing
        - shipped
        - delivered
        - cancelled
```

**OneOf (union types):**
```yaml
    Payment:
      oneOf:
        - $ref: '#/components/schemas/CreditCardPayment'
        - $ref: '#/components/schemas/PayPalPayment'
      discriminator:
        propertyName: payment_type
```

### Components - Responses

**Reusable responses:**
```yaml
components:
  responses:
    NotFound:
      description: Resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            error:
              code: NOT_FOUND
              message: Resource not found
    
    BadRequest:
      description: Invalid request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ValidationError'
    
    Unauthorized:
      description: Authentication required
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
```

### Components - Parameters

**Reusable parameters:**
```yaml
components:
  parameters:
    PageParam:
      name: page
      in: query
      schema:
        type: integer
        default: 1
    
    LimitParam:
      name: limit
      in: query
      schema:
        type: integer
        default: 20
        maximum: 100
    
    IdParam:
      name: id
      in: path
      required: true
      schema:
        type: integer
```

### Security Schemes

**Bearer token:**
```yaml
components:
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - BearerAuth: []
```

**API Key:**
```yaml
components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key
```

**OAuth 2.0:**
```yaml
components:
  securitySchemes:
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://example.com/oauth/authorize
          tokenUrl: https://example.com/oauth/token
          scopes:
            read: Read access
            write: Write access
```

### Examples

**Multiple examples:**
```yaml
responses:
  '200':
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/User'
        examples:
          admin:
            summary: Admin user
            value:
              id: 1
              name: Admin
              role: admin
          regular:
            summary: Regular user
            value:
              id: 2
              name: John
              role: user
```

### Tags

**Organize endpoints:**
```yaml
tags:
  - name: Users
    description: User management
  - name: Products
    description: Product catalog
  - name: Orders
    description: Order processing

paths:
  /users:
    get:
      tags:
        - Users
```

## Complete Example

```yaml
openapi: 3.0.0
info:
  title: Blog API
  version: 1.0.0
  description: RESTful API for blog platform

servers:
  - url: https://api.blog.com/v1

paths:
  /posts:
    get:
      summary: List posts
      tags:
        - Posts
      parameters:
        - $ref: '#/components/parameters/PageParam'
        - $ref: '#/components/parameters/LimitParam'
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Post'
                  meta:
                    $ref: '#/components/schemas/PaginationMeta'
    
    post:
      summary: Create post
      tags:
        - Posts
      security:
        - BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - title
                - content
              properties:
                title:
                  type: string
                content:
                  type: string
                tags:
                  type: array
                  items:
                    type: string
      responses:
        '201':
          description: Post created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Post'

  /posts/{id}:
    get:
      summary: Get post
      tags:
        - Posts
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Post'
        '404':
          $ref: '#/components/responses/NotFound'

components:
  schemas:
    Post:
      type: object
      properties:
        id:
          type: integer
        title:
          type: string
        content:
          type: string
        author:
          $ref: '#/components/schemas/User'
        created_at:
          type: string
          format: date-time
    
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
    
    PaginationMeta:
      type: object
      properties:
        total:
          type: integer
        page:
          type: integer
        per_page:
          type: integer
    
    Error:
      type: object
      properties:
        error:
          type: object
          properties:
            code:
              type: string
            message:
              type: string
  
  parameters:
    PageParam:
      name: page
      in: query
      schema:
        type: integer
        default: 1
    
    LimitParam:
      name: limit
      in: query
      schema:
        type: integer
        default: 20
  
  responses:
    NotFound:
      description: Resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
  
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - BearerAuth: []
```

## Generating from Code

### From Express.js

```javascript
// Use swagger-jsdoc
/**
 * @swagger
 * /users:
 *   get:
 *     summary: List users
 *     responses:
 *       200:
 *         description: Success
 */
app.get('/users', (req, res) => {
  // Handler
});
```

### From FastAPI (Python)

```python
# FastAPI auto-generates OpenAPI
@app.get("/users", response_model=List[User])
async def list_users():
    return users
```

### From ASP.NET Core

```csharp
// Use Swashbuckle
[HttpGet]
[ProducesResponseType(typeof(List<User>), 200)]
public IActionResult GetUsers()
{
    return Ok(users);
}
```

## Tools

**Swagger Editor**: https://editor.swagger.io
**Swagger UI**: Interactive documentation
**Redoc**: Alternative documentation UI
**Postman**: Import OpenAPI for testing

## Best Practices

**Use $ref for reusability:**
- Define schemas once
- Reference in multiple places
- Easier maintenance

**Include examples:**
- Help developers understand
- Enable better testing
- Show expected formats

**Document errors:**
- All possible status codes
- Error response format
- Error codes and meanings

**Version your API:**
- Include version in URL or header
- Document breaking changes
- Maintain old versions

**Keep it updated:**
- Generate from code when possible
- Review regularly
- Update with API changes

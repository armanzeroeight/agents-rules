---
description: Generate OpenAPI 3.0 specification from existing API code or design
allowed-tools: Read, Write, Grep, Bash(find:*)
argument-hint: [source-file-or-directory]
---

# Generate OpenAPI Specification

Generate OpenAPI 3.0 spec from API code or create from scratch.

## Context

- Current directory: !`pwd`
- API files: !`find . -name "*route*" -o -name "*controller*" -o -name "*api*" 2>/dev/null | grep -v node_modules | head -10`

## Your Task

Generate a comprehensive OpenAPI 3.0 specification for the API.

### Arguments

- `$1`: Source file or directory (optional) - Path to API code to analyze

### Steps

1. **Analyze existing API code (if provided):**
   - Read route definitions
   - Identify endpoints and HTTP methods
   - Extract request/response schemas
   - Find authentication patterns

2. **Create OpenAPI spec structure:**

   ```yaml
   openapi: 3.0.0
   info:
     title: API Name
     version: 1.0.0
     description: API description
   
   servers:
     - url: https://api.example.com/v1
       description: Production
   
   paths: {}
   
   components:
     schemas: {}
     responses: {}
     parameters: {}
     securitySchemes: {}
   ```

3. **Define paths from routes:**

   For each endpoint found:
   ```yaml
   paths:
     /users:
       get:
         summary: List users
         tags:
           - Users
         parameters:
           - name: page
             in: query
             schema:
               type: integer
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
                         $ref: '#/components/schemas/User'
       
       post:
         summary: Create user
         requestBody:
           required: true
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/CreateUserRequest'
         responses:
           '201':
             description: User created
   ```

4. **Define schemas:**

   Extract or create data models:
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
       
       CreateUserRequest:
         type: object
         required:
           - email
           - name
         properties:
           name:
             type: string
           email:
             type: string
             format: email
   ```

5. **Add reusable components:**

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
     
     responses:
       NotFound:
         description: Resource not found
         content:
           application/json:
             schema:
               $ref: '#/components/schemas/Error'
       
       BadRequest:
         description: Invalid request
         content:
           application/json:
             schema:
               $ref: '#/components/schemas/Error'
     
     securitySchemes:
       BearerAuth:
         type: http
         scheme: bearer
         bearerFormat: JWT
   ```

6. **Add security if authentication detected:**

   ```yaml
   security:
     - BearerAuth: []
   ```

7. **Add tags for organization:**

   ```yaml
   tags:
     - name: Users
       description: User management
     - name: Products
       description: Product operations
     - name: Orders
       description: Order processing
   ```

8. **Save to openapi.yaml:**

   Write the complete spec to `openapi.yaml` or `openapi.json`

9. **Provide next steps:**
   - View in Swagger Editor: https://editor.swagger.io
   - Generate client SDKs
   - Import into Postman for testing
   - Host with Swagger UI or Redoc

### Examples

**Example 1: Generate from Express routes**
```
/generate-openapi src/routes
```

Analyzes Express route files and generates OpenAPI spec.

**Example 2: Generate from FastAPI**
```
/generate-openapi main.py
```

Extracts OpenAPI from FastAPI application.

**Example 3: Create from scratch**
```
/generate-openapi
```

Creates template OpenAPI spec to fill in manually.

## Notes

- Use OpenAPI 3.0 format (not Swagger 2.0)
- Include examples for all schemas
- Document all response codes
- Add descriptions for clarity
- Use $ref for reusability
- Include authentication schemes
- Add request/response examples
- Document query parameters
- Include error responses
- Use tags to organize endpoints

---
name: schema-designer
description: Design database schemas with proper normalization, relationships, constraints, and indexes. Use when creating database tables, modeling data relationships, or designing database structure.
---

# Schema Designer

Design relational database schemas with proper structure, relationships, and constraints.

## Quick Start

Identify entities, define relationships, normalize to 3NF, add constraints and indexes.

## Instructions

### Schema Design Process

1. **Identify entities** (tables)
2. **Define attributes** (columns)
3. **Establish relationships** (foreign keys)
4. **Apply normalization**
5. **Add constraints**
6. **Create indexes**

### Entity Identification

**Main entities:**
- Core business objects
- Things that need to be stored
- Independent concepts

**Example - E-commerce:**
- Users
- Products
- Orders
- Categories
- Reviews

### Table Definition

**Basic table structure:**
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(100) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Data types (PostgreSQL):**
- `SERIAL`: Auto-incrementing integer
- `INTEGER`: Whole numbers
- `BIGINT`: Large integers
- `VARCHAR(n)`: Variable-length string
- `TEXT`: Unlimited text
- `BOOLEAN`: True/false
- `TIMESTAMP`: Date and time
- `DATE`: Date only
- `JSON/JSONB`: JSON data
- `DECIMAL(p,s)`: Precise decimals

### Relationships

**One-to-Many:**
```sql
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id),
    title VARCHAR(200) NOT NULL,
    content TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Many-to-Many (junction table):**
```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200) NOT NULL
);

CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL
);

CREATE TABLE product_tags (
    product_id INTEGER REFERENCES products(id) ON DELETE CASCADE,
    tag_id INTEGER REFERENCES tags(id) ON DELETE CASCADE,
    PRIMARY KEY (product_id, tag_id)
);
```

**One-to-One:**
```sql
CREATE TABLE user_profiles (
    user_id INTEGER PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
    bio TEXT,
    avatar_url VARCHAR(500),
    phone VARCHAR(20)
);
```

### Normalization

**First Normal Form (1NF):**
- Atomic values (no arrays in cells)
- Each column has unique name
- Order doesn't matter

```sql
-- Bad: Multiple values in one column
CREATE TABLE users (
    id INTEGER,
    phones VARCHAR(200)  -- "555-1234, 555-5678"
);

-- Good: Separate table
CREATE TABLE user_phones (
    user_id INTEGER REFERENCES users(id),
    phone VARCHAR(20)
);
```

**Second Normal Form (2NF):**
- Must be in 1NF
- No partial dependencies

```sql
-- Bad: Order details depend on part of composite key
CREATE TABLE order_items (
    order_id INTEGER,
    product_id INTEGER,
    product_name VARCHAR(200),  -- Depends only on product_id
    quantity INTEGER,
    PRIMARY KEY (order_id, product_id)
);

-- Good: Product name in products table
CREATE TABLE order_items (
    order_id INTEGER,
    product_id INTEGER REFERENCES products(id),
    quantity INTEGER,
    PRIMARY KEY (order_id, product_id)
);
```

**Third Normal Form (3NF):**
- Must be in 2NF
- No transitive dependencies

```sql
-- Bad: City depends on zip_code
CREATE TABLE addresses (
    id INTEGER PRIMARY KEY,
    street VARCHAR(200),
    zip_code VARCHAR(10),
    city VARCHAR(100)  -- Depends on zip_code
);

-- Good: Separate zip_codes table
CREATE TABLE zip_codes (
    code VARCHAR(10) PRIMARY KEY,
    city VARCHAR(100),
    state VARCHAR(2)
);

CREATE TABLE addresses (
    id INTEGER PRIMARY KEY,
    street VARCHAR(200),
    zip_code VARCHAR(10) REFERENCES zip_codes(code)
);
```

### Constraints

**Primary Key:**
```sql
id SERIAL PRIMARY KEY
-- Or composite
PRIMARY KEY (user_id, post_id)
```

**Foreign Key:**
```sql
user_id INTEGER REFERENCES users(id)
-- With cascade
user_id INTEGER REFERENCES users(id) ON DELETE CASCADE
-- With restrict
user_id INTEGER REFERENCES users(id) ON DELETE RESTRICT
```

**Unique:**
```sql
email VARCHAR(255) UNIQUE NOT NULL
-- Or composite unique
UNIQUE (user_id, product_id)
```

**Not Null:**
```sql
name VARCHAR(100) NOT NULL
```

**Check:**
```sql
age INTEGER CHECK (age >= 0 AND age <= 150)
price DECIMAL(10,2) CHECK (price > 0)
status VARCHAR(20) CHECK (status IN ('pending', 'active', 'cancelled'))
```

**Default:**
```sql
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
status VARCHAR(20) DEFAULT 'pending'
is_active BOOLEAN DEFAULT true
```

### Indexes

**Single column:**
```sql
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_posts_created_at ON posts(created_at);
```

**Composite index:**
```sql
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at);
```

**Unique index:**
```sql
CREATE UNIQUE INDEX idx_users_email_unique ON users(email);
```

**Partial index:**
```sql
CREATE INDEX idx_active_users ON users(email) WHERE is_active = true;
```

**When to index:**
- Foreign keys
- Columns in WHERE clauses
- Columns in JOIN conditions
- Columns in ORDER BY
- Columns in GROUP BY

**When not to index:**
- Small tables
- Columns with low cardinality
- Frequently updated columns
- Rarely queried columns

## Complete Example - Blog Platform

```sql
-- Users table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(50) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_username ON users(username);

-- Posts table
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title VARCHAR(200) NOT NULL,
    slug VARCHAR(200) UNIQUE NOT NULL,
    content TEXT NOT NULL,
    status VARCHAR(20) DEFAULT 'draft' CHECK (status IN ('draft', 'published', 'archived')),
    published_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_slug ON posts(slug);
CREATE INDEX idx_posts_status_published ON posts(status, published_at);

-- Comments table
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    post_id INTEGER NOT NULL REFERENCES posts(id) ON DELETE CASCADE,
    user_id INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    parent_id INTEGER REFERENCES comments(id) ON DELETE CASCADE,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_comments_post_id ON comments(post_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);
CREATE INDEX idx_comments_parent_id ON comments(parent_id);

-- Tags table
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) UNIQUE NOT NULL,
    slug VARCHAR(50) UNIQUE NOT NULL
);

-- Post-Tag junction table
CREATE TABLE post_tags (
    post_id INTEGER REFERENCES posts(id) ON DELETE CASCADE,
    tag_id INTEGER REFERENCES tags(id) ON DELETE CASCADE,
    PRIMARY KEY (post_id, tag_id)
);

CREATE INDEX idx_post_tags_tag_id ON post_tags(tag_id);
```

## Common Patterns

### Soft Deletes

```sql
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200),
    deleted_at TIMESTAMP NULL
);

-- Query only non-deleted
SELECT * FROM posts WHERE deleted_at IS NULL;
```

### Audit Trail

```sql
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200),
    created_by INTEGER REFERENCES users(id),
    updated_by INTEGER REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Versioning

```sql
CREATE TABLE document_versions (
    id SERIAL PRIMARY KEY,
    document_id INTEGER REFERENCES documents(id),
    version INTEGER NOT NULL,
    content TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE (document_id, version)
);
```

### Hierarchical Data (Adjacency List)

```sql
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    parent_id INTEGER REFERENCES categories(id)
);
```

### Polymorphic Associations

```sql
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    commentable_type VARCHAR(50),  -- 'Post' or 'Photo'
    commentable_id INTEGER,
    content TEXT
);

CREATE INDEX idx_comments_polymorphic ON comments(commentable_type, commentable_id);
```

## Denormalization Patterns

**Caching counts:**
```sql
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    title VARCHAR(200),
    comment_count INTEGER DEFAULT 0  -- Denormalized
);

-- Update with trigger or application code
```

**Storing computed values:**
```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    subtotal DECIMAL(10,2),
    tax DECIMAL(10,2),
    total DECIMAL(10,2)  -- Denormalized: subtotal + tax
);
```

## Best Practices

**Naming conventions:**
- Tables: plural nouns (`users`, `posts`)
- Columns: snake_case (`created_at`, `user_id`)
- Indexes: `idx_table_column`
- Foreign keys: `fk_table_column`

**Always include:**
- Primary key on every table
- Timestamps (created_at, updated_at)
- Appropriate constraints

**Use appropriate types:**
- VARCHAR for limited strings
- TEXT for unlimited text
- TIMESTAMP for dates with time
- DECIMAL for money

**Index strategically:**
- Foreign keys
- Frequently queried columns
- Don't over-index

## Troubleshooting

**Slow queries:**
- Add indexes on WHERE/JOIN columns
- Check for N+1 queries
- Use EXPLAIN to analyze

**Data integrity issues:**
- Add foreign key constraints
- Use CHECK constraints
- Add NOT NULL where appropriate

**Storage bloat:**
- Review denormalization
- Archive old data
- Use appropriate data types

---
name: query-optimizer
description: Optimize SQL queries for performance with indexing strategies, query rewriting, and execution plan analysis. Use when queries are slow, optimizing database performance, or analyzing query execution.
---

# Query Optimizer

Optimize SQL queries for better performance through indexing, rewriting, and analysis.

## Quick Start

Use EXPLAIN to analyze queries, add indexes on WHERE/JOIN columns, avoid SELECT *, limit results.

## Instructions

### Query Analysis with EXPLAIN

**Basic EXPLAIN:**
```sql
EXPLAIN SELECT * FROM users WHERE email = 'user@example.com';
```

**EXPLAIN ANALYZE (actual execution):**
```sql
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'user@example.com';
```

**Key metrics to check:**
- Seq Scan (bad) vs Index Scan (good)
- Rows: Estimated vs actual
- Cost: Lower is better
- Execution time

### Common Performance Issues

**1. Missing Indexes**

Problem:
```sql
-- Seq Scan on users (cost=0.00..1234.56)
SELECT * FROM users WHERE email = 'user@example.com';
```

Solution:
```sql
CREATE INDEX idx_users_email ON users(email);
-- Now: Index Scan using idx_users_email
```

**2. SELECT ***

Problem:
```sql
SELECT * FROM posts;  -- Fetches all columns
```

Solution:
```sql
SELECT id, title, created_at FROM posts;  -- Only needed columns
```

**3. N+1 Queries**

Problem:
```sql
-- Fetches posts
SELECT * FROM posts;
-- Then for each post:
SELECT * FROM users WHERE id = ?;
```

Solution:
```sql
-- Single query with JOIN
SELECT posts.*, users.name 
FROM posts 
JOIN users ON posts.user_id = users.id;
```

**4. No LIMIT**

Problem:
```sql
SELECT * FROM posts ORDER BY created_at DESC;  -- Returns all rows
```

Solution:
```sql
SELECT * FROM posts ORDER BY created_at DESC LIMIT 20;
```

### Indexing Strategies

**Single column index:**
```sql
CREATE INDEX idx_users_email ON users(email);
```

**Composite index (order matters):**
```sql
-- For: WHERE user_id = ? AND created_at > ?
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at);
```

**Covering index (includes all needed columns):**
```sql
-- For: SELECT id, title FROM posts WHERE user_id = ?
CREATE INDEX idx_posts_user_id_title ON posts(user_id) INCLUDE (title);
```

**Partial index (filtered):**
```sql
CREATE INDEX idx_active_users ON users(email) WHERE is_active = true;
```

**Index on expressions:**
```sql
CREATE INDEX idx_users_lower_email ON users(LOWER(email));
-- For: WHERE LOWER(email) = 'user@example.com'
```

### Query Rewriting

**Use EXISTS instead of IN for large sets:**
```sql
-- Slow
SELECT * FROM users WHERE id IN (SELECT user_id FROM posts);

-- Faster
SELECT * FROM users u WHERE EXISTS (
    SELECT 1 FROM posts p WHERE p.user_id = u.id
);
```

**Use JOIN instead of subquery:**
```sql
-- Slow
SELECT * FROM posts WHERE user_id IN (
    SELECT id FROM users WHERE is_active = true
);

-- Faster
SELECT p.* FROM posts p
JOIN users u ON p.user_id = u.id
WHERE u.is_active = true;
```

**Avoid functions on indexed columns:**
```sql
-- Bad: Can't use index
SELECT * FROM users WHERE YEAR(created_at) = 2024;

-- Good: Can use index
SELECT * FROM users 
WHERE created_at >= '2024-01-01' 
AND created_at < '2025-01-01';
```

**Use UNION ALL instead of UNION:**
```sql
-- Slow: Removes duplicates
SELECT id FROM posts UNION SELECT id FROM drafts;

-- Fast: No duplicate removal
SELECT id FROM posts UNION ALL SELECT id FROM drafts;
```

### JOIN Optimization

**Order matters - filter early:**
```sql
-- Bad: Large intermediate result
SELECT * FROM posts p
JOIN users u ON p.user_id = u.id
WHERE p.created_at > '2024-01-01';

-- Good: Filter first
SELECT * FROM posts p
WHERE p.created_at > '2024-01-01'
JOIN users u ON p.user_id = u.id;
```

**Use appropriate JOIN type:**
```sql
-- INNER JOIN: Only matching rows
SELECT * FROM posts p
INNER JOIN users u ON p.user_id = u.id;

-- LEFT JOIN: All posts, even without user
SELECT * FROM posts p
LEFT JOIN users u ON p.user_id = u.id;
```

**Index JOIN columns:**
```sql
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_users_id ON users(id);  -- Usually PK already indexed
```

### Pagination Optimization

**Offset pagination (slow for large offsets):**
```sql
-- Slow for page 1000
SELECT * FROM posts 
ORDER BY created_at DESC 
LIMIT 20 OFFSET 20000;
```

**Cursor pagination (faster):**
```sql
-- First page
SELECT * FROM posts 
ORDER BY created_at DESC, id DESC 
LIMIT 20;

-- Next page (using last created_at and id)
SELECT * FROM posts 
WHERE (created_at, id) < ('2024-01-01 12:00:00', 12345)
ORDER BY created_at DESC, id DESC 
LIMIT 20;
```

### Aggregation Optimization

**Use indexes for GROUP BY:**
```sql
CREATE INDEX idx_posts_user_id ON posts(user_id);

SELECT user_id, COUNT(*) 
FROM posts 
GROUP BY user_id;
```

**Filter before aggregating:**
```sql
-- Good
SELECT user_id, COUNT(*) 
FROM posts 
WHERE created_at > '2024-01-01'
GROUP BY user_id;
```

**Use HAVING for aggregate filters:**
```sql
SELECT user_id, COUNT(*) as post_count
FROM posts 
GROUP BY user_id
HAVING COUNT(*) > 10;
```

### Subquery Optimization

**Correlated subqueries (slow):**
```sql
-- Bad: Runs subquery for each row
SELECT * FROM users u
WHERE (SELECT COUNT(*) FROM posts WHERE user_id = u.id) > 10;
```

**JOIN instead:**
```sql
-- Good: Single query
SELECT u.* FROM users u
JOIN (
    SELECT user_id, COUNT(*) as post_count
    FROM posts
    GROUP BY user_id
    HAVING COUNT(*) > 10
) p ON u.id = p.user_id;
```

### Caching Strategies

**Materialized views:**
```sql
CREATE MATERIALIZED VIEW user_post_counts AS
SELECT user_id, COUNT(*) as post_count
FROM posts
GROUP BY user_id;

-- Refresh periodically
REFRESH MATERIALIZED VIEW user_post_counts;
```

**Query result caching (application level):**
```python
# Cache expensive queries
@cache(ttl=300)
def get_popular_posts():
    return db.query("SELECT * FROM posts ORDER BY views DESC LIMIT 10")
```

## Common Patterns

### Full-text Search

**PostgreSQL:**
```sql
-- Add tsvector column
ALTER TABLE posts ADD COLUMN search_vector tsvector;

-- Update with trigger
CREATE INDEX idx_posts_search ON posts USING GIN(search_vector);

-- Search
SELECT * FROM posts 
WHERE search_vector @@ to_tsquery('postgresql & optimization');
```

**Use dedicated search engine for complex needs:**
- Elasticsearch
- Algolia
- Meilisearch

### Batch Operations

**Bulk insert:**
```sql
-- Bad: Multiple inserts
INSERT INTO users (name) VALUES ('User 1');
INSERT INTO users (name) VALUES ('User 2');

-- Good: Single insert
INSERT INTO users (name) VALUES 
('User 1'),
('User 2'),
('User 3');
```

**Bulk update:**
```sql
-- Use CASE for conditional updates
UPDATE posts 
SET status = CASE 
    WHEN views > 1000 THEN 'popular'
    WHEN views > 100 THEN 'normal'
    ELSE 'new'
END;
```

### Connection Pooling

```python
# Use connection pool
from sqlalchemy import create_engine

engine = create_engine(
    'postgresql://user:pass@localhost/db',
    pool_size=20,
    max_overflow=10
)
```

## Performance Monitoring

**Check slow queries:**
```sql
-- PostgreSQL: Enable slow query log
ALTER DATABASE mydb SET log_min_duration_statement = 1000;  -- 1 second

-- View pg_stat_statements
SELECT query, calls, total_time, mean_time
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;
```

**Check index usage:**
```sql
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
WHERE idx_scan = 0  -- Unused indexes
ORDER BY pg_relation_size(indexrelid) DESC;
```

**Check table statistics:**
```sql
SELECT 
    schemaname,
    tablename,
    seq_scan,
    seq_tup_read,
    idx_scan,
    idx_tup_fetch
FROM pg_stat_user_tables
ORDER BY seq_scan DESC;
```

## Best Practices

**Always:**
- Use EXPLAIN ANALYZE for slow queries
- Index foreign keys
- Index WHERE/JOIN columns
- Limit result sets
- Use prepared statements

**Avoid:**
- SELECT *
- Functions on indexed columns in WHERE
- Correlated subqueries
- Large OFFSET values
- Over-indexing

**Monitor:**
- Slow query log
- Index usage
- Table statistics
- Connection pool

## Troubleshooting

**Query still slow after indexing:**
- Check if index is being used (EXPLAIN)
- Verify index column order for composite indexes
- Consider covering index
- Check for stale statistics (ANALYZE table)

**Too many indexes:**
- Remove unused indexes
- Combine similar indexes
- Monitor write performance

**High memory usage:**
- Reduce work_mem
- Optimize sort operations
- Use streaming instead of loading all data

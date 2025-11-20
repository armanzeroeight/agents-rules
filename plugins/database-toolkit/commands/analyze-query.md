---
description: Analyze SQL query execution plan and provide optimization recommendations
allowed-tools: Read, Write
argument-hint: [query-file-or-inline-query]
---

# Analyze Query

Analyze SQL query execution plan and suggest optimizations.

## Your Task

Analyze the provided SQL query and provide optimization recommendations.

### Arguments

- `$1`: SQL query (inline) or file path containing query

### Steps

1. **Parse the query:**
   - Identify SELECT, FROM, WHERE, JOIN, ORDER BY, GROUP BY clauses
   - Note any subqueries or CTEs
   - Check for functions on columns

2. **Analyze query structure:**

   **Check for common issues:**
   - SELECT * (fetching unnecessary columns)
   - Missing WHERE clause (full table scan)
   - Functions on indexed columns
   - Correlated subqueries
   - Large OFFSET values
   - No LIMIT clause

3. **Provide EXPLAIN command:**

   ```sql
   EXPLAIN ANALYZE
   [user's query];
   ```

   Instruct user to run this and share results.

4. **Analyze execution plan (if provided):**

   **Look for:**
   - Seq Scan (sequential scan - bad for large tables)
   - Index Scan (good)
   - Nested Loop (can be slow for large datasets)
   - Hash Join (good for large datasets)
   - Sort operations (expensive)
   - High cost values

5. **Recommend indexes:**

   Based on WHERE, JOIN, ORDER BY clauses:
   ```sql
   -- For WHERE user_id = ?
   CREATE INDEX idx_table_user_id ON table_name(user_id);
   
   -- For WHERE user_id = ? AND created_at > ?
   CREATE INDEX idx_table_user_created ON table_name(user_id, created_at);
   
   -- For JOIN
   CREATE INDEX idx_table_foreign_key ON table_name(foreign_key_column);
   ```

6. **Suggest query rewrites:**

   **Replace SELECT *:**
   ```sql
   -- Instead of
   SELECT * FROM users;
   
   -- Use
   SELECT id, name, email FROM users;
   ```

   **Add LIMIT:**
   ```sql
   -- Instead of
   SELECT * FROM posts ORDER BY created_at DESC;
   
   -- Use
   SELECT * FROM posts ORDER BY created_at DESC LIMIT 20;
   ```

   **Optimize subqueries:**
   ```sql
   -- Instead of
   SELECT * FROM users WHERE id IN (SELECT user_id FROM posts);
   
   -- Use
   SELECT DISTINCT u.* FROM users u
   JOIN posts p ON u.id = p.user_id;
   ```

   **Avoid functions on indexed columns:**
   ```sql
   -- Instead of
   WHERE YEAR(created_at) = 2024
   
   -- Use
   WHERE created_at >= '2024-01-01' AND created_at < '2025-01-01'
   ```

7. **Provide optimization summary:**

   **Priority 1 (High Impact):**
   - Missing indexes on WHERE/JOIN columns
   - SELECT * with many columns
   - No LIMIT on large result sets

   **Priority 2 (Medium Impact):**
   - Subquery optimization
   - JOIN order optimization
   - Function usage on indexed columns

   **Priority 3 (Low Impact):**
   - Minor query rewrites
   - Pagination improvements

8. **Provide optimized query:**

   Show the rewritten query with all optimizations applied.

### Examples

**Example 1: Analyze inline query**
```
/analyze-query "SELECT * FROM users WHERE email = 'user@example.com'"
```

**Example 2: Analyze from file**
```
/analyze-query queries/slow-query.sql
```

**Example 3: Complex query**
```
/analyze-query "SELECT u.*, COUNT(p.id) FROM users u LEFT JOIN posts p ON u.id = p.user_id GROUP BY u.id"
```

## Notes

- Always recommend running EXPLAIN ANALYZE
- Prioritize high-impact optimizations
- Consider table size in recommendations
- Suggest indexes but warn about write performance impact
- Provide before/after query examples
- Include estimated performance improvement
- Check for N+1 query patterns
- Recommend connection pooling if many queries

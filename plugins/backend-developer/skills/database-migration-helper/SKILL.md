---
name: database-migration-helper
description: Create and manage database migrations safely with rollback support. Use when modifying database schema, adding indexes, or managing database changes.
---

# Database Migration Helper

Create and manage database migrations safely with proper rollback support.

## Quick Start

Create migration files with up/down functions, test locally, backup before production, run migrations incrementally.

## Instructions

### Migration Structure

**Basic migration:**
```javascript
// migrations/001_create_users_table.js
exports.up = async (db) => {
  await db.schema.createTable('users', (table) => {
    table.increments('id').primary();
    table.string('email').unique().notNullable();
    table.string('password_hash').notNullable();
    table.timestamps(true, true);
  });
};

exports.down = async (db) => {
  await db.schema.dropTable('users');
};
```

### Creating Tables

**With Knex:**
```javascript
exports.up = async (knex) => {
  await knex.schema.createTable('posts', (table) => {
    table.increments('id').primary();
    table.integer('user_id').unsigned().notNullable();
    table.string('title', 200).notNullable();
    table.text('content');
    table.enum('status', ['draft', 'published', 'archived']).defaultTo('draft');
    table.timestamps(true, true);
    
    // Foreign key
    table.foreign('user_id').references('users.id').onDelete('CASCADE');
    
    // Indexes
    table.index('user_id');
    table.index('status');
  });
};

exports.down = async (knex) => {
  await knex.schema.dropTable('posts');
};
```

**With raw SQL:**
```sql
-- migrations/001_create_users.up.sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);

-- migrations/001_create_users.down.sql
DROP TABLE IF EXISTS users;
```

### Adding Columns

```javascript
exports.up = async (knex) => {
  await knex.schema.table('users', (table) => {
    table.string('phone', 20);
    table.boolean('is_verified').defaultTo(false);
  });
};

exports.down = async (knex) => {
  await knex.schema.table('users', (table) => {
    table.dropColumn('phone');
    table.dropColumn('is_verified');
  });
};
```

### Modifying Columns

```javascript
exports.up = async (knex) => {
  await knex.schema.alterTable('users', (table) => {
    table.string('email', 320).alter(); // Increase length
    table.string('name', 100).notNullable().alter(); // Add NOT NULL
  });
};

exports.down = async (knex) => {
  await knex.schema.alterTable('users', (table) => {
    table.string('email', 255).alter();
    table.string('name', 100).nullable().alter();
  });
};
```

### Adding Indexes

```javascript
exports.up = async (knex) => {
  await knex.schema.table('posts', (table) => {
    table.index('created_at');
    table.index(['user_id', 'created_at']); // Composite index
  });
};

exports.down = async (knex) => {
  await knex.schema.table('posts', (table) => {
    table.dropIndex('created_at');
    table.dropIndex(['user_id', 'created_at']);
  });
};
```

### Data Migrations

```javascript
exports.up = async (knex) => {
  // Add column
  await knex.schema.table('users', (table) => {
    table.string('full_name');
  });
  
  // Migrate data
  const users = await knex('users').select('id', 'first_name', 'last_name');
  for (const user of users) {
    await knex('users')
      .where('id', user.id)
      .update({ full_name: `${user.first_name} ${user.last_name}` });
  }
  
  // Drop old columns
  await knex.schema.table('users', (table) => {
    table.dropColumn('first_name');
    table.dropColumn('last_name');
  });
};

exports.down = async (knex) => {
  // Add old columns
  await knex.schema.table('users', (table) => {
    table.string('first_name');
    table.string('last_name');
  });
  
  // Migrate data back
  const users = await knex('users').select('id', 'full_name');
  for (const user of users) {
    const [firstName, ...lastNameParts] = user.full_name.split(' ');
    await knex('users')
      .where('id', user.id)
      .update({
        first_name: firstName,
        last_name: lastNameParts.join(' ')
      });
  }
  
  // Drop new column
  await knex.schema.table('users', (table) => {
    table.dropColumn('full_name');
  });
};
```

### Foreign Keys

```javascript
exports.up = async (knex) => {
  await knex.schema.table('posts', (table) => {
    table.foreign('user_id')
      .references('id')
      .inTable('users')
      .onDelete('CASCADE')
      .onUpdate('CASCADE');
  });
};

exports.down = async (knex) => {
  await knex.schema.table('posts', (table) => {
    table.dropForeign('user_id');
  });
};
```

### Renaming Tables/Columns

```javascript
exports.up = async (knex) => {
  await knex.schema.renameTable('posts', 'articles');
  
  await knex.schema.table('articles', (table) => {
    table.renameColumn('content', 'body');
  });
};

exports.down = async (knex) => {
  await knex.schema.table('articles', (table) => {
    table.renameColumn('body', 'content');
  });
  
  await knex.schema.renameTable('articles', 'posts');
};
```

### Migration Tools

**Knex.js:**
```bash
# Create migration
npx knex migrate:make create_users_table

# Run migrations
npx knex migrate:latest

# Rollback last batch
npx knex migrate:rollback

# Rollback all
npx knex migrate:rollback --all

# Check status
npx knex migrate:status
```

**TypeORM:**
```bash
# Generate migration
npm run typeorm migration:generate -- -n CreateUsersTable

# Run migrations
npm run typeorm migration:run

# Revert last migration
npm run typeorm migration:revert
```

**Prisma:**
```bash
# Create migration
npx prisma migrate dev --name create_users_table

# Apply migrations
npx prisma migrate deploy

# Reset database
npx prisma migrate reset
```

**Sequelize:**
```bash
# Create migration
npx sequelize-cli migration:generate --name create-users-table

# Run migrations
npx sequelize-cli db:migrate

# Undo last migration
npx sequelize-cli db:migrate:undo
```

### Best Practices

**1. Always include rollback:**
```javascript
// Every migration must have down()
exports.down = async (knex) => {
  // Reverse the changes
};
```

**2. Make migrations idempotent:**
```javascript
exports.up = async (knex) => {
  const exists = await knex.schema.hasTable('users');
  if (!exists) {
    await knex.schema.createTable('users', (table) => {
      // ...
    });
  }
};
```

**3. Test migrations:**
```bash
# Run migration
npm run migrate

# Test application
npm test

# Rollback
npm run migrate:rollback

# Run again
npm run migrate
```

**4. Backup before production:**
```bash
# PostgreSQL
pg_dump dbname > backup.sql

# MySQL
mysqldump dbname > backup.sql

# Then run migration
npm run migrate
```

**5. Run migrations incrementally:**
```bash
# Don't run all at once in production
# Run one migration at a time
npx knex migrate:up 001_create_users_table.js
# Verify
# Then next migration
npx knex migrate:up 002_create_posts_table.js
```

### Common Patterns

**Add column with default:**
```javascript
exports.up = async (knex) => {
  await knex.schema.table('users', (table) => {
    table.boolean('is_active').defaultTo(true);
  });
};
```

**Add enum column:**
```javascript
exports.up = async (knex) => {
  await knex.schema.table('posts', (table) => {
    table.enum('status', ['draft', 'published', 'archived'])
      .defaultTo('draft');
  });
};
```

**Add timestamp columns:**
```javascript
exports.up = async (knex) => {
  await knex.schema.table('posts', (table) => {
    table.timestamps(true, true); // created_at, updated_at
  });
};
```

**Add JSON column:**
```javascript
exports.up = async (knex) => {
  await knex.schema.table('users', (table) => {
    table.json('metadata');
  });
};
```

### Handling Large Tables

**Add index without locking (PostgreSQL):**
```sql
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
```

**Add column with default (PostgreSQL 11+):**
```sql
-- Fast: doesn't rewrite table
ALTER TABLE users ADD COLUMN is_active BOOLEAN DEFAULT true;
```

**Batch data migration:**
```javascript
exports.up = async (knex) => {
  const batchSize = 1000;
  let offset = 0;
  
  while (true) {
    const users = await knex('users')
      .select('id', 'email')
      .limit(batchSize)
      .offset(offset);
    
    if (users.length === 0) break;
    
    for (const user of users) {
      await knex('users')
        .where('id', user.id)
        .update({ email_lower: user.email.toLowerCase() });
    }
    
    offset += batchSize;
  }
};
```

### Migration Checklist

**Before creating:**
- [ ] Understand the change needed
- [ ] Plan rollback strategy
- [ ] Consider data migration
- [ ] Check for dependencies

**In migration:**
- [ ] Include up and down functions
- [ ] Add appropriate indexes
- [ ] Set constraints
- [ ] Handle existing data

**Before running:**
- [ ] Test locally
- [ ] Test rollback
- [ ] Backup database
- [ ] Plan maintenance window

**After running:**
- [ ] Verify changes
- [ ] Test application
- [ ] Monitor performance
- [ ] Document changes

## Troubleshooting

**Migration fails:**
- Check error message
- Verify database connection
- Check for syntax errors
- Ensure dependencies exist

**Can't rollback:**
- Check down() function
- Verify rollback logic
- May need manual intervention
- Restore from backup if needed

**Performance issues:**
- Add indexes after data load
- Use CONCURRENTLY for indexes
- Batch large data migrations
- Run during low traffic

---
description: Create a new database migration file with up and down functions
allowed-tools: Write, Read, Bash(ls:*)
argument-hint: [migration-name]
---

# Create Migration

Create a new database migration file with proper structure.

## Your Task

Generate a migration file for database schema changes.

### Arguments

- `$1`: Migration name (required) - descriptive name like "create_users_table" or "add_email_to_users"

### Steps

1. **Determine migration tool:**
   - Check for Knex (knexfile.js)
   - Check for TypeORM (ormconfig.json)
   - Check for Prisma (schema.prisma)
   - Check for Sequelize (.sequelizerc)
   - Default to Knex if none found

2. **Generate timestamp:**
   ```javascript
   const timestamp = Date.now();
   const filename = `${timestamp}_${migrationName}.js`;
   ```

3. **Create migration file:**

   **For Knex:**
   ```javascript
   // migrations/20240101120000_create_users_table.js
   
   exports.up = async function(knex) {
     await knex.schema.createTable('users', (table) => {
       table.increments('id').primary();
       table.string('email').unique().notNullable();
       table.string('password_hash').notNullable();
       table.timestamps(true, true);
     });
   };
   
   exports.down = async function(knex) {
     await knex.schema.dropTable('users');
   };
   ```

   **For adding columns:**
   ```javascript
   exports.up = async function(knex) {
     await knex.schema.table('users', (table) => {
       table.string('phone', 20);
       table.boolean('is_verified').defaultTo(false);
     });
   };
   
   exports.down = async function(knex) {
     await knex.schema.table('users', (table) => {
       table.dropColumn('phone');
       table.dropColumn('is_verified');
     });
   };
   ```

   **For adding indexes:**
   ```javascript
   exports.up = async function(knex) {
     await knex.schema.table('posts', (table) => {
       table.index('user_id');
       table.index(['user_id', 'created_at']);
     });
   };
   
   exports.down = async function(knex) {
     await knex.schema.table('posts', (table) => {
       table.dropIndex('user_id');
       table.dropIndex(['user_id', 'created_at']);
     });
   };
   ```

   **For TypeORM:**
   ```typescript
   import { MigrationInterface, QueryRunner, Table } from "typeorm";
   
   export class CreateUsersTable1640000000000 implements MigrationInterface {
     public async up(queryRunner: QueryRunner): Promise<void> {
       await queryRunner.createTable(
         new Table({
           name: "users",
           columns: [
             {
               name: "id",
               type: "int",
               isPrimary: true,
               isGenerated: true,
               generationStrategy: "increment",
             },
             {
               name: "email",
               type: "varchar",
               isUnique: true,
             },
             {
               name: "password_hash",
               type: "varchar",
             },
           ],
         }),
         true
       );
     }
   
     public async down(queryRunner: QueryRunner): Promise<void> {
       await queryRunner.dropTable("users");
     }
   }
   ```

   **For Prisma:**
   ```sql
   -- migrations/20240101120000_create_users_table/migration.sql
   CREATE TABLE "users" (
       "id" SERIAL PRIMARY KEY,
       "email" VARCHAR(255) UNIQUE NOT NULL,
       "password_hash" VARCHAR(255) NOT NULL,
       "created_at" TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       "updated_at" TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );
   
   CREATE INDEX "idx_users_email" ON "users"("email");
   ```

4. **Provide usage instructions:**

   **Knex:**
   ```bash
   # Run migration
   npx knex migrate:latest
   
   # Rollback
   npx knex migrate:rollback
   
   # Check status
   npx knex migrate:status
   ```

   **TypeORM:**
   ```bash
   # Run migration
   npm run typeorm migration:run
   
   # Revert
   npm run typeorm migration:revert
   ```

   **Prisma:**
   ```bash
   # Apply migration
   npx prisma migrate dev
   
   # Deploy to production
   npx prisma migrate deploy
   ```

5. **Provide testing checklist:**
   - [ ] Test migration locally
   - [ ] Test rollback
   - [ ] Verify data integrity
   - [ ] Check application still works
   - [ ] Backup production database
   - [ ] Run in staging first

### Examples

**Example 1: Create table**
```
/create-migration create_users_table
```

**Example 2: Add column**
```
/create-migration add_phone_to_users
```

**Example 3: Add index**
```
/create-migration add_index_to_posts_user_id
```

**Example 4: Modify column**
```
/create-migration increase_email_length
```

## Notes

- Always include rollback (down function)
- Test migrations before production
- Backup database before running
- Use descriptive migration names
- Add indexes for foreign keys
- Consider performance on large tables
- Use transactions where possible
- Document complex migrations

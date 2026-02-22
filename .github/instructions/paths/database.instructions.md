# Path Instructions: migrations/ (Database Migrations)

This file provides specific guidance for writing and managing database migrations.

## Migration Organization

**Directory Structure**:
```
migrations/
├── 2026-02-23-0001-create-users-table.js
├── 2026-02-23-0002-add-email-index.js
├── 2026-02-24-0003-create-posts-table.js
└── README.md
```

**File Naming**: `YYYY-MM-DD-NNNN-description.js`
- YYYY-MM-DD = Date created
- NNNN = Sequential number (0001, 0002, etc.)
- description = What the migration does (kebab-case)

## Migration Template

### Using Knex (Node.js)

```javascript
/**
 * Migration: Create users table
 * 
 * Creates the core users table with authentication fields.
 * Backward compatible: Safe to rollback.
 */

exports.up = async (knex) => {
  return knex.schema.createTable('users', (table) => {
    table.uuid('id').primary().defaultTo(knex.raw('gen_random_uuid()'));
    table.string('email', 255).unique().notNullable();
    table.string('password_hash', 255).notNullable();
    table.string('name', 255).notNullable();
    table.boolean('is_active').defaultTo(true);
    
    // Timestamps
    table.timestamp('created_at').defaultTo(knex.fn.now());
    table.timestamp('updated_at').defaultTo(knex.fn.now());

    // Indexes
    table.index('email');
    table.index('created_at');
  });
};

exports.down = async (knex) => {
  return knex.schema.dropTable('users');
};
```

### Using Alembic (Python/SQLAlchemy)

```python
"""Create users table

Revision ID: 0001
Revises: 
Create Date: 2026-02-23 10:00:00.000000

"""
from alembic import op
import sqlalchemy as sa
from sqlalchemy.dialects import postgresql

revision = '0001'
down_revision = None
branch_labels = None
depends_on = None


def upgrade() -> None:
    op.create_table(
        'users',
        sa.Column('id', postgresql.UUID(as_uuid=True), 
                  server_default=sa.text('gen_random_uuid()'), 
                  nullable=False),
        sa.Column('email', sa.String(255), nullable=False, unique=True),
        sa.Column('password_hash', sa.String(255), nullable=False),
        sa.Column('name', sa.String(255), nullable=False),
        sa.Column('is_active', sa.Boolean(), server_default='true'),
        sa.Column('created_at', sa.DateTime(), 
                  server_default=sa.func.now()),
        sa.Column('updated_at', sa.DateTime(), 
                  server_default=sa.func.now()),
        sa.PrimaryKeyConstraint('id'),
        sa.UniqueConstraint('email'),
    )
    op.create_index('ix_users_email', 'users', ['email'])
    op.create_index('ix_users_created_at', 'users', ['created_at'])


def downgrade() -> None:
    op.drop_index('ix_users_created_at', 'users')
    op.drop_index('ix_users_email', 'users')
    op.drop_table('users')
```

## Migration Rules (Non-Negotiable)

### Rule 1: Idempotent (Safe to Run Multiple Times)

```javascript
// ✅ Good - idempotent
exports.up = async (knex) => {
  const exists = await knex.schema.hasTable('users');
  if (!exists) {
    return knex.schema.createTable('users', ...);
  }
};

// ❌ Bad - fails if run twice
exports.up = async (knex) => {
  return knex.schema.createTable('users', ...); // Fails on second run
};
```

### Rule 2: Reversible (Rollback Must Work)

```javascript
// ✅ Good - can rollback
exports.up = async (knex) => {
  return knex.schema.createTable('users', ...);
};

exports.down = async (knex) => {
  return knex.schema.dropTable('users');
};

// ❌ Bad - can't rollback (loses data without recovery plan)
exports.up = async (knex) => {
  return knex.schema.createTable('users', ...);
};

exports.down = async (knex) => {
  // No rollback? Data loss!
};
```

### Rule 3: Tested (Forward & Backward)

Before committing:

```bash
# Test forward
npm run migrate:up

# Test backward
npm run migrate:down

# Test forward again
npm run migrate:up
```

### Rule 4: No Data Loss Without Plan

```javascript
// ⚠️ Dangerous - data loss
exports.up = async (knex) => {
  return knex.schema.dropColumn('users', 'email');
};

// ✅ Safe - can recover
exports.up = async (knex) => {
  // Step 1: Create backup column
  await knex.schema.table('users', (table) => {
    table.string('email_backup', 255);
  });
  
  // Step 2: Copy data to backup
  await knex('users').update({
    email_backup: knex.raw('email'),
  });
  
  // Step 3: Drop original (can restore from backup if needed)
  return knex.schema.table('users', (table) => {
    table.dropColumn('email');
  });
};

exports.down = async (knex) => {
  // Step 1: Restore from backup
  await knex.schema.table('users', (table) => {
    table.string('email', 255);
  });
  
  await knex('users').update({
    email: knex.raw('email_backup'),
  });
  
  // Step 2: Drop backup column
  return knex.schema.table('users', (table) => {
    table.dropColumn('email_backup');
  });
};
```

## Common Migration Patterns

### Adding a Column

```javascript
exports.up = async (knex) => {
  return knex.schema.table('users', (table) => {
    table.string('phone', 20).nullable();
  });
};

exports.down = async (knex) => {
  return knex.schema.table('users', (table) => {
    table.dropColumn('phone');
  });
};
```

### Adding an Index

```javascript
exports.up = async (knex) => {
  return knex.schema.table('users', (table) => {
    table.index('email');
  });
};

exports.down = async (knex) => {
  return knex.schema.table('users', (table) => {
    table.dropIndex('email');
  });
};
```

### Creating a Foreign Key

```javascript
exports.up = async (knex) => {
  return knex.schema.table('posts', (table) => {
    table.uuid('user_id').notNullable();
    table.foreign('user_id').references('users.id').onDelete('CASCADE');
  });
};

exports.down = async (knex) => {
  return knex.schema.table('posts', (table) => {
    table.dropForeign(['user_id']);
    table.dropColumn('user_id');
  });
};
```

### Data Transformation

```javascript
exports.up = async (knex) => {
  // Step 1: Add new column
  await knex.schema.table('users', (table) => {
    table.string('full_name', 255).nullable();
  });

  // Step 2: Transform data
  await knex('users').update({
    full_name: knex.raw("CONCAT(first_name, ' ', last_name)"),
  });

  // Step 3: Drop old columns
  return knex.schema.table('users', (table) => {
    table.dropColumn('first_name');
    table.dropColumn('last_name');
  });
};

exports.down = async (knex) => {
  // Reverse the transformation
  await knex.schema.table('users', (table) => {
    table.string('first_name', 255).nullable();
    table.string('last_name', 255).nullable();
  });

  await knex('users').update({
    first_name: knex.raw("SUBSTRING_INDEX(full_name, ' ', 1)"),
    last_name: knex.raw("SUBSTRING_INDEX(full_name, ' ', -1)"),
  });

  return knex.schema.table('users', (table) => {
    table.dropColumn('full_name');
  });
};
```

## Migration Naming Conventions

**Good**:
- `create-users-table`
- `add-email-to-users`
- `create-posts-table-with-fk`
- `add-unique-constraint-email`

**Bad**:
- `update` (too vague)
- `add_stuff` (what stuff?)
- `migration_1` (not descriptive)
- `ADD_EMAIL_AND_PHONE_AND_CITY` (too many changes)

## Testing Migrations

### Manual Testing

```bash
# Run all pending migrations
npm run migrate:up

# Rollback one migration
npm run migrate:down

# Rollback and re-run (test reversibility)
npm run migrate:down && npm run migrate:up
```

### Migration Tests

```javascript
describe('Migrations', () => {
  describe('create users table', () => {
    it('should create users table with correct columns', async () => {
      const exists = await knex.schema.hasTable('users');
      expect(exists).toBe(true);

      const columns = await knex('users').columnInfo();
      expect(columns).toHaveProperty('id');
      expect(columns).toHaveProperty('email');
    });

    it('should be reversible', async () => {
      await knex.migrate.down();
      const exists = await knex.schema.hasTable('users');
      expect(exists).toBe(false);

      await knex.migrate.up();
      const existsAfter = await knex.schema.hasTable('users');
      expect(existsAfter).toBe(true);
    });
  });
});
```

## Anti-Patterns to Avoid

❌ **DON'T**:
- Multiple changes in one migration (one concern per migration)
- Hardcoded data without configuration
- Skipping the down migration
- Testing migrations only on production
- Migrations that can't be tested in reverse
- Data loss without documentation

✅ **DO**:
- One logical change per migration
- Use environment variables for config
- Test both up and down
- Test migrations on staging first
- Write reversible migrations
- Document why if necessary

## Migration Commands

```bash
# Create new migration
npm run migrate:create -- create-users-table

# Run all pending
npm run migrate:up

# Rollback one
npm run migrate:down

# List completed migrations
npm run migrate:status

# Rollback and re-run (development)
npm run migrate:refresh
```

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026

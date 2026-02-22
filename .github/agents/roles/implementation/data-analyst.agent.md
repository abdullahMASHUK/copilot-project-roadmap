# Data Analyst Agent

**Role:** Database schema and data model specialist  
**Phase:** Implementation (1-2 days)  
**Sequence:** After Architect, parallel with Backend API Specialist & Frontend Specialist  
**When to Use:** Whenever data storage, queries, or migrations are needed  
**When to Skip:** Stateless services or frontend-only features

---

## Agent Profile

| Property | Value |
|----------|-------|
| **Title** | Data Analyst Agent |
| **Specialty** | Database schema, migrations, query optimization, data modeling |
| **Phase** | Implementation (days 6-8 of 14-16 total) |
| **Timeline** | 1-2 days per feature |
| **Input From** | Architect (data model guide) |
| **Output To** | Backend API Specialist (queries to implement) |
| **Key Skills** | SQL, database design, migrations, indexing, query optimization |
| **When Needed** | All features with persistent data storage |
| **When Optional** | Temporary/cache data only, no permanent storage |
| **Failure Impact** | High - poor schema causes performance issues |
| **Success Metric** | Migrations run cleanly, queries perform well, data integrity maintained |

---

## Responsibilities

### 1. Design Database Schema
- Create entity relationship diagrams
- Design tables with proper column types
- Define primary and foreign keys
- Plan relationships and constraints
- Ensure data integrity

### 2. Create Database Migrations
- Write migration scripts (SQL or ORM)
- Plan backward compatibility
- Include rollback scripts
- Test migrations on fresh database
- Document migration sequence

### 3. Optimize Query Performance
- Create indexes for common queries
- Analyze query execution plans
- Identify N+1 query problems
- Plan pagination for large datasets
- Monitor long-running queries

### 4. Plan Data Integrity
- Define constraints (unique, not null, check)
- Plan for data consistency
- Handle cascading deletes safely
- Plan archival strategy
- Document data retention policies

### 5. Plan Scalability
- Design for growth (sharding if needed)
- Plan for large result sets
- Design for concurrent access
- Plan for backup/recovery
- Document capacity limits

### 6. Create Query Helpers
- Write reusable query functions
- Create database helpers/utilities
- Document common queries
- Provide examples for Backend Specialist
- Create query templates

### 7. Write Database Tests
- Test migrations run cleanly
- Test query accuracy
- Test performance (slow query detection)
- Test data constraints
- Test edge cases

---

## Input Format

Receives output from Architect:

### From Architect (Data Model Guide):

```markdown
# Payment Data Model

## Entities
- Users (already exist)
- Transactions (new)
  - ID, amount, currency, status, timestamp
- AuditLog (new)
  - Transaction reference, action, timestamp

## Relationships
- User (1) ← (M) Transactions
- Transaction (1) ← (M) AuditLog

## Queries Needed
1. Get user's transactions (paginated)
2. Get transaction by ID
3. Update transaction status
4. Find transaction by Stripe ID (for webhooks)
5. Get audit trail for transaction
6. Get daily/monthly transaction summaries

## Constraints
- Amount must be positive
- Status from enum (pending, completed, failed, refunded)
- One transaction per Stripe charge ID
- Audit log immutable (no updates)
```

---

## Output Format

### 1. Entity Relationship Diagram

```
Users
├─ id (UUID, PK)
├─ email (string, unique)
├─ name (string)
└─ createdAt (timestamp)
    │
    └─ (1:M) Transactions
        ├─ id (UUID, PK)
        ├─ userId (FK → Users.id)
        ├─ amount (decimal)
        ├─ currency (enum)
        ├─ status (enum)
        ├─ stripeTransactionId (string, unique)
        ├─ createdAt (timestamp)
        ├─ completedAt (timestamp, nullable)
        └─ (1:M) AuditLog
            ├─ id (UUID, PK)
            ├─ transactionId (FK → Transactions.id)
            ├─ action (enum)
            └─ timestamp (timestamp)
```

### 2. Schema Definition (SQL)

```sql
-- User transactions table
CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  userId UUID NOT NULL,
  amount NUMERIC(10, 2) NOT NULL,
  currency VARCHAR(3) NOT NULL DEFAULT 'USD',
  status VARCHAR(20) NOT NULL DEFAULT 'pending',
  
  -- External references
  stripeTransactionId VARCHAR(100),
  stripePaymentMethodId VARCHAR(100),
  
  -- Timestamps
  createdAt TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  completedAt TIMESTAMP,
  refundedAt TIMESTAMP,
  
  -- Refund tracking
  refundAmount NUMERIC(10, 2),
  
  -- Metadata
  description TEXT,
  
  -- Constraints
  FOREIGN KEY (userId) REFERENCES users(id) ON DELETE CASCADE,
  UNIQUE(stripeTransactionId),
  CHECK (amount > 0),
  CHECK (status IN ('pending', 'completed', 'failed', 'refunded')),
  CHECK (currency IN ('USD', 'EUR', 'GBP', 'CAD', 'AUD'))
);

-- Audit log table (immutable)
CREATE TABLE audit_log (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  transactionId UUID NOT NULL,
  action VARCHAR(50) NOT NULL,
  details JSONB,
  timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  FOREIGN KEY (transactionId) REFERENCES transactions(id) ON DELETE CASCADE
);

-- Summary table (materialized view for reporting)
CREATE MATERIALIZED VIEW transaction_summary AS
SELECT
  DATE(createdAt) as date,
  COUNT(*) as transaction_count,
  SUM(CASE WHEN status = 'completed' THEN amount ELSE 0 END) as total_completed,
  COUNT(CASE WHEN status = 'completed' THEN 1 END) as completed_count,
  COUNT(CASE WHEN status = 'failed' THEN 1 END) as failed_count,
  AVG(CASE WHEN status = 'completed' THEN amount ELSE NULL END) as avg_amount
FROM transactions
GROUP BY DATE(createdAt);
```

### 3. Migration Scripts

```sql
-- migration_001_create_transactions.sql
BEGIN;

-- Create transactions table
CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  userId UUID NOT NULL,
  amount NUMERIC(10, 2) NOT NULL,
  currency VARCHAR(3) NOT NULL DEFAULT 'USD',
  status VARCHAR(20) NOT NULL DEFAULT 'pending',
  stripeTransactionId VARCHAR(100),
  stripePaymentMethodId VARCHAR(100),
  createdAt TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  completedAt TIMESTAMP,
  refundedAt TIMESTAMP,
  refundAmount NUMERIC(10, 2),
  description TEXT,
  
  FOREIGN KEY (userId) REFERENCES users(id) ON DELETE CASCADE,
  UNIQUE(stripeTransactionId),
  CHECK (amount > 0),
  CHECK (status IN ('pending', 'completed', 'failed', 'refunded')),
  CHECK (currency IN ('USD', 'EUR', 'GBP', 'CAD', 'AUD'))
);

-- Create indexes for common queries
CREATE INDEX idx_transactions_userId_createdAt ON transactions(userId, createdAt DESC);
CREATE INDEX idx_transactions_stripeTransactionId ON transactions(stripeTransactionId);
CREATE INDEX idx_transactions_status ON transactions(status);
CREATE INDEX idx_transactions_createdAt ON transactions(createdAt DESC);

-- Create audit log table
CREATE TABLE audit_log (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  transactionId UUID NOT NULL,
  action VARCHAR(50) NOT NULL,
  details JSONB,
  timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  
  FOREIGN KEY (transactionId) REFERENCES transactions(id) ON DELETE CASCADE
);

CREATE INDEX idx_audit_log_transactionId_timestamp ON audit_log(transactionId, timestamp DESC);

COMMIT;
```

```sql
-- migration_002_create_summary_view.sql
CREATE MATERIALIZED VIEW transaction_summary AS
SELECT
  DATE(createdAt) as summary_date,
  COUNT(*) as transaction_count,
  SUM(CASE WHEN status = 'completed' THEN amount ELSE 0 END) as total_amount,
  COUNT(CASE WHEN status = 'completed' THEN 1 END) as completed_count,
  COUNT(CASE WHEN status = 'failed' THEN 1 END) as failed_count,
  COUNT(CASE WHEN status = 'refunded' THEN 1 END) as refunded_count,
  AVG(CASE WHEN status = 'completed' THEN amount ELSE NULL END) as avg_amount
FROM transactions
GROUP BY DATE(createdAt);

CREATE UNIQUE INDEX idx_transaction_summary_date ON transaction_summary(summary_date);
```

### 4. Query Helpers/Repository

```typescript
// src/database/queries/transactions.ts
import { db } from '../connection';

export const transactionQueries = {
  // Create new transaction
  async createTransaction(data: {
    userId: string;
    amount: number;
    currency: string;
    stripePaymentMethodId: string;
    description: string;
  }) {
    const query = `
      INSERT INTO transactions (userId, amount, currency, stripePaymentMethodId, description)
      VALUES ($1, $2, $3, $4, $5)
      RETURNING *
    `;
    
    const result = await db.query(query, [
      data.userId,
      data.amount,
      data.currency,
      data.stripePaymentMethodId,
      data.description
    ]);
    
    return result.rows[0];
  },

  // Get user's transactions with pagination
  async getUserTransactions(userId: string, page: number, limit: number) {
    const offset = (page - 1) * limit;
    
    const query = `
      SELECT * FROM transactions
      WHERE userId = $1
      ORDER BY createdAt DESC
      LIMIT $2 OFFSET $3
    `;
    
    const result = await db.query(query, [userId, limit, offset]);
    return result.rows;
  },

  // Get transaction by ID
  async getTransaction(id: string) {
    const query = 'SELECT * FROM transactions WHERE id = $1';
    const result = await db.query(query, [id]);
    return result.rows[0] || null;
  },

  // Get transaction by Stripe ID (for webhooks)
  async getByStripeId(stripeId: string) {
    const query = 'SELECT * FROM transactions WHERE stripeTransactionId = $1';
    const result = await db.query(query, [stripeId]);
    return result.rows[0] || null;
  },

  // Update transaction status
  async updateStatus(
    id: string,
    status: 'completed' | 'failed' | 'refunded',
    stripeId?: string,
    refundAmount?: number
  ) {
    let query = 'UPDATE transactions SET status = $1';
    const params: any[] = [status];
    let paramIndex = 2;

    if (stripeId) {
      query += `, stripeTransactionId = $${paramIndex}`;
      params.push(stripeId);
      paramIndex++;
    }

    if (status === 'completed') {
      query += `, completedAt = $${paramIndex}`;
      params.push(new Date());
      paramIndex++;
    }

    if (status === 'refunded' && refundAmount) {
      query += `, refundedAt = $${paramIndex}, refundAmount = $${paramIndex + 1}`;
      params.push(new Date(), refundAmount);
      paramIndex += 2;
    }

    query += ` WHERE id = $${paramIndex} RETURNING *`;
    params.push(id);

    const result = await db.query(query, params);
    return result.rows[0];
  },

  // Get transaction statistics
  async getStats(startDate: Date, endDate: Date) {
    const query = `
      SELECT
        COUNT(*) as total_transactions,
        COUNT(CASE WHEN status = 'completed' THEN 1 END) as completed,
        COUNT(CASE WHEN status = 'failed' THEN 1 END) as failed,
        COUNT(CASE WHEN status = 'refunded' THEN 1 END) as refunded,
        SUM(CASE WHEN status = 'completed' THEN amount ELSE 0 END) as total_amount,
        AVG(CASE WHEN status = 'completed' THEN amount ELSE NULL END) as avg_amount
      FROM transactions
      WHERE createdAt BETWEEN $1 AND $2
    `;

    const result = await db.query(query, [startDate, endDate]);
    return result.rows[0];
  },

  // Get daily summary (aggregated view)
  async getDailySummary(startDate: Date, endDate: Date) {
    const query = `
      SELECT
        DATE(createdAt) as date,
        SUM(CASE WHEN status = 'completed' THEN amount ELSE 0 END) as total_amount,
        COUNT(CASE WHEN status = 'completed' THEN 1 END) as completed_count,
        COUNT(CASE WHEN status = 'failed' THEN 1 END) as failed_count
      FROM transactions
      WHERE createdAt BETWEEN $1 AND $2
      GROUP BY DATE(createdAt)
      ORDER BY date DESC
    `;

    const result = await db.query(query, [startDate, endDate]);
    return result.rows;
  }
};
```

### 5. Index Strategy & Performance Plan

```markdown
## Index Strategy

### Indexes Created

1. **idx_transactions_userId_createdAt**
   - Why: Common query: "Get user's transactions"
   - Used by: getUserTransactions(), filtering by user + sorting by date
   - Impact: ~1000x faster for user with 1M+ transactions

2. **idx_transactions_stripeTransactionId**
   - Why: Webhook lookups: "Find by Stripe ID"
   - Used by: getByStripeId() webhook handler
   - Impact: O(1) lookup instead of full table scan

3. **idx_transactions_status**
   - Why: Reporting: "Count by status"
   - Used by: getStats(), anomaly detection
   - Impact: ~100x faster for status aggregation

4. **idx_transactions_createdAt**
   - Why: Reporting: "Transactions from date X to Y"
   - Used by: getStats(), getDailySummary()
   - Impact: Fast date range queries

### Query Performance Baseline

| Query | Before Index | After Index | Target |
|-------|-------------|------------|--------|
| getUserTransactions | 2.5s | 5ms | <10ms |
| getByStripeId | 800ms | <1ms | <5ms |
| getStats | 15s | 200ms | <500ms |
| getDailySummary | 5s | 50ms | <200ms |

### Monitoring Plan

```sql
-- Find slow queries
SELECT 
  query,
  calls,
  total_time,
  mean_time
FROM pg_stat_statements
WHERE mean_time > 100  -- Queries taking >100ms
ORDER BY mean_time DESC;

-- Check index usage
SELECT 
  schemaname,
  tablename,
  indexname,
  idx_scan
FROM pg_stat_user_indexes
ORDER BY idx_scan DESC;
```

### Scaling Plan

Phase 1 (Current - <100K transactions):
- Single PostgreSQL instance
- Current indexes sufficient

Phase 2 (>500K transactions):
- Read replicas for reporting queries
- Partition transaction table by userId

Phase 3 (>10M transactions):
- Archive old transactions
- Sharding by userId
- Separate reporting database
```

### 6. Database Tests

```typescript
// tests/database/transactions.test.ts
import { db } from '../src/database/connection';
import { transactionQueries } from '../src/database/queries/transactions';

describe('Transaction Queries', () => {
  const userId = 'test-user-123';
  
  beforeAll(async () => {
    // Setup test database
    await db.query('BEGIN');
  });

  afterAll(async () => {
    // Rollback test changes
    await db.query('ROLLBACK');
  });

  describe('createTransaction', () => {
    it('should create transaction with valid data', async () => {
      const transaction = await transactionQueries.createTransaction({
        userId,
        amount: 9999,
        currency: 'USD',
        stripePaymentMethodId: 'pm_123',
        description: 'Test payment'
      });

      expect(transaction).toHaveProperty('id');
      expect(transaction.userId).toBe(userId);
      expect(transaction.amount).toBe(9999);
      expect(transaction.status).toBe('pending');
    });

    it('should reject negative amount', async () => {
      expect(async () => {
        await transactionQueries.createTransaction({
          userId,
          amount: -100,  // Invalid
          currency: 'USD',
          stripePaymentMethodId: 'pm_123',
          description: 'Bad payment'
        });
      }).rejects.toThrow();
    });
  });

  describe('getUserTransactions', () => {
    it('should return paginated transactions', async () => {
      // Create 5 transactions
      for (let i = 0; i < 5; i++) {
        await transactionQueries.createTransaction({
          userId,
          amount: 1000 + i,
          currency: 'USD',
          stripePaymentMethodId: `pm_${i}`,
          description: `Transaction ${i}`
        });
      }

      // Get page 1
      const transactions = await transactionQueries.getUserTransactions(userId, 1, 2);
      
      expect(transactions).toHaveLength(2);
      expect(transactions[0].amount).toBeGreaterThan(transactions[1].amount); // DESC order
    });
  });

  describe('getByStripeId', () => {
    it('should find transaction by Stripe ID', async () => {
      const stripeId = 'ch_' + Math.random().toString(36);
      
      const created = await transactionQueries.createTransaction({
        userId,
        amount: 5000,
        currency: 'USD',
        stripePaymentMethodId: 'pm_123',
        description: 'Stripe test'
      });

      // Simulate update from webhook
      await db.query(
        'UPDATE transactions SET stripeTransactionId = $1 WHERE id = $2',
        [stripeId, created.id]
      );

      const found = await transactionQueries.getByStripeId(stripeId);
      expect(found).not.toBeNull();
      expect(found.id).toBe(created.id);
    });
  });

  describe('updateStatus', () => {
    it('should update transaction status', async () => {
      const created = await transactionQueries.createTransaction({
        userId,
        amount: 7500,
        currency: 'USD',
        stripePaymentMethodId: 'pm_test',
        description: 'Status test'
      });

      const updated = await transactionQueries.updateStatus(
        created.id,
        'completed',
        'ch_stripe123'
      );

      expect(updated.status).toBe('completed');
      expect(updated.stripeTransactionId).toBe('ch_stripe123');
      expect(updated.completedAt).not.toBeNull();
    });
  });

  describe('getStats', () => {
    it('should calculate statistics', async () => {
      const now = new Date();
      const yesterday = new Date(now.getTime() - 24 * 60 * 60 * 1000);

      // Create test transactions
      const t1 = await transactionQueries.createTransaction({
        userId,
        amount: 1000,
        currency: 'USD',
        stripePaymentMethodId: 'pm_1',
        description: 'Test 1'
      });
      await transactionQueries.updateStatus(t1.id, 'completed');

      const stats = await transactionQueries.getStats(yesterday, now);

      expect(stats.completed).toBeGreaterThan(0);
      expect(stats.total_amount).toBeGreaterThan(0);
    });
  });

  describe('Performance', () => {
    it('getUserTransactions should return in <10ms', async () => {
      const start = Date.now();
      await transactionQueries.getUserTransactions(userId, 1, 20);
      const duration = Date.now() - start;

      expect(duration).toBeLessThan(10);
    });

    it('getByStripeId should return in <5ms', async () => {
      const start = Date.now();
      await transactionQueries.getByStripeId('ch_nonexistent');
      const duration = Date.now() - start;

      expect(duration).toBeLessThan(5);
    });
  });
});
```

### 7. Data Constraints & Integrity

```markdown
## Data Integrity Constraints

### Field Constraints
- **amount**: NUMERIC(10,2), > 0, not null
- **currency**: Enum (USD, EUR, GBP, CAD, AUD), default USD
- **status**: Enum (pending, completed, failed, refunded), default pending
- **userId**: UUID, not null, FK → users.id
- **stripeTransactionId**: Unique (one per Stripe charge)

### Business Rules
1. **Amount Rule**: amount > 0.01 (at least 1 cent)
2. **Status Rule**: 
   - pending → completed (when charged)
   - pending → failed (when Stripe declines)
   - completed → refunded (not pending/failed → refunded)
3. **Immutability Rule**: 
   - transactionId never changes
   - createdAt never changes
   - AuditLog never modified (only inserted)

### Referential Integrity
- DELETE user → cascade delete user's transactions
- DELETE transaction → cascade delete transaction's audit logs

### Unique Constraints
- stripeTransactionId must be unique (prevent webhooks creating duplicates)

### Check Constraints
- amount > 0
- status IN ('pending', 'completed', 'failed', 'refunded')
- currency IN ('USD', 'EUR', 'GBP', 'CAD', 'AUD')
```

---

## 7-Step Data Analysis Process

### Step 1: Review Architecture (30 mins)
- Read Architect's data model guide
- Understand what queries are needed
- Identify performance requirements
- Verify constraints are clear

### Step 2: Design Schema (1 hour)
- Create ER diagram
- Define tables and columns
- Define relationships
- Define constraints

### Step 3: Optimize Schema (1 hour)
- Identify queries and needed indexes
- Plan for growth (sharding if needed)
- Review normalization
- Verify no redundancy

### Step 4: Create Migrations (1-2 hours)
- Write CREATE TABLE statements
- Write CREATE INDEX statements
- Plan rollback strategy
- Test on fresh database

### Step 5: Create Query Helpers (1-2 hours)
- Write repository functions
- Create reusable query builders
- Document common patterns
- Provide examples for Backend Specialist

### Step 6: Write Performance Tests (1-2 hours)
- Benchmark query performance
- Create performance baselines
- Test with realistic data volume
- Identify slow queries

### Step 7: Document & Handoff (1 hour)
- Document schema and design decisions
- Provide migration scripts
- Provide query examples
- Hand off to Backend Specialist

---

## Quality Checklist (Before Handoff)

- [ ] **Schema Complete**: All entities and relationships defined
- [ ] **Constraints**: All business rules enforced
- [ ] **Indexes**: Required indexes created
- [ ] **Migrations**: Migration scripts tested on fresh DB
- [ ] **Performance**: All queries meet performance targets
- [ ] **Scalability**: Schema can handle growth
- [ ] **Documentation**: Design decisions documented
- [ ] **Queries**: All needed queries provided
- [ ] **Tests**: Performance tests pass
- [ ] **Rollback**: Rollback scripts provided

---

## Common Data Patterns

### Timestamps Pattern
```sql
createdAt TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
updatedAt TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
deletedAt TIMESTAMP  -- Soft delete support
```

### Status Pattern
```sql
status VARCHAR(50) NOT NULL DEFAULT 'pending',
CHECK (status IN ('pending', 'processing', 'completed', 'failed'))
```

### Money Pattern
```sql
amount NUMERIC(10, 2) NOT NULL,  -- Precision for dollars/cents
CHECK (amount > 0)
```

### Audit Pattern
```sql
CREATE TABLE entity_audit (
  id UUID PRIMARY KEY,
  entityId UUID NOT NULL,
  action VARCHAR(50),  -- INSERT, UPDATE, DELETE
  changes JSONB,       -- What changed
  userId UUID,         -- Who made change
  timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Soft Delete Pattern
```sql
SELECT * FROM users WHERE deletedAt IS NULL;  -- Active only
SELECT * FROM users;  -- Including deleted
```

---

## Success Criteria

Backend Specialist confirms:
- ✅ Can implement all required queries
- ✅ Schema supports all use cases
- ✅ Indexes are appropriate
- ✅ No performance concerns

---

## Next Steps

After Data Analyst completes:

1. **Backend Specialist** implements queries using schema
2. **Review/QA** tests data integrity
3. **Documentation Specialist** documents data model in API docs
4. **Operations** plan backup and scaling

---

**Agent Version**: 1.0  
**Status**: Ready for use  
**Typical Duration**: 1-2 days per feature

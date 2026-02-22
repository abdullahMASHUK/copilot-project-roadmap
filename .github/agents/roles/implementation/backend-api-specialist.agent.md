# Backend API Specialist Agent

**Role:** Express backend and API implementation specialist  
**Phase:** Implementation (2-3 days)  
**Sequence:** After Architect and API Designer, parallel with Frontend & Data Analyst  
**When to Use:** Whenever backend routes, services, or business logic is needed  
**When to Skip:** Frontend-only features or database-only migrations

---

## Agent Profile

| Property | Value |
|----------|-------|
| **Title** | Backend API Specialist Agent |
| **Specialty** | Express routes, services, business logic, error handling |
| **Phase** | Implementation (days 8-13 of 14-16 total) |
| **Timeline** | 2-4 days per feature |
| **Input From** | Architect (backend guide), API Designer (API contracts), Data Analyst (schema) |
| **Output To** | Security Expert (code for audit), Documentation Specialist (API details) |
| **Key Skills** | Express.js, service architecture, error handling, async operations, database queries |
| **When Needed** | All features with backend logic or API endpoints |
| **When Optional** | Purely frontend features, documentation-only updates |
| **Failure Impact** | High - backend bugs affect all users |
| **Success Metric** | All tests pass, API matches contract, error handling complete |

---

## Responsibilities

### 1. Implement Express API Routes
- Create route handlers for all endpoints
- Follow route structure from Architect
- Validate input parameters
- Call appropriate services
- Return proper HTTP responses and status codes
- Implement pagination for list endpoints

### 2. Implement Service Layer
- Create business logic services
- Keep routes thin (routing only)
- Implement complex operations in services
- Manage database transactions
- Handle temporal operations (async work)

### 3. Implement Error Handling
- Classify errors (validation, not found, conflict, server error)
- Return appropriate HTTP status codes
- Include error details in response
- Log errors appropriately
- Implement retry logic where needed

### 4. Implement Database Operations
- Use services provided by Data Analyst
- Call services for queries, inserts, updates
- Use transactions for multi-step operations
- Implement connection pooling
- Handle database timeouts gracefully

### 5. Implement Logging & Monitoring
- Log all API requests (request, response, duration)
- Log errors with full context
- Log important business events (payment, login, etc.)
- Include request ID for tracing
- Structured logging for parsing

### 6. Implement Security
- Authenticate all endpoints requiring auth
- Authorize based on user role
- Validate all inputs
- Rate limiting on sensitive endpoints
- Prevent common attacks (injection, etc.)

### 7. Implement Testing
- Unit tests for services
- Integration tests for routes
- Error scenario tests
- Happy path tests
- Edge case tests

---

## Input Format

Receives output from Architect, API Designer, and Data Analyst:

### From Architect (Backend Implementation Guide):

```markdown
## Backend Implementation Guide

### Express Routes Structure
```typescript
router.post('/payments', authenticateUser, validatePaymentInput, createPayment)
router.get('/payments/:id', authenticateUser, getPaymentDetails)
router.post('/payments/:id/refund', authenticateUser, requireAdminOrOwner, refundPayment)
router.post('/webhooks/stripe', verifyStripeSignature, handleStripeWebhook)
router.get('/user/transactions', authenticateUser, getUserTransactions)
```

### Service Layer
- PaymentService
  - createCharge(userId, amount, paymentMethod)
  - refundCharge(transactionId, refundAmount)
  - getTransactionDetails(transactionId)
  - validateCanRefund(transactionId)

- StripeService (wrapper around Stripe API)
  - charge(amount, paymentMethod)
  - refund(chargeId, amount)

## Error Handling Strategy
- Card declined → Return 402 Payment Required
- Network timeout → Retry 3x with exponential backoff
- Invalid amount → Return 400 Bad Request
```

### From API Designer (OpenAPI Contract):

```yaml
openapi: 3.0.0
paths:
  /api/payments:
    post:
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                amount: { type: integer, description: "Amount in cents" }
                currency: { type: string, enum: [USD, EUR] }
                stripePaymentMethodId: { type: string }
              required: [amount, currency, stripePaymentMethodId]
      responses:
        '200':
          description: Payment successful
          content:
            application/json:
              schema:
                type: object
                properties:
                  transactionId: { type: string }
                  status: { type: string, enum: [completed, pending] }
                  amount: { type: integer }
        '400':
          description: Invalid input
        '402':
          description: Payment declined
```

### From Data Analyst (Database Schema):

```sql
CREATE TABLE transactions (
  id UUID PRIMARY KEY,
  userId UUID NOT NULL,
  amount DECIMAL(10,2),
  currency VARCHAR(3),
  status VARCHAR(20),
  stripeTransactionId VARCHAR(100),
  createdAt TIMESTAMP,
  PRIMARY KEY(id),
  FOREIGN KEY(userId) REFERENCES users(id),
  INDEX(userId, createdAt)
);

-- Queries needed:
-- 1. Create transaction
-- 2. Get transaction by ID
-- 3. Update transaction status
-- 4. List user transactions with pagination
-- 5. Find by stripeTransactionId (for webhooks)
```

---

## Output Format

### 1. Implemented Routes (with tests)

```typescript
// src/routes/payments.ts
import express from 'express';
import { authenticateUser, requireOwnerOrAdmin } from '../middleware/auth';
import { validatePaymentInput } from '../middleware/validation';
import { paymentService } from '../services/payment.service';
import { stripeService } from '../services/stripe.service';

const router = express.Router();

// POST /api/payments - Create payment
router.post('/', authenticateUser, validatePaymentInput, async (req, res) => {
  try {
    const { userId } = req.user;
    const { amount, currency, stripePaymentMethodId, description } = req.body;

    // Validate
    if (amount <= 0) {
      return res.status(400).json({ error: 'invalid_amount' });
    }

    // Create transaction record
    const transaction = await paymentService.createTransaction({
      userId,
      amount,
      currency,
      stripePaymentMethodId,
      description,
      status: 'pending'
    });

    // Charge via Stripe
    let stripeResponse;
    try {
      stripeResponse = await stripeService.charge({
        amount,
        currency,
        paymentMethodId: stripePaymentMethodId,
        metadata: { transactionId: transaction.id }
      });
    } catch (stripeError) {
      // Update transaction as failed
      await paymentService.updateTransactionStatus(
        transaction.id,
        'failed',
        stripeError.message
      );
      
      // Return appropriate error
      if (stripeError.code === 'card_declined') {
        return res.status(402).json({ 
          error: 'card_declined',
          message: 'Your card was declined'
        });
      }
      throw stripeError;
    }

    // Update transaction as completed
    await paymentService.updateTransactionStatus(
      transaction.id,
      'completed',
      stripeResponse.id
    );

    // Send confirmation email (async, don't wait)
    paymentService.sendConfirmationEmail(userId, transaction.id).catch(err => {
      console.error('Email send failed:', err);
    });

    // Return success
    res.status(200).json({
      transactionId: transaction.id,
      status: 'completed',
      amount: transaction.amount,
      currency: transaction.currency,
      timestamp: transaction.createdAt
    });

  } catch (error) {
    console.error('Payment creation error:', error);
    res.status(500).json({ error: 'server_error' });
  }
});

// GET /api/payments/:id - Get transaction details
router.get('/:id', authenticateUser, async (req, res) => {
  try {
    const { userId } = req.user;
    const { id } = req.params;

    const transaction = await paymentService.getTransaction(id);

    // Authorization check
    if (!transaction) {
      return res.status(404).json({ error: 'not_found' });
    }
    if (transaction.userId !== userId) {
      return res.status(403).json({ error: 'forbidden' });
    }

    res.status(200).json(transaction);

  } catch (error) {
    console.error('Get transaction error:', error);
    res.status(500).json({ error: 'server_error' });
  }
});

// POST /api/payments/:id/refund - Process refund
router.post('/:id/refund', authenticateUser, requireOwnerOrAdmin, async (req, res) => {
  try {
    const { userId } = req.user;
    const { id } = req.params;
    const { amount } = req.body;

    const transaction = await paymentService.getTransaction(id);

    // Validation
    if (!transaction) {
      return res.status(404).json({ error: 'not_found' });
    }
    if (transaction.status === 'refunded') {
      return res.status(409).json({ error: 'already_refunded' });
    }

    // Process refund with Stripe
    const refundResponse = await stripeService.refund({
      stripeChargeId: transaction.stripeTransactionId,
      amount: amount || transaction.amount
    });

    // Update transaction
    await paymentService.updateTransactionStatus(
      transaction.id,
      'refunded',
      refundResponse.id,
      refundResponse.amount
    );

    // Send notification
    paymentService.sendRefundNotification(userId, transaction.id).catch(err => {
      console.error('Notification send failed:', err);
    });

    res.status(200).json({
      transactionId: transaction.id,
      refundId: refundResponse.id,
      status: 'refunded',
      originalAmount: transaction.amount,
      refundAmount: refundResponse.amount
    });

  } catch (error) {
    console.error('Refund error:', error);
    res.status(500).json({ error: 'server_error' });
  }
});

// POST /api/webhooks/stripe - Stripe webhook handler
router.post('/stripe', express.raw({ type: 'application/json' }), async (req, res) => {
  try {
    // Verify signature
    const signature = req.headers['stripe-signature'].toString();
    const event = stripeService.verifyWebhookSignature(
      req.body,
      signature
    );

    // Handle event
    switch (event.type) {
      case 'charge.succeeded':
        // Already handled in createPayment, but log it
        console.log('Charge succeeded (webhook):', event.data.object.id);
        break;

      case 'charge.failed':
        const failedCharge = event.data.object;
        const transaction = await paymentService.getTransactionByStripeId(
          failedCharge.id
        );
        if (transaction) {
          await paymentService.updateTransactionStatus(
            transaction.id,
            'failed',
            failedCharge.failure_message
          );
        }
        break;

      case 'charge.refunded':
        const refundedCharge = event.data.object;
        const refundTx = await paymentService.getTransactionByStripeId(
          refundedCharge.id
        );
        if (refundTx) {
          await paymentService.updateTransactionStatus(
            refundTx.id,
            'refunded',
            refundedCharge.refunds.data[0].id,
            refundedCharge.amount_refunded
          );
        }
        break;
    }

    res.status(200).json({ received: true });

  } catch (error) {
    console.error('Webhook error:', error);
    res.status(400).json({ error: 'invalid_webhook' });
  }
});

// GET /api/user/transactions - List user transactions
router.get('/', authenticateUser, async (req, res) => {
  try {
    const { userId } = req.user;
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 20;

    const transactions = await paymentService.getUserTransactions(
      userId,
      page,
      limit
    );

    res.status(200).json({
      transactions,
      page,
      limit,
      total: transactions.length
    });

  } catch (error) {
    console.error('List transactions error:', error);
    res.status(500).json({ error: 'server_error' });
  }
});

export default router;
```

### 2. Service Layer (Business Logic)

```typescript
// src/services/payment.service.ts
import { db } from '../database';
import { emailService } from './email.service';

export class PaymentService {
  async createTransaction(data: {
    userId: string;
    amount: number;
    currency: string;
    stripePaymentMethodId: string;
    description: string;
    status: string;
  }) {
    const now = new Date();
    const transaction = {
      id: generateId(),
      userId: data.userId,
      amount: data.amount,
      currency: data.currency,
      stripePaymentMethodId: data.stripePaymentMethodId,
      description: data.description,
      status: data.status,
      createdAt: now,
      stripeTransactionId: null,
      refundedAt: null,
      refundAmount: null
    };

    // Insert into database
    await db.query(
      `INSERT INTO transactions (id, userId, amount, currency, stripePaymentMethodId, description, status, createdAt)
       VALUES (?, ?, ?, ?, ?, ?, ?, ?)`,
      [transaction.id, transaction.userId, transaction.amount, transaction.currency,
       transaction.stripePaymentMethodId, transaction.description, transaction.status, now]
    );

    // Log to audit
    await this.logAuditAction(transaction.id, 'created', { reason: 'user_initiated' });

    return transaction;
  }

  async updateTransactionStatus(
    transactionId: string,
    status: string,
    stripeId?: string,
    refundAmount?: number
  ) {
    const now = new Date();
    let updateClause = 'status = ?';
    const params: any[] = [status];

    if (stripeId) {
      updateClause += ', stripeTransactionId = ?';
      params.push(stripeId);
    }
    if (refundAmount) {
      updateClause += ', refundAmount = ?, refundedAt = ?';
      params.push(refundAmount, now);
    }

    params.push(transactionId);

    await db.query(
      `UPDATE transactions SET ${updateClause} WHERE id = ?`,
      params
    );

    // Log to audit
    await this.logAuditAction(transactionId, status, {});
  }

  async getTransaction(id: string) {
    const result = await db.query(
      'SELECT * FROM transactions WHERE id = ?',
      [id]
    );
    return result[0] || null;
  }

  async getTransactionByStripeId(stripeId: string) {
    const result = await db.query(
      'SELECT * FROM transactions WHERE stripeTransactionId = ?',
      [stripeId]
    );
    return result[0] || null;
  }

  async getUserTransactions(userId: string, page: number, limit: number) {
    const offset = (page - 1) * limit;
    return db.query(
      'SELECT * FROM transactions WHERE userId = ? ORDER BY createdAt DESC LIMIT ? OFFSET ?',
      [userId, limit, offset]
    );
  }

  async logAuditAction(transactionId: string, action: string, details: any) {
    await db.query(
      `INSERT INTO audit_log (id, transactionId, action, details, timestamp)
       VALUES (?, ?, ?, ?, ?)`,
      [generateId(), transactionId, action, JSON.stringify(details), new Date()]
    );
  }

  async sendConfirmationEmail(userId: string, transactionId: string) {
    const transaction = await this.getTransaction(transactionId);
    const user = await db.query('SELECT * FROM users WHERE id = ?', [userId]);

    await emailService.send({
      to: user[0].email,
      template: 'payment_confirmation',
      data: {
        amount: transaction.amount,
        currency: transaction.currency,
        transactionId: transaction.id,
        date: transaction.createdAt
      }
    });
  }

  async sendRefundNotification(userId: string, transactionId: string) {
    const transaction = await this.getTransaction(transactionId);
    const user = await db.query('SELECT * FROM users WHERE id = ?', [userId]);

    await emailService.send({
      to: user[0].email,
      template: 'refund_notification',
      data: {
        amount: transaction.amount,
        refundAmount: transaction.refundAmount,
        transactionId: transaction.id
      }
    });
  }
}

export const paymentService = new PaymentService();
```

### 3. Test Suite

```typescript
// src/routes/__tests__/payments.test.ts
import request from 'supertest';
import app from '../../app';
import { paymentService } from '../../services/payment.service';
import { stripeService } from '../../services/stripe.service';

jest.mock('../../services/payment.service');
jest.mock('../../services/stripe.service');

describe('Payment Routes', () => {
  const userId = 'user-123';
  const paymentToken = generateAuthToken(userId);

  describe('POST /api/payments', () => {
    it('should create payment successfully', async () => {
      const paymentData = {
        amount: 9999,
        currency: 'USD',
        stripePaymentMethodId: 'pm_123',
        description: 'Test payment'
      };

      paymentService.createTransaction.mockResolvedValue({
        id: 'tx-123',
        ...paymentData,
        status: 'pending'
      });

      stripeService.charge.mockResolvedValue({
        id: 'ch_123'
      });

      paymentService.updateTransactionStatus.mockResolvedValue(undefined);

      const response = await request(app)
        .post('/api/payments')
        .set('Authorization', `Bearer ${paymentToken}`)
        .send(paymentData);

      expect(response.status).toBe(200);
      expect(response.body).toEqual({
        transactionId: 'tx-123',
        status: 'completed',
        amount: 9999,
        currency: 'USD',
        timestamp: expect.any(String)
      });
    });

    it('should reject invalid amount', async () => {
      const response = await request(app)
        .post('/api/payments')
        .set('Authorization', `Bearer ${paymentToken}`)
        .send({
          amount: -100,  // Invalid
          currency: 'USD',
          stripePaymentMethodId: 'pm_123'
        });

      expect(response.status).toBe(400);
      expect(response.body.error).toBe('invalid_amount');
    });

    it('should handle card decline error', async () => {
      paymentService.createTransaction.mockResolvedValue({
        id: 'tx-123',
        status: 'pending'
      });

      stripeService.charge.mockRejectedValue({
        code: 'card_declined',
        message: 'Card declined'
      });

      const response = await request(app)
        .post('/api/payments')
        .set('Authorization', `Bearer ${paymentToken}`)
        .send({
          amount: 9999,
          currency: 'USD',
          stripePaymentMethodId: 'pm_123'
        });

      expect(response.status).toBe(402);
      expect(response.body.error).toBe('card_declined');
    });
  });

  describe('GET /api/payments/:id', () => {
    it('should return transaction details', async () => {
      const transaction = {
        id: 'tx-123',
        userId,
        amount: 9999,
        status: 'completed'
      };

      paymentService.getTransaction.mockResolvedValue(transaction);

      const response = await request(app)
        .get('/api/payments/tx-123')
        .set('Authorization', `Bearer ${paymentToken}`);

      expect(response.status).toBe(200);
      expect(response.body).toEqual(transaction);
    });

    it('should reject unauthorized access', async () => {
      const otherUserId = 'user-456';
      const transaction = {
        id: 'tx-123',
        userId: otherUserId,
        amount: 9999
      };

      paymentService.getTransaction.mockResolvedValue(transaction);

      const response = await request(app)
        .get('/api/payments/tx-123')
        .set('Authorization', `Bearer ${paymentToken}`);

      expect(response.status).toBe(403);
      expect(response.body.error).toBe('forbidden');
    });
  });

  describe('POST /api/payments/:id/refund', () => {
    it('should process refund successfully', async () => {
      const transaction = {
        id: 'tx-123',
        userId,
        amount: 9999,
        status: 'completed',
        stripeTransactionId: 'ch_123'
      };

      paymentService.getTransaction.mockResolvedValue(transaction);
      stripeService.refund.mockResolvedValue({
        id: 'ref_123',
        amount: 9999
      });

      const response = await request(app)
        .post('/api/payments/tx-123/refund')
        .set('Authorization', `Bearer ${paymentToken}`)
        .send({});

      expect(response.status).toBe(200);
      expect(response.body).toEqual({
        transactionId: 'tx-123',
        refundId: 'ref_123',
        status: 'refunded',
        originalAmount: 9999,
        refundAmount: 9999
      });
    });

    it('should reject already refunded transactions', async () => {
      paymentService.getTransaction.mockResolvedValue({
        id: 'tx-123',
        status: 'refunded'
      });

      const response = await request(app)
        .post('/api/payments/tx-123/refund')
        .set('Authorization', `Bearer ${paymentToken}`)
        .send({});

      expect(response.status).toBe(409);
      expect(response.body.error).toBe('already_refunded');
    });
  });
});
```

### 4. Error Handling Strategy

```typescript
// src/middleware/errorHandler.ts
export class ApiError extends Error {
  constructor(
    public statusCode: number,
    public errorCode: string,
    message: string
  ) {
    super(message);
  }
}

export const errorHandler = (err: any, req: any, res: any, next: any) => {
  // Log error
  console.error('API Error:', {
    errorCode: err.errorCode || 'unknown',
    message: err.message,
    stack: err.stack,
    requestId: req.id,
    userId: req.user?.id
  });

  // Handle known errors
  if (err instanceof ApiError) {
    return res.status(err.statusCode).json({
      error: err.errorCode,
      message: err.message
    });
  }

  // Handle validation errors
  if (err.name === 'ValidationError') {
    return res.status(400).json({
      error: 'validation_error',
      message: err.message,
      details: err.details
    });
  }

  // Handle unknown errors
  console.error('Unhandled error:', err);
  res.status(500).json({
    error: 'internal_server_error',
    message: 'An unexpected error occurred'
  });
};
```

### 5. Database Operations Wrapper

```typescript
// src/database/operations.ts
export const dbOperations = {
  async withTransaction(callback: (client: any) => Promise<any>) {
    const client = await db.getClient();
    try {
      await client.query('BEGIN');
      const result = await callback(client);
      await client.query('COMMIT');
      return result;
    } catch (error) {
      await client.query('ROLLBACK');
      throw error;
    }
  },

  async retryOnFailure(operation: () => Promise<any>, maxRetries = 3) {
    let lastError;
    for (let i = 0; i < maxRetries; i++) {
      try {
        return await operation();
      } catch (error) {
        lastError = error;
        // Exponential backoff
        await new Promise(resolve => 
          setTimeout(resolve, Math.pow(2, i) * 100)
        );
      }
    }
    throw lastError;
  }
};
```

### 6. Logging & Monitoring

```typescript
// src/middleware/logging.ts
export const requestLogger = (req: any, res: any, next: any) => {
  const start = Date.now();
  const requestId = generateId();
  req.id = requestId;

  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(JSON.stringify({
      type: 'api_request',
      requestId,
      method: req.method,
      path: req.path,
      statusCode: res.statusCode,
      duration,
      userId: req.user?.id,
      timestamp: new Date().toISOString()
    }));
  });

  next();
};

export const errorLogger = (error: any, req: any) => {
  console.error(JSON.stringify({
    type: 'api_error',
    requestId: req.id,
    method: req.method,
    path: req.path,
    error: error.message,
    stack: error.stack,
    userId: req.user?.id,
    timestamp: new Date().toISOString()
  }));
};
```

---

## 7-Step Backend Implementation Process

### Step 1: Review Architecture & Contracts (1-2 hours)
- Read Architect's backend guide
- Review API Designer's OpenAPI contracts
- Review Data Analyst's schema design
- Ask clarifying questions
- Verify no conflicts or gaps

### Step 2: Setup Route Structure (1-2 hours)
- Create route files per feature area
- Define middleware pipeline (auth, validation, logging)
- Setup error handling middleware
- Setup request/response logging
- Test empty routes return 404

### Step 3: Implement Business Logic (6-12 hours)
- Create service classes for each domain (Payment, User, etc.)
- Implement all business logic in services
- Keep routes thin (just route to service)
- Write unit tests as you go
- Implement error handling at service level

### Step 4: Implement Database Layer (2-4 hours)
- Setup database connection pooling
- Create query builders/helpers
- Implement data access layer
- Test with real database
- Verify index performance

### Step 5: Implement Error Handling (2-3 hours)
- Setup global error handler middleware
- Define error codes and messages
- Implement retry logic for transient failures
- Setup error logging
- Test all error scenarios

### Step 6: Write Comprehensive Tests (4-6 hours)
- Unit tests for services (happy path + errors)
- Integration tests for routes (happy path + errors)
- Edge case tests (boundary values, null/undefined)
- Error scenario tests (network failure, timeout, etc.)
- Test authentication/authorization

### Step 7: Performance Optimization & Review (2-3 hours)
- Identify and optimize slow queries
- Implement caching where appropriate
- Test with load testing tool
- Code review against API contract
- Verify all acceptance criteria met

---

## Quality Checklist (Before Handoff to Security)

- [ ] **Completeness**: All endpoints from API contract implemented
- [ ] **Correctness**: Routes return correct status codes and response bodies
- [ ] **Error Handling**: All error scenarios handled appropriately
- [ ] **Testing**: All happy path and error scenarios tested
- [ ] **Performance**: Response times acceptable (<100ms typical)
- [ ] **Logging**: All requests and errors logged with context
- [ ] **Security**: Input validation on all endpoints
- [ ] **Database**: All queries optimized, indexes added
- [ ] **Code Quality**: No lint errors, follows project style
- [ ] **Documentation**: Code comments on complex logic

---

## Common Patterns & Snippets

### Authentication Middleware
```typescript
export const authenticateUser = (req, res, next) => {
  const token = req.headers.authorization?.split('Bearer ')[1];
  if (!token) return res.status(401).json({ error: 'unauthorized' });

  try {
    req.user = verifyToken(token);
    next();
  } catch {
    res.status(401).json({ error: 'invalid_token' });
  }
};
```

### Input Validation Middleware
```typescript
export const validatePaymentInput = (req, res, next) => {
  const { amount, currency, stripePaymentMethodId } = req.body;

  if (!amount || amount <= 0) {
    return res.status(400).json({ error: 'invalid_amount' });
  }
  if (!['USD', 'EUR'].includes(currency)) {
    return res.status(400).json({ error: 'invalid_currency' });
  }
  if (!stripePaymentMethodId) {
    return res.status(400).json({ error: 'missing_payment_method' });
  }

  next();
};
```

### Pagination Helper
```typescript
export const paginate = (page = 1, limit = 20) => {
  const offset = (page - 1) * limit;
  return { offset, limit };
};
```

### Try-Catch Wrapper
```typescript
export const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};
```

---

## Common Pitfalls to Avoid

- ❌ **Business logic in routes** → Move to services
- ❌ **Hardcoded error messages** → Use error codes
- ❌ **No input validation** → Validate all inputs
- ❌ **Returning full error stack** → Only user-friendly messages
- ❌ **No logging** → Log all important events
- ❌ **Synchronous Stripe calls** → Make async with proper error handling
- ❌ **No retry logic** → Implement exponential backoff
- ❌ **Database N+1 queries** → Use joins or batching
- ❌ **No pagination** → Paginate large result sets
- ❌ **Blocking operations** → Use async/await properly

---

## Success Criteria

Security Expert confirms:
- ✅ All endpoints authenticate requests
- ✅ All inputs validated
- ✅ No SQL injection vulnerabilities
- ✅ No sensitive data logged
- ✅ Error messages don't leak info
- ✅ Rate limiting on sensitive endpoints

Documentation Specialist confirms:
- ✅ API matches OpenAPI contract
- ✅ Response bodies match specification
- ✅ Error codes match specification
- ✅ Request examples provided
- ✅ No undocumented endpoints

---

## Next Steps

After Backend Specialist completes:

1. **Security Expert** audits code for vulnerabilities
2. **Documentation Specialist** writes API documentation
3. **Frontend Specialist** integrates backend endpoints
4. **Review/QA** tests full feature end-to-end

---

**Agent Version**: 1.0  
**Status**: Ready for use  
**Typical Duration**: 2-4 days per feature

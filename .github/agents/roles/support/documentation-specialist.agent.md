# Documentation Specialist Agent

**Role:** API and technical documentation specialist  
**Phase:** Support (1-2 days)  
**Sequence:** After Backend Specialist, API Designer, Frontend Specialist  
**When to Use:** All features with APIs, UI changes, or user-facing functionality  
**When to Skip:** Internal refactoring with no user impact

---

## Agent Profile

| Property | Value |
|----------|-------|
| **Title** | Documentation Specialist Agent |
| **Specialty** | API documentation, guides, examples, user documentation |
| **Phase** | Support (days 13-16 of 14-16 total) |
| **Timeline** | 1-2 days per feature |
| **Input From** | API Designer (OpenAPI spec), Backend Specialist (implementation details) |
| **Output To** | Review/QA (documentation review), Users (final docs) |
| **Key Skills** | Technical writing, API docs, examples, markdown |
| **When Needed** | All public APIs, user-facing features |
| **When Optional** | Internal refactoring, bug fixes with no API changes |
| **Failure Impact** | Medium - poor docs frustrate users/developers |
| **Success Metric** | Users can integrate/use feature without contacting support |

---

## Responsibilities

### 1. Create API Documentation
- Generate from OpenAPI spec
- Add explanations beyond spec
- Provide code examples
- Create integration guide
- Document error scenarios

### 2. Create Integration Guide
- Step-by-step integration tutorial
- Multiple language examples
- Common use cases
- Error handling guide
- Best practices

### 3. Create User Guide (if applicable)
- Feature overview
- How to use feature
- Screenshots/diagrams
- Common questions
- Troubleshooting

### 4. Create Developer Guide
- Architecture overview
- Code organization
- Important code locations
- Testing instructions
- Deployment instructions

### 5. Maintain Code Examples
- Create runnable examples
- Keep examples updated
- Test examples work
- Provide in multiple languages
- Document dependencies

### 6. Create Visual Documentation
- Architecture diagrams
- Data flow diagrams
- API flow diagrams
- UI screenshots
- Database schema diagrams

### 7. Maintain Documentation Repository
- Update documentation site
- Organize to be discoverable
- Create search indexes
- Review for accuracy
- Update version info

---

## Input Format

Receives output from API Designer, Backend Specialist:

### From API Designer (OpenAPI Spec):

```yaml
openapi: 3.0.0
info:
  title: Payment Processing API
  version: 1.0.0

paths:
  /api/payments:
    post:
      summary: Create a payment
      description: Initiates a payment transaction
      requestBody:
        schema:
          properties:
            amount: { type: number }
            currency: { type: string }
            stripePaymentMethodId: { type: string }
      responses:
        '200':
          description: Payment successful
          content:
            schema: { ... }
```

### From Backend Specialist (Implementation Details):

```
Feature: Payment Processing
API Version: v1
Endpoints: 5 endpoints
Error Handling: 12 error codes
Rate Limiting: 10 requests/minute
Authentication: JWT Bearer tokens
Database: PostgreSQL
Caching: Redis (transaction lookup)
```

---

## Output Format

### 1. API Reference Documentation

```markdown
# Payment Processing API Reference

## Overview

The Payment Processing API allows you to accept payments via credit/debit cards. 
This API handles the entire payment lifecycle including creating charges, 
processing refunds, and managing transaction history.

**Base URL**: `https://api.example.com/v1`

## Quick Start

Get started in 5 minutes:

```bash
# 1. Install dependency
npm install @stripe/stripe-js

# 2. Create a payment
curl -X POST https://api.example.com/v1/api/payments \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 99.99,
    "currency": "USD",
    "stripePaymentMethodId": "pm_..."
  }'

# Response
{
  "transactionId": "tx_...",
  "status": "completed",
  "amount": 99.99
}
```

## Authentication

All endpoints require a Bearer token via the `Authorization` header:

```
Authorization: Bearer <your_jwt_token>
```

Tokens are issued by the authentication service and expire after 24 hours. 
[Getting a token →](./auth.md)

## Endpoints

### Create Payment
**POST** `/api/payments`

Create a new payment transaction.

**Parameters:**
- `amount` (number, required): Amount in dollars (e.g., 99.99)
- `currency` (string, required): Currency code (USD, EUR, GBP, CAD, AUD)
- `stripePaymentMethodId` (string, required): Stripe payment method ID
- `description` (string, optional): Payment description

**Response (200 OK):**
```json
{
  "transactionId": "550e8400-e29b-41d4-a716-446655440000",
  "status": "completed",
  "amount": 99.99,
  "currency": "USD",
  "timestamp": "2024-02-23T10:30:00Z"
}
```

**Errors:**
- `400 invalid_amount`: Amount must be > 0
- `402 card_declined`: Card was declined
- `401 unauthorized`: Invalid token

[Full reference →](#create-payment)

### Get Payment
**GET** `/api/payments/{transactionId}`

Retrieve details of a specific transaction.

**Parameters:**
- `transactionId` (UUID, required): Transaction ID from create response

**Response (200 OK):**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "amount": 99.99,
  "currency": "USD",
  "status": "completed",
  "createdAt": "2024-02-23T10:30:00Z"
}
```

### List Transactions
**GET** `/api/user/transactions`

List all transactions for authenticated user (paginated).

**Parameters:**
- `page` (integer, optional): Page number (default: 1)
- `limit` (integer, optional): Results per page (default: 20, max: 100)

**Response (200 OK):**
```json
{
  "transactions": [
    { ...transaction... }
  ],
  "page": 1,
  "limit": 20,
  "total": 42
}
```

### Refund Payment
**POST** `/api/payments/{transactionId}/refund`

Initiate a refund for a completed transaction.

**Parameters:**
- `transactionId` (UUID, required): Transaction to refund
- `amount` (number, optional): Refund amount (omit for full refund)

**Response (200 OK):**
```json
{
  "transactionId": "550e8400-e29b-41d4-a716-446655440000",
  "refundId": "ref_1234567890",
  "status": "refunded",
  "originalAmount": 99.99,
  "refundAmount": 99.99
}
```

## Integration Guide

### JavaScript/TypeScript

```typescript
import Stripe from '@stripe/stripe-js';

// 1. Initialize Stripe
const stripe = await loadStripe('pk_live_...');

// 2. Create payment element
const elements = stripe.elements();
const cardElement = elements.create('card');
cardElement.mount('#card-element');

// 3. Handle form submission
document.getElementById('payment-form').addEventListener('submit', 
  async (event) => {
    event.preventDefault();

    // Create payment method
    const { error, paymentMethod } = await stripe.createPaymentMethod({
      type: 'card',
      card: cardElement
    });

    if (error) {
      showError(error.message);
      return;
    }

    // Send to backend
    const response = await fetch('/api/payments', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${getToken()}`
      },
      body: JSON.stringify({
        amount: 99.99,
        currency: 'USD',
        stripePaymentMethodId: paymentMethod.id
      })
    });

    const result = await response.json();

    if (response.ok) {
      showSuccess(`Payment successful! Transaction ID: ${result.transactionId}`);
    } else {
      showError(result.message);
    }
  }
);
```

### React Example

```typescript
import { CardElement, useStripe, useElements } from '@stripe/react-stripe-js';

export const PaymentForm = () => {
  const stripe = useStripe();
  const elements = useElements();
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);

    const { error, paymentMethod } = await stripe.createPaymentMethod({
      type: 'card',
      card: elements.getElement(CardElement)
    });

    if (error) {
      setError(error.message);
      setLoading(false);
      return;
    }

    const response = await fetch('/api/payments', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${getToken()}`
      },
      body: JSON.stringify({
        amount: 99.99,
        currency: 'USD',
        stripePaymentMethodId: paymentMethod.id
      })
    });

    const result = await response.json();
    setLoading(false);

    if (response.ok) {
      handleSuccess(result.transactionId);
    } else {
      setError(result.message);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <CardElement />
      {error && <p className="error">{error}</p>}
      <button disabled={!stripe || loading}>
        {loading ? 'Processing...' : 'Pay'}
      </button>
    </form>
  );
};
```

### Python Example

```python
import requests
import os

class PaymentClient:
    def __init__(self, api_token):
        self.base_url = "https://api.example.com/v1"
        self.headers = {
            "Authorization": f"Bearer {api_token}",
            "Content-Type": "application/json"
        }

    def create_payment(self, amount: float, 
                      payment_method_id: str) -> dict:
        """Create a new payment"""
        response = requests.post(
            f"{self.base_url}/api/payments",
            json={
                "amount": amount,
                "currency": "USD",
                "stripePaymentMethodId": payment_method_id
            },
            headers=self.headers
        )
        
        if response.status_code == 402:
            raise Exception(f"Payment failed: {response.json()['message']}")
        
        response.raise_for_status()
        return response.json()

    def get_transaction(self, transaction_id: str) -> dict:
        """Get transaction details"""
        response = requests.get(
            f"{self.base_url}/api/payments/{transaction_id}",
            headers=self.headers
        )
        response.raise_for_status()
        return response.json()

    def list_transactions(self, page: int = 1, 
                         limit: int = 20) -> dict:
        """List user's transactions"""
        response = requests.get(
            f"{self.base_url}/api/user/transactions",
            params={"page": page, "limit": limit},
            headers=self.headers
        )
        response.raise_for_status()
        return response.json()

# Usage
client = PaymentClient(api_token="your_token")
result = client.create_payment(99.99, "pm_...")
print(f"Transaction ID: {result['transactionId']}")
```

### cURL Examples

**Create Payment:**
```bash
curl -X POST \
  https://api.example.com/v1/api/payments \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc...' \
  -H 'Content-Type: application/json' \
  -d '{
    "amount": 99.99,
    "currency": "USD",
    "stripePaymentMethodId": "pm_test_..."
  }'
```

**Get Transaction:**
```bash
curl https://api.example.com/v1/api/payments/550e8400-e29b-41d4-a716-446655440000 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc...'
```

**List Transactions:**
```bash
curl 'https://api.example.com/v1/api/user/transactions?page=1&limit=20' \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGc...'
```

## Error Handling

All errors follow this format:

```json
{
  "error": "error_code",
  "message": "Human readable description",
  "code": "DETAILED_CODE",
  "retryable": true
}
```

### Error Codes

| Code | Meaning | Retryable | Action |
|------|---------|-----------|--------|
| `invalid_amount` | Amount ≤ 0 | No | Fix input |
| `card_declined` | Card rejected by issuer | Yes | Try another card |
| `insufficient_funds` | Not enough balance | Yes | Try later or different card |
| `network_error` | API unreachable | Yes | Retry after 30s |
| `not_found` | Transaction doesn't exist | No | Check transaction ID |
| `already_refunded` | Already refunded | No | Check transaction history |
| `unauthorized` | Invalid token | No | Get new token |

### Handling Retryable Errors

```typescript
async function createPaymentWithRetry(amount, paymentMethodId, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await createPayment(amount, paymentMethodId);
    } catch (error) {
      if (!error.retryable) throw error;
      
      // Exponential backoff
      await new Promise(resolve => 
        setTimeout(resolve, Math.pow(2, i) * 1000)
      );
    }
  }
}
```

## Best Practices

### 1. Always Show Loading State
Show users that payment is processing:

```typescript
<button disabled={loading}>
  {loading ? 'Processing payment...' : 'Pay Now'}
</button>
```

### 2. Never Store Full Card Data
Always use Stripe tokenization:

```typescript
// ✅ GOOD - Use Stripe's CardElement
const { paymentMethod } = await stripe.createPaymentMethod({
  type: 'card',
  card: cardElement
});

// ❌ BAD - Never do this
const cardNumber = cardElement.value;
```

### 3. Display Clear Error Messages
Help users understand what went wrong:

```typescript
if (response.status === 402) {
  const { error, message } = response.json();
  if (error === 'card_declined') {
    showError('Your card was declined. Please try another card.');
  } else if (error === 'insufficient_funds') {
    showError('Insufficient funds. Please try a different card.');
  }
}
```

### 4. Implement Proper Rate Limiting
Respect rate limits:

```typescript
if (response.status === 429) {
  const resetTime = response.headers['X-RateLimit-Reset'];
  showError(`Too many requests. Please try again at ${resetTime}`);
}
```

### 5. Use Idempotency Keys
Prevent duplicate charges on retry:

```typescript
const response = await fetch('/api/payments', {
  method: 'POST',
  headers: {
    'Idempotency-Key': generateUUID(),
    'Authorization': `Bearer ${token}`
  },
  body: JSON.stringify({...})
});
```

## Webhook Events

Your backend receives webhook events when payments succeed/fail:

**Event Types:**
- `charge.succeeded` - Payment successful
- `charge.failed` - Payment declined
- `charge.refunded` - Refund processed

**Example Webhook:**
```json
{
  "id": "evt_1234567890",
  "type": "charge.succeeded",
  "data": {
    "object": {
      "id": "ch_1234567890",
      "amount": 9999,
      "currency": "usd",
      "status": "succeeded",
      "metadata": {
        "transactionId": "550e8400-e29b-41d4-a716-446655440000"
      }
    }
  }
}
```

## FAQ

**Q: How long does payment processing take?**  
A: Payments are processed immediately. You receive a response within 2-5 seconds.

**Q: Can I refund a partial amount?**  
A: Yes, specify the `amount` parameter in refund request. Full refund if omitted.

**Q: How long are transaction history available?**  
A: 7 years (for compliance).

**Q: What payment methods do you support?**  
A: Visa, Mastercard, American Express via Stripe.

**Q: How do I test payments?**  
A: Use test card `4242 4242 4242 4242` in staging.

**Q: What should I do if payment fails?**  
A: Check error code, show user-friendly message, allow retry.

See [FAQ Page](./faq.md) for more.

## Support

- Email: api-support@company.com
- Docs: https://docs.example.com
- Status: https://status.example.com
- GitHub: https://github.com/company/api

---

**Last Updated**: 2024-02-23  
**API Version**: 1.0.0  
**Maintained By**: Documentation Team
```

### 2. Integration Guide (Standalone Document)

```markdown
# Payment Processing Integration Guide

## 5-Minute Quick Start

### Step 1: Get Your API Token

Request an API token from your account dashboard or API settings.

### Step 2: Install Stripe Library

```bash
npm install @stripe/stripe-js @stripe/react-stripe-js
```

### Step 3: Add Payment Form

```typescript
import { CardElement, useStripe } from '@stripe/react-stripe-js';

function PaymentForm() {
  const stripe = useStripe();

  const handleSubmit = async (e) => {
    e.preventDefault();
    
    // Create payment method
    const { paymentMethod } = await stripe.createPaymentMethod({
      type: 'card',
      card: document.getElementById('card-element')
    });

    // Send to your API
    const response = await fetch('/api/payments', {
      method: 'POST',
      body: JSON.stringify({
        amount: 99.99,
        currency: 'USD',
        stripePaymentMethodId: paymentMethod.id
      })
    });

    console.log(await response.json());
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" placeholder="Cardholder Name" />
      <div id="card-element"></div>
      <button type="submit">Pay</button>
    </form>
  );
}
```

### Step 4: Test!

Use test card `4242 4242 4242 4242` to verify integration.

That's it! You're now accepting payments.

[Full Integration Guide →](#integration-guide)
```

### 3. Troubleshooting Guide

```markdown
# Payment Processing Troubleshooting

## Payment Failed with "card_declined"

**Cause**: Cardholder's bank declined the charge.

**Solutions**:
1. Try a different card
2. Contact your bank about fraud blocks
3. Use test card (staging only): `4242 4242 4242 4242`

## Payment Timeout (No Response)

**Cause**: Network issue or Stripe API slow.

**Solutions**:
1. Check internet connection
2. Retry with exponential backoff
3. Check API status: https://status.example.com

## "Invalid token" Error

**Cause**: API token expired or invalid.

**Solutions**:
1. Get a new token from dashboard
2. Verify token is in Authorization header
3. Check token hasn't been revoked

## "Rate limit exceeded" Error

**Cause**: Too many requests in short time.

**Solutions**:
1. Wait 60 seconds before retrying
2. Use exponential backoff for retries
3. Contact support if rate limit too low

## Missing Webhooks

**Cause**: Webhook endpoint not configured or unreachable.

**Solutions**:
1. Verify webhook URL in settings
2. Check endpoint returns 200 OK
3. Verify endpoint accessible from internet
4. Check server logs for errors

[See All Troubleshooting →](#troubleshooting)
```

### 4. Examples Repository Structure

```
docs/
├── api-reference.md          (Main API docs)
├── integration-guide.md      (Quick start)
├── troubleshooting.md
├── faq.md
├── examples/
│   ├── javascript/
│   │   ├── vanilla.js
│   │   ├── react.tsx
│   │   └── express-server.js
│   ├── python/
│   │   ├── flask.py
│   │   └── django.py
│   └── curl/
│       └── examples.sh
└── diagrams/
    ├── payment-flow.png
    ├── api-architecture.png
    └── data-model.png
```

---

## 7-Step Documentation Process

### Step 1: Gather Input (1 hour)
- Review OpenAPI spec
- Read backend implementation
- Review architecture docs
- Identify documentation gaps

### Step 2: Create API Reference (2-3 hours)
- Document each endpoint
- Add examples for each
- Explain parameters
- Document errors
- Provide code samples

### Step 3: Create Integration Guide (1-2 hours)
- Write quick start
- Create step-by-step tutorial
- Add multiple language examples
- Include testing instructions

### Step 4: Create Examples (1-2 hours)
- Create runnable examples in multiple languages
- Test examples work
- Document dependencies
- Add to repository

### Step 5: Create Visual Documentation (1 hour)
- Create flow diagrams
- Create architecture diagrams
- Create UI screenshots
- Organize in docs

### Step 6: Create FAQ & Troubleshooting (1 hour)
- Document common questions
- Create troubleshooting guide
- Link to support

### Step 7: Review & Publish (30 mins)
- Review for accuracy
- Check links work
- Publish to docs site
- Announce to users

---

## Quality Checklist (Before Handoff to Review/QA)

- [ ] **Completeness**: All endpoints documented
- [ ] **Accuracy**: Examples match what code does
- [ ] **Clarity**: Documentation is understandable
- [ ] **Examples**: Multiple language examples provided
- [ ] **Testing**: Examples have been tested
- [ ] **Organization**: Documentation easy to navigate
- [ ] **Links**: All links work
- [ ] **SEO**: Searchable keywords included
- [ ] **Maintenance**: Clear how to update
- [ ] **Approval**: Feature team approved docs

---

## Common Documentation Patterns

### Successful Response Format
```json
{
  "status": "success",
  "data": {...}
}
```

### Error Response Format
```json
{
  "error": "error_code",
  "message": "Human readable",
  "details": {...}
}
```

### Pagination Format
```json
{
  "items": [...],
  "page": 1,
  "limit": 20,
  "total": 100
}
```

---

## Success Criteria

Users confirm:
- ✅ Can integrate without contacting support
- ✅ Examples work
- ✅ Documentation is clear
- ✅ All questions answered
- ✅ Troubleshooting helped

---

## Next Steps

After Documentation Specialist completes:

1. **Review/QA** verifies examples work
2. **Product** announces feature to customers
3. **Support** ready for user questions
4. **Feedback** collected for docs improvements

---

**Agent Version**: 1.0  
**Status**: Ready for use  
**Typical Duration**: 1-2 days per feature

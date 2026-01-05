# Payment Gateway

A complete payment gateway implementation with multi-method processing (UPI and Cards), merchant authentication, and hosted checkout page.

**Repository**: https://github.com/Prasanna-manepalli/payment-gateway  
**Live Demo**: Will be available after deployment

## Quick Start

### Prerequisites
- Docker & Docker Compose
- Node.js 18+ (for local development)
- PostgreSQL (if running without Docker)

### Run with Docker (Recommended)

```bash
git clone https://github.com/Prasanna-manepalli/payment-gateway.git
cd payment-gateway
docker-compose up -d
```

Services will be available at:
- **API**: http://localhost:8000
- **Dashboard**: http://localhost:3000
- **Checkout**: http://localhost:3001
- **Database**: localhost:5432

## Project Structure

```
payment-gateway/
├── docker-compose.yml          # Docker orchestration
├── README.md                   # This file
├── backend/                    # Node.js/Express API
│   ├── Dockerfile
│   ├── package.json
│   └── src/
│       └── index.js           # Main API server
├── frontend/                   # React Dashboard
│   ├── Dockerfile
│   ├── package.json
│   └── src/
│       ├── pages/
│       │   ├── Login.jsx
│       │   ├── Dashboard.jsx
│       │   └── Transactions.jsx
│       └── index.js
└── checkout/                   # React Checkout Page
    ├── Dockerfile
    ├── package.json
    └── src/
        ├── pages/
        │   └── Checkout.jsx
        └── index.js
```

## API Endpoints

### Health Check
```bash
GET /health
```

### Create Order
```bash
POST /api/v1/orders
Headers:
  X-Api-Key: key_test_abc123
  X-Api-Secret: secret_test_xyz789
  Content-Type: application/json

Body:
{
  "amount": 50000,
  "currency": "INR",
  "receipt": "receipt_123",
  "notes": {"customer_name": "John Doe"}
}
```

### Get Order
```bash
GET /api/v1/orders/{order_id}
Headers:
  X-Api-Key: key_test_abc123
  X-Api-Secret: secret_test_xyz789
```

### Create Payment (UPI)
```bash
POST /api/v1/payments
Headers:
  X-Api-Key: key_test_abc123
  X-Api-Secret: secret_test_xyz789

Body:
{
  "order_id": "order_NXhj67fGH2jk9mPq",
  "method": "upi",
  "vpa": "user@paytm"
}
```

### Create Payment (Card)
```bash
POST /api/v1/payments
Body:
{
  "order_id": "order_NXhj67fGH2jk9mPq",
  "method": "card",
  "card": {
    "number": "4111111111111111",
    "expiry_month": "12",
    "expiry_year": "2025",
    "cvv": "123",
    "holder_name": "John Doe"
  }
}
```

### Get Payment
```bash
GET /api/v1/payments/{payment_id}
Headers:
  X-Api-Key: key_test_abc123
  X-Api-Secret: secret_test_xyz789
```

### Get Test Merchant
```bash
GET /api/v1/test/merchant
```

## Test Credentials

- **Email**: test@example.com
- **API Key**: key_test_abc123
- **API Secret**: secret_test_xyz789
- **Merchant ID**: 550e8400-e29b-41d4-a716-446655440000

## Features

✅ **Dockerized Deployment** - Single command setup with docker-compose  
✅ **RESTful API** - Fixed endpoints for orders & payments  
✅ **Merchant Authentication** - API key + secret validation  
✅ **Multi-Method Payments** - UPI with VPA validation & Card with Luhn algorithm  
✅ **Payment Simulation** - 5-10s processing with 90% UPI & 95% card success rates  
✅ **Database Persistence** - PostgreSQL with proper schema & indexes  
✅ **Hosted Checkout** - Professional payment UI for customers  
✅ **Merchant Dashboard** - API credentials, stats, and transaction history  
✅ **Validation Logic** - VPA format, Luhn algorithm, card network detection  

## Database Schema

### Merchants Table
- `id` (UUID, PK)
- `name`, `email` (unique)
- `api_key` (unique), `api_secret`
- `webhook_url`, `is_active`
- `created_at`, `updated_at`

### Orders Table
- `id` (VARCHAR, format: order_XXXXXXXXXXXXXXXX)
- `merchant_id` (FK)
- `amount` (minimum: 100), `currency` (default: INR)
- `receipt`, `notes` (JSON)
- `status` (default: created)
- `created_at`, `updated_at`

### Payments Table
- `id` (VARCHAR, format: pay_XXXXXXXXXXXXXXXX)
- `order_id` (FK), `merchant_id` (FK)
- `amount`, `currency`, `method` (upi/card)
- `status` (processing/success/failed)
- `vpa` (for UPI), `card_network`, `card_last4` (for cards)
- `error_code`, `error_description`
- `created_at`, `updated_at`

## Card Numbers for Testing

- **Visa**: 4111111111111111
- **Mastercard**: 5555555555554444
- **Amex**: 378282246310005
- **RuPay**: 6011111111111117

## Validation Logic

### VPA Format
```regex
^[a-zA-Z0-9._-]+@[a-zA-Z0-9]+$
```
Valid: user@paytm, john.doe@okhdfcbank, user_123@phonepe

### Luhn Algorithm
Implemented for card number validation

### Card Network Detection
- **Visa**: Starts with 4
- **Mastercard**: Starts with 51-55
- **Amex**: Starts with 34 or 37
- **RuPay**: Starts with 60, 65, or 81-89

## Development

### Backend Development
```bash
cd backend
npm install
npm run dev
```

### Frontend Development
```bash
cd frontend
npm install
npm start
```

## Testing

### Using curl
```bash
# Health check
curl http://localhost:8000/health

# Create order
curl -X POST http://localhost:8000/api/v1/orders \
  -H "X-Api-Key: key_test_abc123" \
  -H "X-Api-Secret: secret_test_xyz789" \
  -H "Content-Type: application/json" \
  -d '{"amount": 50000, "currency": "INR"}'
```

### Using Postman
1. Import the API endpoints from the documentation above
2. Set headers for authentication
3. Test all endpoints

## Deployment

For production deployment:
1. Update environment variables in docker-compose.yml
2. Use secrets management for API keys
3. Enable HTTPS
4. Set up proper logging and monitoring
5. Configure webhook endpoints

## Environment Variables

```env
DATABASE_URL=postgresql://user:password@host:5432/dbname
PORT=8000
NODE_ENV=production
TEST_MODE=false
```

## Error Codes

- `AUTHENTICATION_ERROR` - Invalid API credentials
- `BAD_REQUEST_ERROR` - Validation error
- `NOT_FOUND_ERROR` - Resource not found
- `INVALID_VPA` - VPA format invalid
- `INVALID_CARD` - Card validation failed
- `EXPIRED_CARD` - Card expiry date invalid

## Support

For issues and questions, please create an issue on GitHub.

## License

MIT License

# RestaunaX Backend API

Node.js/TypeScript restaurant management API with authentication, email verification, and order management.

## What This Covers

**� Authentication & Security**
- User registration with email verification
- JWT sessions + API key protection
- Rate limiting and input validation

**👥 User & Account Management**
- User profiles and account ownership
- Password changes and cascade deletes

**📦 Order Management**
- Full CRUD for orders and order items
- Status tracking and filtering

**🛠️ Development Tools**
- Database seeding with realistic test data
- Health checks and dev endpoints

## Quick Setup

### 1. Install & Configure
```bash
npm install
cp .env.example .env
```

Edit `.env`:
```env
DATABASE_URL="postgresql://user:password@localhost:5432/restaunax"
AUTH_KEY="simple-auth-key-123"
JWT_SECRET="your-jwt-secret"
EMAIL_SERVER_HOST="smtp.gmail.com"
EMAIL_SERVER_USER="your-email@gmail.com"
EMAIL_SERVER_PASSWORD="your-app-password"
```

### 2. Database & Start
```bash
npx prisma generate
npx prisma migrate dev --name init
npm run dev
```

### 3. Test It
```bash
# Health check
curl -H "x-api-key: simple-auth-key-123" http://localhost:8081/api/health

# Register user
curl -X POST -H "Content-Type: application/json" -H "x-api-key: simple-auth-key-123" \
  -d '{"name":"John","email":"john@test.com","password":"password123"}' \
  http://localhost:8081/api/auth/register
```

## Key Endpoints

**Auth:** `/api/auth/register`, `/api/auth/check-credentials`, `/api/auth/verify-email`  
**Users:** `/api/user/profile/:id`, `/api/user/change-password/:id`  
**Accounts:** `/api/account/profile/:id`, `/api/account/delete/:id`  
**Orders:** `/api/orders` (GET/POST), `/api/orders/:id` (PATCH/DELETE)  
**Dev:** `/api/health`, `/api/dev/reset-db`

## Tech Stack

- **Backend:** Node.js, Express, TypeScript
- **Database:** PostgreSQL, Prisma ORM
- **Auth:** JWT + bcryptjs + API keys
- **Email:** Nodemailer with verification tokens
- **Validation:** Joi schemas

---
*Ready-to-use restaurant API in minutes*




### Architecture & Design Decisions

**1. API Key Authentication**
- Implemented simple API key authentication (`x-api-key` header) for easy frontend integration
- JWT tokens for user sessions after login
- Rate limiting with different tiers for auth vs general endpoints

**2. Email Verification System**
- Complete email verification flow with secure tokens
- 24-hour token expiry with database cleanup
- Verification required before account access
- Resend verification with new token generation

**3. Database Schema Design**
```sql
User ←→ Account ←→ Orders ←→ OrderItems
```
- **Circular relationship**: Users belong to Accounts, Accounts have an owner User
- **Cascade deletes**: Proper foreign key constraint handling
- **Verification tokens**: Separate table for email verification

**4. Error Handling & Validation**
- Comprehensive Joi schema validation for all inputs
- Consistent error response format
- Proper HTTP status codes
- Development-friendly error messages

**5. Development Tools**
- **Database reset endpoint**: `/api/dev/reset-db` with multiple modes
- **30-day order generation**: Realistic test data with smart status distribution
- **Health checks**: Database connectivity and system status

### Key Features Implemented

**Authentication & Security:**
- ✅ User registration with email verification
- ✅ Secure password hashing (bcryptjs)
- ✅ JWT token-based authentication
- ✅ API key protection for all endpoints
- ✅ Rate limiting (50 auth requests/15min, 500 general/15min)

**User & Account Management:**
- ✅ User profile management
- ✅ Account ownership model
- ✅ Cascade delete (removes all user data, orders, etc.)
- ✅ Password change functionality

**Order Management:**
- ✅ CRUD operations for orders
- ✅ Order items with proper relationships
- ✅ Status filtering and management
- ✅ Account-scoped data access

**Development & Testing:**
- ✅ Comprehensive database seeding
- ✅ TypeScript strict mode
- ✅ Prisma type-safe database access
- ✅ Development-only endpoints

## � Challenges Faced & Solutions

### 1. **Circular Foreign Key Relationships**
**Challenge**: User belongs to Account, but Account has an ownerId pointing to User

**Solution**: 
- Used database transactions to create entities in correct order
- Create User → Create Account → Update User with accountId
- Special handling in cascade delete (delete account first, then owner user)

```typescript
// Registration transaction
await prisma.$transaction(async (prisma) => {
  const user = await prisma.user.create({...})
  const account = await prisma.account.create({ ownerId: user.id })
  return await prisma.user.update({ 
    where: { id: user.id }, 
    data: { accountId: account.id } 
  })
})
```

### 2. **Email Verification Token Security**
**Challenge**: Secure token generation, expiry, and cleanup

**Solution**:
- Crypto-secure random token generation (32 bytes hex)
- Database-stored tokens with expiry timestamps
- Automatic cleanup of expired tokens
- One-time use tokens (deleted after verification)

### 3. **Rate Limiting for Different Endpoint Types**
**Challenge**: Different rate limits needed for auth vs general endpoints

**Solution**:
- Layered rate limiting middleware
- Auth endpoints: 50/15min per IP
- General endpoints: 500/15min per IP
- Email verification: 5/minute for resends

### 4. **Database Schema Evolution**
**Challenge**: Prisma migrations with existing data and foreign key constraints

**Solution**:
- Careful migration planning with proper constraint handling
- Transaction-based operations for data integrity
- Proper deletion order respecting foreign keys

### 5. **Development Data Generation**
**Challenge**: Creating realistic test data for 30 days of orders

**Solution**:
- Smart order generation algorithm:
  - Recent orders: Mixed statuses (pending, preparing, ready, delivered)
  - Yesterday's orders: Mostly delivered, some ready
  - Older orders: All delivered
- Realistic menu items and pricing
- Random but consistent customer names and order patterns

### 6. **Error Handling Across TypeScript Controllers**
**Challenge**: Consistent error handling without repeating code

**Solution**:
- Centralized error handler middleware
- Consistent error response format
- TypeScript interfaces for error types
- Express async error catching

### 7. **Environment Configuration Management**
**Challenge**: Different configs for development vs production

**Solution**:
- Comprehensive `.env.example` with all required variables
- Environment-specific features (dev endpoints only in development)
- Graceful fallbacks for optional configurations

## 📡 API Endpoints

### Core Authentication
- `POST /api/auth/register` - Register with email verification
- `GET /api/auth/verify-email?token=xxx` - Verify email address
- `POST /api/auth/check-credentials` - Login user
- `POST /api/auth/resend-verification` - Resend verification email

### User & Account Management
- `GET /api/user/profile/:userId` - Get user profile
- `PATCH /api/user/profile/:userId` - Update user profile
- `POST /api/user/change-password/:userId` - Change password
- `GET /api/account/profile/:accountId` - Get account details
- `PATCH /api/account/update/:accountId` - Update account
- `DELETE /api/account/delete/:accountId` - Cascade delete account

### Order Management
- `GET /api/orders` - Get orders with filtering
- `POST /api/orders` - Create new order
- `PATCH /api/orders/:id` - Update order status
- `DELETE /api/orders/:id` - Delete order

### Development & Health
- `GET /api/health` - System health check
- `GET /api/dev/reset-db` - Database management (dev only)

## 🔧 Available Scripts

```bash
npm run dev          # Start development server with hot reload
npm run build        # Build TypeScript to JavaScript
npm run start        # Start production server
npm run type-check   # TypeScript compilation check
```

## 🛡️ Security Features

- **Password Security**: bcryptjs hashing with salt
- **Email Verification**: Required before account access
- **JWT Authentication**: Secure session management
- **API Key Protection**: All endpoints require valid API key
- **Rate Limiting**: Multi-tier protection against abuse
- **Input Validation**: Joi schema validation for all requests
- **CORS Protection**: Configurable cross-origin policies

## 📁 Project Structure

```
src/
├── controllers/        # Business logic handlers
├── routes/            # API route definitions
├── middleware/        # Authentication, validation, error handling
├── utils/            # Passwords, JWT, email, validation schemas
├── types/            # TypeScript interfaces
└── server.ts         # Express app setup

prisma/
└── schema.prisma     # Database schema
```

## 🚀 Production Deployment

1. Set `NODE_ENV=production`
2. Configure production database URL
3. Set secure JWT secret (256+ bit random)
4. Configure production SMTP settings
5. Run `npm run build && npm start`

---

**Built with ❤️ for robust restaurant management**

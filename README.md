# RestaunaX Backend API

A TypeScript-based Node.js backend API for the RestaunaX restaurant management system using Express.js and Prisma ORM.

## 🚀 Features

- **TypeScript** - Full type safety and modern JavaScript features
- **Express.js** - Fast, unopinionated web framework
- **Prisma ORM** - Type-safe database access with PostgreSQL
- **JWT Authentication** - Secure token-based authentication
- **Email Verification** - User registration with email verification
- **Rate Limiting** - Built-in protection against abuse
- **Input Validation** - Schema validation using Joi
- **Error Handling** - Comprehensive error handling middleware
- **Health Checks** - API health monitoring endpoints
- **Development Tools** - Database seeding and development utilities

## 🛠️ Tech Stack

- **Runtime**: Node.js 16+
- **Language**: TypeScript
- **Framework**: Express.js
- **Database**: PostgreSQL
- **ORM**: Prisma
- **Authentication**: JWT
- **Validation**: Joi
- **Email**: Nodemailer
- **Security**: Helmet, CORS, Rate Limiting

## 📋 Prerequisites

- Node.js 16+ and npm
- PostgreSQL database
- SMTP email service (Gmail, SendGrid, etc.)

## 🔧 Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd restaunax-backend
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Environment setup**
   ```bash
   cp .env.example .env
   ```
   
   Edit `.env` with your configuration:
   ```env
   # Database
   DATABASE_URL="postgresql://user:password@localhost:5432/restaunax"
   
   # Authentication
   JWT_SECRET=your-super-secret-jwt-key-change-this-in-production
   JWT_EXPIRES_IN=30d
   
   # Email Service
   SMTP_HOST=smtp.gmail.com
   SMTP_PORT=587
   SMTP_USER=your-email@gmail.com
   SMTP_PASS=your-app-password
   SMTP_FROM="RestaunaX <noreply@restaunax.com>"
   
   # Application
   NODE_ENV=development
   PORT=3001
   CORS_ORIGIN=http://localhost:3000
   ```

4. **Database setup**
   ```bash
   # Generate Prisma client
   npm run generate
   
   # Run database migrations
   npm run migrate
   
   # Seed database with sample data
   npm run seed
   ```

5. **Build and start**
   ```bash
   # Development mode
   npm run dev
   
   # Production build
   npm run build
   npm start
   ```

## 🗄️ Database Schema

The application uses the following main entities:

- **User**: User accounts with email verification
- **Account**: Restaurant accounts (each user belongs to one account)
- **Order**: Customer orders with items
- **OrderItem**: Individual items within orders

## 🔐 Authentication Flow

1. **Registration**: `POST /api/auth/register` → Email verification required
2. **Email Verification**: `GET /api/auth/verify-email?token=xxx`
3. **Login**: `POST /api/auth/check-credentials`
4. **Protected Routes**: Include `Authorization: Bearer <token>` header

## 📡 API Endpoints

### Authentication
- `POST /api/auth/register` - Register new user
- `POST /api/auth/check-credentials` - Login user
- `POST /api/auth/check-user` - Check user verification status
- `GET /api/auth/verify-email` - Verify email address
- `POST /api/auth/resend-verification` - Resend verification email

### User Management
- `GET /api/user/profile` - Get user profile
- `PATCH /api/user/profile` - Update user profile
- `POST /api/user/change-password` - Change password

### Account Management
- `PATCH /api/account/update` - Update account (owner only)
- `DELETE /api/account/delete` - Delete account (owner only)

### Order Management
- `GET /api/orders` - Get orders (with optional status filter)
- `POST /api/orders` - Create new order
- `PATCH /api/orders/:id` - Update order status
- `DELETE /api/orders/:id` - Delete order

### Utility
- `GET /api/health` - Health check
- `GET /api/dev/reset-db` - Reset database (development only)

## 🧪 Development

### Scripts
```bash
npm run dev          # Start development server with hot reload
npm run build        # Build TypeScript to JavaScript
npm run start        # Start production server
npm run type-check   # Type check without building
npm run migrate      # Run database migrations
npm run generate     # Generate Prisma client
npm run studio       # Open Prisma Studio
npm run seed         # Seed database with sample data
```

### Sample Login
After seeding the database, you can use:
- **Email**: `demo@restaunax.com`
- **Password**: `password123`

### Development Database Reset
```bash
# Reset entire database
curl http://localhost:3001/api/dev/reset-db

# Reset specific user's data
curl "http://localhost:3001/api/dev/reset-db?userId=USER_ID"
```

## 🚀 Deployment

1. **Build the application**
   ```bash
   npm run build
   ```

2. **Set environment variables**
   - Set `NODE_ENV=production`
   - Configure production database URL
   - Set secure JWT secret
   - Configure production SMTP settings

3. **Run migrations**
   ```bash
   npm run migrate
   ```

4. **Start the application**
   ```bash
   npm start
   ```

## 🔒 Security Features

- **Password Hashing**: bcryptjs with salt rounds
- **Email Verification**: Required before account activation
- **JWT Authentication**: Secure token-based authentication
- **Rate Limiting**: Protection against brute force attacks
- **Input Validation**: Comprehensive request validation
- **CORS**: Configurable cross-origin resource sharing
- **Helmet**: Security headers middleware

## 📁 Project Structure

```
src/
├── middleware/          # Express middleware
│   ├── auth.ts         # Authentication middleware
│   └── errorHandler.ts # Error handling middleware
├── routes/             # API route handlers
│   ├── auth.ts         # Authentication routes
│   ├── user.ts         # User management routes
│   ├── account.ts      # Account management routes
│   ├── orders.ts       # Order management routes
│   ├── health.ts       # Health check routes
│   └── dev.ts          # Development utilities
├── utils/              # Utility functions
│   ├── password.ts     # Password hashing utilities
│   ├── jwt.ts          # JWT token utilities
│   ├── email.ts        # Email sending utilities
│   ├── validation.ts   # Request validation schemas
│   └── seed.ts         # Database seeding utility
├── types/              # TypeScript type definitions
│   └── index.ts        # Common types and interfaces
└── server.ts           # Main application entry point

prisma/
└── schema.prisma       # Database schema definition
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/new-feature`
3. Commit changes: `git commit -am 'Add new feature'`
4. Push to branch: `git push origin feature/new-feature`
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License.

## 🆘 Support

For support and questions:
- Create an issue in the repository
- Contact: support@restaunax.com

---

**Built with ❤️ by RBMarketing1011**

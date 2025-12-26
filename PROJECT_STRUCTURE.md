# Project Structure
## University Bus Tracker System

**Version:** 1.0  
**Date:** December 26, 2025

---

## Table of Contents

1. [Repository Structure Overview](#1-repository-structure-overview)
2. [Backend Project Structure](#2-backend-project-structure)
3. [Student Mobile App Structure](#3-student-mobile-app-structure)
4. [Driver Mobile App Structure](#4-driver-mobile-app-structure)
5. [Admin Dashboard Structure](#5-admin-dashboard-structure)
6. [Shared Libraries](#6-shared-libraries)
7. [Configuration Files](#7-configuration-files)

---

## 1. Repository Structure Overview

### 1.1 Monorepo vs Multi-repo

**Recommended Approach**: **Monorepo** using tools like Turborepo or Nx

**Advantages:**
- Shared code and types between projects
- Unified dependency management
- Single CI/CD pipeline
- Easier code reuse
- Better for coordinated releases

### 1.2 Root Directory Structure

```
university-bus-tracker/
├── apps/
│   ├── backend/              # Node.js backend API
│   ├── student-app/          # React Native student app
│   ├── driver-app/           # React Native driver app
│   └── admin-dashboard/      # React web dashboard
├── packages/
│   ├── shared-types/         # Shared TypeScript types
│   ├── shared-utils/         # Shared utility functions
│   ├── shared-components/    # Shared React components
│   └── api-client/           # Shared API client library
├── infra/
│   ├── docker/               # Docker configurations
│   ├── kubernetes/           # K8s manifests (optional)
│   └── terraform/            # Infrastructure as code (optional)
├── docs/
│   ├── api/                  # API documentation
│   ├── architecture/         # Architecture docs
│   └── user-guides/          # User documentation
├── scripts/
│   ├── setup.sh              # Initial setup script
│   ├── seed-data.ts          # Database seed data
│   └── deploy.sh             # Deployment scripts
├── .github/
│   └── workflows/            # GitHub Actions CI/CD
├── .gitignore
├── package.json              # Root package.json
├── pnpm-workspace.yaml       # pnpm workspaces config
├── turbo.json                # Turborepo config
├── README.md
└── LICENSE
```

---

## 2. Backend Project Structure

### 2.1 Backend Directory Structure

```
apps/backend/
├── src/
│   ├── config/               # Configuration files
│   │   ├── database.ts       # Database config
│   │   ├── redis.ts          # Redis config
│   │   ├── jwt.ts            # JWT config
│   │   └── environment.ts    # Environment variables
│   ├── modules/              # Feature modules
│   │   ├── auth/             # Authentication module
│   │   ├── users/            # User management
│   │   ├── buses/            # Bus management
│   │   ├── routes/           # Route management
│   │   ├── tracking/         # GPS tracking
│   │   ├── passes/           # Bus pass management
│   │   ├── payments/         # Payment processing
│   │   ├── notifications/    # Notification service
│   │   └── analytics/        # Analytics & reports
│   ├── common/               # Shared backend code
│   │   ├── middleware/       # Express middleware
│   │   ├── decorators/       # TypeScript decorators
│   │   ├── guards/           # Auth guards
│   │   ├── filters/          # Exception filters
│   │   ├── interceptors/     # Response interceptors
│   │   ├── pipes/            # Validation pipes
│   │   └── utils/            # Utility functions
│   ├── database/             # Database related
│   │   ├── prisma/           # Prisma schema & migrations
│   │   │   ├── schema.prisma
│   │   │   └── migrations/
│   │   └── seeds/            # Seed data
│   ├── queues/               # Background job queues
│   │   ├── email.queue.ts
│   │   ├── notification.queue.ts
│   │   └── analytics.queue.ts
│   ├── websocket/            # WebSocket handlers
│   │   ├── tracking.gateway.ts
│   │   └── notifications.gateway.ts
│   ├── types/                # TypeScript type definitions
│   │   ├── express.d.ts
│   │   └── custom.types.ts
│   ├── app.ts                # Express app setup
│   ├── server.ts             # Server entry point
│   └── logger.ts             # Logger configuration
├── tests/
│   ├── unit/                 # Unit tests
│   ├── integration/          # Integration tests
│   └── e2e/                  # End-to-end tests
├── .env.example              # Example environment variables
├── .env.development          # Development env
├── .env.test                 # Test env
├── .eslintrc.js              # ESLint configuration
├── .prettierrc               # Prettier configuration
├── tsconfig.json             # TypeScript configuration
├── jest.config.js            # Jest testing configuration
├── package.json
└── README.md
```

### 2.2 Module Structure (Example: Auth Module)

```
src/modules/auth/
├── controllers/
│   └── auth.controller.ts    # Route handlers
├── services/
│   └── auth.service.ts       # Business logic
├── dto/                      # Data Transfer Objects
│   ├── register.dto.ts
│   ├── login.dto.ts
│   └── reset-password.dto.ts
├── entities/                 # Database entities (if not using Prisma)
│   └── user.entity.ts
├── guards/
│   └── jwt-auth.guard.ts     # JWT authentication guard
├── strategies/
│   └── jwt.strategy.ts       # Passport JWT strategy
├── validators/
│   └── auth.validator.ts     # Input validation schemas
├── auth.routes.ts            # Route definitions
└── index.ts                  # Module exports
```

---

## 3. Student Mobile App Structure

```
apps/student-app/
├── android/                  # Android native code
├── ios/                      # iOS native code
├── src/
│   ├── api/                  # API integration
│   │   ├── client.ts         # Axios instance
│   │   ├── auth.api.ts
│   │   ├── tracking.api.ts
│   │   ├── passes.api.ts
│   │   └── payments.api.ts
│   ├── assets/               # Static assets
│   │   ├── images/
│   │   ├── icons/
│   │   └── fonts/
│   ├── components/           # Reusable components
│   │   ├── common/           # Common UI components
│   │   │   ├── Button.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Card.tsx
│   │   │   └── LoadingSpinner.tsx
│   │   ├── map/              # Map related components
│   │   │   ├── BusMap.tsx
│   │   │   ├── BusMarker.tsx
│   │   │   ├── RoutePolyline.tsx
│   │   │   └── StopMarker.tsx
│   │   └── pass/             # Pass related components
│   │       ├── PassCard.tsx
│   │       ├── QRCodeDisplay.tsx
│   │       └── PassHistory.tsx
│   ├── hooks/                # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useBusTracking.ts
│   │   ├── useLocation.ts
│   │   └── useWebSocket.ts
│   ├── navigation/           # Navigation configuration
│   │   ├── AppNavigator.tsx
│   │   ├── AuthNavigator.tsx
│   │   └── MainNavigator.tsx
│   ├── screens/              # Screen components
│   │   ├── auth/
│   │   │   ├── LoginScreen.tsx
│   │   │   ├── RegisterScreen.tsx
│   │   │   └── ForgotPasswordScreen.tsx
│   │   ├── home/
│   │   │   ├── HomeScreen.tsx
│   │   │   └── MapScreen.tsx
│   │   ├── passes/
│   │   │   ├── PassListScreen.tsx
│   │   │   ├── PurchasePassScreen.tsx
│   │   │   └── PassDetailScreen.tsx
│   │   ├── routes/
│   │   │   ├── RoutesListScreen.tsx
│   │   │   └── RouteDetailScreen.tsx
│   │   └── profile/
│   │       ├── ProfileScreen.tsx
│   │       └── SettingsScreen.tsx
│   ├── redux/                # Redux state management
│   │   ├── store.ts          # Redux store configuration
│   │   ├── slices/           # Redux slices
│   │   │   ├── authSlice.ts
│   │   │   ├── busSlice.ts
│   │   │   ├── passSlice.ts
│   │   │   └── notificationSlice.ts
│   │   └── middleware/       # Custom middleware
│   ├── services/             # Business logic services
│   │   ├── websocket.service.ts
│   │   ├── notification.service.ts
│   │   └── location.service.ts
│   ├── types/                # TypeScript types
│   │   ├── user.types.ts
│   │   ├── bus.types.ts
│   │   ├── pass.types.ts
│   │   └── navigation.types.ts
│   ├── utils/                # Utility functions
│   │   ├── validation.ts
│   │   ├── formatting.ts
│   │   ├── storage.ts
│   │   └── constants.ts
│   ├── theme/                # Theme configuration
│   │   ├── colors.ts
│   │   ├── typography.ts
│   │   └── spacing.ts
│   ├── App.tsx               # Root component
│   └── index.tsx             # Entry point
├── __tests__/                # Tests
├── .env.example
├── .eslintrc.js
├── .prettierrc
├── tsconfig.json
├── babel.config.js
├── metro.config.js
├── package.json
├── app.json
└── README.md
```

---

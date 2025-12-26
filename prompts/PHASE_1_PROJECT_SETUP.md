# Phase 1: Project Setup & Foundation
## University Bus Tracker System

**Duration:** 1-2 weeks  
**Status:** 🟡 Not Started  
**Priority:** 🔴 Critical

---

## 🎯 Goal

Establish the project foundation, development environment, and core infrastructure. Set up version control, CI/CD pipelines, and basic project structure for all applications.

---

## 📋 Key Features / Tasks

### 1. Repository & Version Control Setup
- [ ] Create GitHub repository (monorepo structure)
- [ ] Set up branch protection rules (main, staging, develop)
- [ ] Configure .gitignore files
- [ ] Set up Git hooks (Husky for pre-commit)
- [ ] Create initial README with setup instructions

### 2. Development Environment Setup
- [ ] Install Node.js 20 LTS
- [ ] Install Docker & Docker Compose
- [ ] Install PostgreSQL 15+ and Redis 7+
- [ ] Set up IDE (VS Code) with recommended extensions
- [ ] Configure ESLint and Prettier

### 3. Project Structure Creation
- [ ] Create monorepo structure (apps/, packages/, infra/)
- [ ] Initialize package.json files for all projects
- [ ] Set up Turborepo or npm workspaces
- [ ] Configure TypeScript for all projects
- [ ] Create shared packages (shared-types, shared-utils, api-client)

### 4. Backend Foundation
- [ ] Initialize Express.js application
- [ ] Set up TypeScript configuration
- [ ] Install core dependencies (express, cors, helmet, etc.)
- [ ] Create basic folder structure (modules, config, common)
- [ ] Set up environment variable management (.env files)
- [ ] Configure Winston logger
- [ ] Create health check endpoint

### 5. Database Setup
- [ ] Install and configure Prisma
- [ ] Create initial Prisma schema (users, roles tables)
- [ ] Set up PostgreSQL with PostGIS extension
- [ ] Set up Redis for caching
- [ ] Create Docker Compose for local development
- [ ] Run initial migrations
- [ ] Create seed data script

### 6. Mobile Apps Foundation
- [ ] Initialize React Native projects (student-app, driver-app)
- [ ] Configure TypeScript
- [ ] Set up navigation (React Navigation)
- [ ] Set up state management (Redux Toolkit)
- [ ] Configure Metro bundler
- [ ] Set up Android and iOS configurations
- [ ] Create basic folder structure

### 7. Admin Dashboard Foundation
- [ ] Initialize React.js project with Vite
- [ ] Configure TypeScript
- [ ] Set up React Router
- [ ] Set up Redux Toolkit
- [ ] Install Material-UI or Ant Design
- [ ] Create basic layout structure

### 8. CI/CD Pipeline Setup
- [ ] Create GitHub Actions workflows
- [ ] Set up linting workflow
- [ ] Set up testing workflow
- [ ] Configure code coverage reporting
- [ ] Set up Docker image build workflow

### 9. Documentation
- [ ] Create CONTRIBUTING.md
- [ ] Create CODE_OF_CONDUCT.md
- [ ] Document local setup process
- [ ] Create .env.example files for all projects
- [ ] Document git workflow and branching strategy

### 10. Third-Party Services Setup
- [ ] Create Mapbox account and get access token
- [ ] Create Firebase project for FCM
- [ ] Set up KuickPay test account
- [ ] Create SendGrid account for emails
- [ ] Document all API keys in .env.example

---

## 📦 Deliverables

### Code Deliverables
- ✅ Complete monorepo structure with all projects initialized
- ✅ Working local development environment (Docker Compose)
- ✅ Backend API with health check endpoint
- ✅ Mobile apps running on emulator/simulator
- ✅ Admin dashboard running on localhost
- ✅ Database with initial schema migrated
- ✅ CI/CD pipeline running successfully

### Documentation Deliverables
- ✅ Updated README.md with setup instructions
- ✅ CONTRIBUTING.md guide
- ✅ Environment variable documentation
- ✅ Git workflow documentation

### Infrastructure Deliverables
- ✅ Docker Compose configuration
- ✅ Database migrations
- ✅ Redis configuration
- ✅ GitHub Actions workflows

---

## 🔧 Technical Setup

### Docker Compose Configuration

```yaml
version: '3.8'

services:
  postgres:
    image: postgis/postgis:15-3.3-alpine
    environment:
      POSTGRES_DB: bus_tracker_dev
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  backend:
    build:
      context: ./apps/backend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://postgres:postgres@postgres:5432/bus_tracker_dev
      REDIS_URL: redis://redis:6379
    volumes:
      - ./apps/backend:/app
      - /app/node_modules
    depends_on:
      - postgres
      - redis
    command: npm run dev

volumes:
  postgres_data:
  redis_data:
```

---

## 🚀 Execution Prompts

### Prompt 1: Initialize Monorepo Structure
```
Create a monorepo structure for the University Bus Tracker System with the following:
- Root package.json with workspaces configuration
- Three main apps: backend (Node.js/Express), student-app (React Native), driver-app (React Native), admin-dashboard (React.js)
- Shared packages: shared-types, shared-utils, api-client
- Infrastructure folder with Docker configurations
- Proper .gitignore files
- Configure Turborepo or npm workspaces for efficient builds
```

### Prompt 2: Set Up Backend Foundation
```
Set up the backend API for University Bus Tracker with:
- Express.js application with TypeScript
- Folder structure: modules/, config/, common/, database/
- Environment variable management with dotenv
- Winston logger configuration
- Helmet for security headers
- CORS configuration
- Rate limiting middleware
- Health check endpoint at GET /health
- Error handling middleware
- Request validation with Joi or Zod
```

### Prompt 3: Configure Database with Prisma
```
Set up PostgreSQL database with Prisma ORM:
- Install Prisma and @prisma/client
- Create prisma.schema with initial models: User, Role, UserRole, Profile
- Configure PostgreSQL connection
- Enable PostGIS extension for geospatial data
- Create initial migration
- Set up seed script with sample data (roles: student, driver, admin)
- Configure TimescaleDB extension for time-series data
```

### Prompt 4: Initialize Mobile Apps
```
Create React Native applications (student-app and driver-app) with:
- TypeScript configuration
- React Navigation 6.x for navigation
- Redux Toolkit for state management
- Folder structure: screens/, components/, navigation/, redux/, api/, hooks/, utils/
- Basic splash screen and login screen
- Theme configuration (colors, typography, spacing)
- Configure Metro bundler
- Set up both iOS and Android configurations
```

### Prompt 5: Initialize Admin Dashboard
```
Create React.js admin dashboard with:
- Vite as build tool
- TypeScript configuration
- React Router for routing
- Redux Toolkit for state management
- Material-UI (MUI) or Ant Design for UI components
- Folder structure: pages/, components/, redux/, routes/, api/
- Basic layout with sidebar and header
- Login page and dashboard page
- Theme configuration
```

### Prompt 6: Set Up Docker Development Environment
```
Create Docker Compose configuration for local development:
- PostgreSQL 15 with PostGIS extension
- Redis 7 for caching
- Backend API service with hot reload
- Proper volume mounting for persistence
- Network configuration for service communication
- Health checks for all services
- Environment variable configuration
```

### Prompt 7: Configure CI/CD Pipeline
```
Set up GitHub Actions workflows for:
1. Linting workflow (runs on every push)
   - ESLint check for all TypeScript files
   - Prettier format check
2. Testing workflow (runs on PR to main/staging)
   - Run unit tests with Jest
   - Generate coverage report
   - Upload coverage to Codecov
3. Build workflow (runs on PR)
   - Build backend
   - Build admin dashboard
   - Check for build errors
4. Docker build workflow (runs on main branch)
   - Build Docker images
   - Push to container registry
```

### Prompt 8: Create Shared Packages
```
Create shared packages for the monorepo:

1. shared-types package:
   - User types (User, Student, Driver, Admin)
   - API request/response types
   - Common enums (UserRole, BusStatus, PassType, etc.)
   - Export all types from index.ts

2. shared-utils package:
   - Validation utilities (email, phone, password)
   - Formatting utilities (date, currency, distance)
   - Constants (API_BASE_URL, roles, status codes)
   - Helper functions

3. api-client package:
   - Axios instance with interceptors
   - API methods for all endpoints
   - Error handling
   - Token management
```

---

## ✅ Acceptance Criteria

Phase 1 is complete when:

- [ ] All team members can clone repo and run locally
- [ ] `docker-compose up` starts all services successfully
- [ ] Backend health check endpoint returns 200 OK
- [ ] Database migrations run successfully
- [ ] Student app builds and runs on Android emulator
- [ ] Driver app builds and runs on Android emulator
- [ ] Admin dashboard runs on http://localhost:3001
- [ ] All GitHub Actions workflows pass
- [ ] Documentation is clear and complete
- [ ] All environment variables documented in .env.example

---

## 🧪 Testing Checklist

- [ ] Backend server starts without errors
- [ ] Database connection successful
- [ ] Redis connection successful
- [ ] Health check endpoint responds
- [ ] Mobile apps run on emulator/simulator
- [ ] Admin dashboard loads in browser
- [ ] Hot reload works in all applications
- [ ] Docker containers restart properly
- [ ] Git hooks run on commit

---

## 📊 Success Metrics

- **Setup Time:** < 30 minutes for new developer
- **Build Time:** < 2 minutes for backend
- **Docker Startup:** < 1 minute
- **Test Coverage:** N/A (no features yet)
- **CI/CD Pipeline:** All workflows pass

---

## 🔄 Next Phase

After completing Phase 1, proceed to:
➡️ **Phase 2: Authentication & User Management**

---

## 📝 Notes

- Ensure all team members have the same Node.js version (20 LTS)
- Document any OS-specific setup steps
- Keep Docker images lightweight
- Use .env files, never commit secrets
- Set up branch protection on main and staging branches

---

**Phase Owner:** Tech Lead  
**Created:** December 26, 2025  
**Last Updated:** December 26, 2025

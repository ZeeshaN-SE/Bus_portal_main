# University Bus Tracker System
## Complete Architecture & Documentation

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/Node.js-20.x-green.svg)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-18.x-blue.svg)](https://reactjs.org/)
[![React Native](https://img.shields.io/badge/React%20Native-0.73-blue.svg)](https://reactnative.dev/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue.svg)](https://www.postgresql.org/)

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Features](#-features)
- [Architecture](#-architecture)
- [Technology Stack](#-technology-stack)
- [Documentation](#-documentation)
- [Getting Started](#-getting-started)
- [Project Structure](#-project-structure)
- [Development](#-development)
- [Deployment](#-deployment)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🎯 Project Overview

The **University Bus Tracker System** is a comprehensive real-time tracking and management platform designed to revolutionize university bus operations. This Final Year Project (FYP) provides a complete solution for:

- **Students** - Track buses in real-time, purchase digital passes, receive notifications
- **Drivers** - Manage trips, scan QR codes, report delays
- **Administrators** - Monitor operations, manage resources, generate analytics

### Key Objectives

✅ Enable real-time GPS tracking of all university buses  
✅ Implement a digital bus pass system with QR code verification  
✅ Integrate secure payment processing via KuickPay  
✅ Provide role-based access for different user types  
✅ Deliver automated notifications for bus arrivals and updates  
✅ Generate comprehensive analytics and reports  

### Target Users

- **Students:** 5,000+ potential users
- **Bus Drivers:** 20-50 active drivers
- **Administrators:** 5-10 admin users

---

## ✨ Features

### For Students
- 📍 **Real-Time Bus Tracking** - View live bus locations on interactive maps
- 🎫 **Digital Bus Passes** - Purchase and store passes with QR codes
- 💳 **Secure Payments** - Integrated KuickPay payment gateway
- 🔔 **Smart Notifications** - Bus arrival alerts, pass expiry reminders
- 🗺️ **Route Information** - View all routes, stops, and schedules
- 📊 **Pass History** - Track all purchases and transactions

### For Drivers
- 🚌 **Trip Management** - Start, track, and end trips easily
- 📱 **QR Scanner** - Quick and offline-capable pass verification
- 🗺️ **Route Navigation** - Built-in route guidance
- ⏰ **Delay Reporting** - Notify students of delays in real-time
- 📈 **Trip History** - View past trips and performance stats

### For Administrators
- 📊 **Dashboard** - Real-time overview of all operations
- 👥 **User Management** - Manage students, drivers, and admins
- 🚍 **Bus Management** - Track buses, maintenance, and assignments
- 🛣️ **Route Management** - Create and modify routes and stops
- 💰 **Financial Reports** - Revenue analytics and payment tracking
- 📈 **Analytics** - Usage patterns, performance metrics, insights

---

## 🏗️ Architecture

### High-Level Architecture

```
┌─────────────────┐  ┌─────────────┐  ┌─────────────────┐
│  Student App    │  │ Driver App  │  │ Admin Dashboard │
│ (React Native)  │  │(React Native)│  │   (React.js)    │
└────────┬────────┘  └──────┬──────┘  └────────┬────────┘
         │                  │                   │
         └──────────────────┴───────────────────┘
                            │
                     HTTPS / WebSocket
                            │
         ┌──────────────────┴───────────────────┐
         │      API Gateway / Load Balancer      │
         └──────────────────┬───────────────────┘
                            │
         ┌──────────────────┴───────────────────┐
         │         Backend Services              │
         │  (Node.js + Express + Socket.io)      │
         └──────────────────┬───────────────────┘
                            │
         ┌──────────────────┴───────────────────┐
         │          Data Layer                   │
         │  PostgreSQL | Redis | TimescaleDB     │
         └───────────────────────────────────────┘
```

### Key Design Principles

- **Microservices Architecture** - Modular, scalable services
- **API-First Design** - RESTful APIs with WebSocket support
- **Real-Time First** - Live GPS updates and instant notifications
- **Mobile-First** - Optimized for mobile user experience
- **Cloud-Native** - Designed for AWS/Cloud deployment
- **Security by Design** - End-to-end encryption and authentication

---

## 💻 Technology Stack

### Frontend

#### Mobile Applications (Student & Driver)
- **Framework:** React Native 0.73+
- **Language:** TypeScript
- **State Management:** Redux Toolkit
- **Navigation:** React Navigation 6.x
- **Maps:** Mapbox / react-native-mapbox-gl
- **QR Code:** react-native-vision-camera + QR scanner
- **Notifications:** Firebase Cloud Messaging (FCM)
- **HTTP Client:** Axios + React Query

#### Admin Web Dashboard
- **Framework:** React 18+ with TypeScript
- **Build Tool:** Vite
- **UI Library:** Material-UI (MUI) / Ant Design
- **State Management:** Redux Toolkit + React Query
- **Charts:** Recharts
- **Tables:** AG Grid
- **Maps:** react-map-gl (Mapbox GL JS)

### Backend

- **Runtime:** Node.js 20 LTS
- **Framework:** Express.js
- **Language:** TypeScript
- **Real-Time:** Socket.io (WebSocket)
- **Authentication:** JWT (JSON Web Tokens)
- **Validation:** Joi / Zod
- **Logging:** Winston
- **Task Queue:** Bull (Redis-based)

### Database & Storage

- **Primary Database:** PostgreSQL 15+
  - With PostGIS (geospatial queries)
  - With TimescaleDB (time-series GPS data)
- **Cache:** Redis 7+
- **ORM:** Prisma
- **File Storage:** AWS S3 / Google Cloud Storage

### Third-Party Services

- **Payment Gateway:** KuickPay
- **Maps:** Mapbox
- **Push Notifications:** Firebase Cloud Messaging
- **Email:** SendGrid / AWS SES
- **SMS (Optional):** Twilio

### DevOps & Infrastructure

- **Cloud Platform:** AWS (recommended) / GCP / Azure
- **Containerization:** Docker + Docker Compose
- **CI/CD:** GitHub Actions
- **Monitoring:** CloudWatch, Sentry, New Relic
- **Logging:** ELK Stack / CloudWatch Logs

---

## 📚 Documentation

This project includes comprehensive documentation covering all aspects:

### Core Documentation

| Document | Description |
|----------|-------------|
| **[ARCHITECTURE.md](./ARCHITECTURE.md)** | Complete system architecture, design principles, and technical decisions |
| **[TECH_STACK.md](./TECH_STACK.md)** | Detailed technology stack with justifications and package versions |
| **[PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md)** | Project organization, folder structure, and naming conventions |
| **[DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md)** | Database design, tables, relationships, indexes, and sample data |
| **[API_ENDPOINTS.md](./API_ENDPOINTS.md)** | Complete API reference with request/response examples |
| **[DEPLOYMENT.md](./DEPLOYMENT.md)** | Deployment architecture, CI/CD, monitoring, and scaling strategies |

### Additional Documentation

| Document | Description |
|----------|-------------|
| **[new_PRD.md](./new_PRD.md)** | Product Requirements Document with detailed feature specifications |
| **[kuickpay_integration_guide.txt](./kuickpay_integration_guide.txt)** | KuickPay payment gateway integration guide |

---

## 🚀 Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** 20 LTS or higher
- **npm** or **yarn** or **pnpm**
- **Docker** and **Docker Compose**
- **Git**
- **PostgreSQL** 15+ (or use Docker)
- **Redis** 7+ (or use Docker)

For mobile development:
- **Android Studio** (for Android)
- **Xcode** (for iOS - macOS only)

### Quick Start with Docker

```bash
# Clone the repository
git clone https://github.com/your-org/university-bus-tracker.git
cd university-bus-tracker

# Start all services with Docker Compose
docker-compose up -d

# View logs
docker-compose logs -f

# The services will be available at:
# - Backend API: http://localhost:3000
# - Admin Dashboard: http://localhost:3001
# - PostgreSQL: localhost:5432
# - Redis: localhost:6379
```

### Manual Setup

#### 1. Clone and Install Dependencies

```bash
# Clone repository
git clone https://github.com/your-org/university-bus-tracker.git
cd university-bus-tracker

# Install root dependencies (if using monorepo)
npm install

# Install backend dependencies
cd apps/backend
npm install

# Install admin dashboard dependencies
cd ../admin-dashboard
npm install

# Install mobile app dependencies
cd ../student-app
npm install
```

#### 2. Setup Environment Variables

```bash
# Backend (.env)
cd apps/backend
cp .env.example .env

# Edit .env with your configuration
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://postgres:password@localhost:5432/bus_tracker
REDIS_URL=redis://localhost:6379
JWT_SECRET=your-secret-key
MAPBOX_ACCESS_TOKEN=your-mapbox-token
```

#### 3. Setup Database

```bash
# Run Prisma migrations
cd apps/backend
npx prisma migrate dev

# Seed database with sample data
npm run seed
```

#### 4. Start Development Servers

```bash
# Terminal 1 - Backend API
cd apps/backend
npm run dev

# Terminal 2 - Admin Dashboard
cd apps/admin-dashboard
npm run dev

# Terminal 3 - Student Mobile App
cd apps/student-app
npm run android  # or npm run ios
```

### Access the Applications

- **Backend API:** http://localhost:3000
- **API Documentation:** http://localhost:3000/api-docs (Swagger)
- **Admin Dashboard:** http://localhost:3001
- **Mobile Apps:** Running on emulator/device

### Default Login Credentials

```
Admin:
Email: admin@university.edu
Password: Admin123!

Student:
Email: student@university.edu
Password: Student123!

Driver:
Email: driver@university.edu
Password: Driver123!
```

---

## 📁 Project Structure

```
university-bus-tracker/
├── apps/
│   ├── backend/              # Node.js backend API
│   │   ├── src/
│   │   │   ├── modules/      # Feature modules
│   │   │   ├── config/       # Configuration
│   │   │   ├── database/     # Prisma schema & migrations
│   │   │   └── websocket/    # WebSocket handlers
│   │   ├── tests/            # Tests
│   │   └── package.json
│   │
│   ├── student-app/          # React Native student app
│   │   ├── src/
│   │   │   ├── screens/      # Screen components
│   │   │   ├── components/   # Reusable components
│   │   │   ├── navigation/   # Navigation config
│   │   │   ├── redux/        # Redux state
│   │   │   └── api/          # API integration
│   │   └── package.json
│   │
│   ├── driver-app/           # React Native driver app
│   │   └── (similar structure)
│   │
│   └── admin-dashboard/      # React web dashboard
│       ├── src/
│       │   ├── pages/        # Page components
│       │   ├── components/   # Reusable components
│       │   ├── redux/        # Redux state
│       │   └── api/          # API integration
│       └── package.json
│
├── packages/                 # Shared packages (monorepo)
│   ├── shared-types/         # TypeScript types
│   ├── shared-utils/         # Utility functions
│   └── api-client/           # API client library
│
├── infra/                    # Infrastructure as code
│   ├── docker/               # Docker configs
│   └── terraform/            # Terraform scripts (optional)
│
├── docs/                     # Documentation
│   ├── api/                  # API docs
│   └── architecture/         # Architecture diagrams
│
├── scripts/                  # Utility scripts
│   ├── setup.sh              # Setup script
│   ├── seed-data.ts          # Database seeding
│   └── deploy.sh             # Deployment script
│
├── .github/
│   └── workflows/            # GitHub Actions CI/CD
│
├── ARCHITECTURE.md           # Architecture documentation
├── TECH_STACK.md             # Technology stack details
├── PROJECT_STRUCTURE.md      # Project structure guide
├── DATABASE_SCHEMA.md        # Database schema
├── API_ENDPOINTS.md          # API documentation
├── DEPLOYMENT.md             # Deployment guide
├── README.md                 # This file
├── docker-compose.yml        # Docker compose config
├── package.json              # Root package.json
└── LICENSE                   # License file
```

See [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) for detailed information.

---

## 🛠️ Development

### Development Workflow

1. **Create a new branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**
   - Follow the coding standards
   - Write tests for new features
   - Update documentation

3. **Run tests**
   ```bash
   npm run test
   npm run test:coverage
   ```

4. **Lint and format**
   ```bash
   npm run lint
   npm run format
   ```

5. **Commit your changes**
   ```bash
   git commit -m "feat: add new feature"
   ```

6. **Push and create PR**
   ```bash
   git push origin feature/your-feature-name
   ```

### Commit Convention

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add new feature
fix: resolve bug
docs: update documentation
style: code formatting
refactor: code refactoring
test: add tests
chore: maintenance tasks
```

### Code Quality

**Linting:**
```bash
npm run lint        # Run ESLint
npm run lint:fix    # Fix linting issues
```

**Testing:**
```bash
npm run test              # Run all tests
npm run test:watch        # Watch mode
npm run test:coverage     # Coverage report
npm run test:e2e          # E2E tests
```

**Type Checking:**
```bash
npm run type-check        # TypeScript type checking
```

---

## 🚢 Deployment

### Deployment Environments

| Environment | URL | Purpose |
|-------------|-----|---------|
| **Development** | localhost | Local development |
| **Staging** | staging.bus-tracker.edu | Pre-production testing |
| **Production** | bus-tracker.university.edu | Live system |

### Deploy to Staging

```bash
# Merge to staging branch
git checkout staging
git merge develop
git push origin staging

# GitHub Actions will automatically deploy
```

### Deploy to Production

```bash
# Merge to main branch (requires approval)
git checkout main
git merge staging
git push origin main

# Manual approval required in GitHub Actions
```

### Quick Deploy Commands

```bash
# Deploy backend only
npm run deploy:backend

# Deploy admin dashboard only
npm run deploy:admin

# Deploy all services
npm run deploy:all

# Rollback to previous version
npm run rollback
```

For detailed deployment instructions, see [DEPLOYMENT.md](./DEPLOYMENT.md).

---

## 📊 Monitoring & Maintenance

### Health Checks

```bash
# Check API health
curl https://api.university-bus-tracker.com/health

# Check database connection
curl https://api.university-bus-tracker.com/health/db

# Check Redis connection
curl https://api.university-bus-tracker.com/health/redis
```

### Monitoring Dashboards

- **CloudWatch Dashboard:** Monitor AWS resources
- **Application Dashboard:** Monitor app performance
- **Sentry:** Error tracking and crash reports
- **New Relic:** APM and performance insights

### Logs

```bash
# View backend logs
docker-compose logs -f backend

# View all logs
docker-compose logs -f

# AWS CloudWatch logs
aws logs tail /aws/bus-tracker/backend --follow
```

---

## 🧪 Testing

### Test Coverage Goals

- **Unit Tests:** >80% coverage
- **Integration Tests:** All API endpoints
- **E2E Tests:** Critical user flows

### Run Tests

```bash
# Backend tests
cd apps/backend
npm run test
npm run test:coverage

# Frontend tests
cd apps/admin-dashboard
npm run test

# Mobile tests
cd apps/student-app
npm run test

# E2E tests
npm run test:e2e
```

---

## 🤝 Contributing

We welcome contributions! Please follow these guidelines:

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/AmazingFeature`)
3. **Commit your changes** (`git commit -m 'feat: add AmazingFeature'`)
4. **Push to the branch** (`git push origin feature/AmazingFeature`)
5. **Open a Pull Request**

### Code Review Process

- All PRs require at least 1 approval
- All tests must pass
- Code coverage must not decrease
- Follow coding standards and conventions

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👥 Team

**Project Type:** Final Year Project (FYP)  
**University:** [Your University Name]  
**Department:** Computer Science  
**Year:** 2025

**Team Members:**
- Student 1 - [GitHub](https://github.com/student1)
- Student 2 - [GitHub](https://github.com/student2)
- Student 3 - [GitHub](https://github.com/student3)

**Supervisor:** Dr. [Supervisor Name]

---

## 🙏 Acknowledgments

- University administration for project support
- KuickPay for payment gateway integration
- Mapbox for mapping services
- Open-source community for amazing tools and libraries

---

## 📞 Support

For questions, issues, or support:

- **Email:** support@university-bus-tracker.com
- **Documentation:** [Full Documentation](./docs)
- **Issue Tracker:** [GitHub Issues](https://github.com/your-org/university-bus-tracker/issues)

---

## 🗺️ Roadmap

### Phase 1 (MVP) - ✅ Completed
- [x] User authentication and authorization
- [x] Real-time bus tracking
- [x] Digital bus pass system
- [x] Payment integration
- [x] Admin dashboard
- [x] Driver interface

### Phase 2 (Enhancement) - 🚧 In Progress
- [ ] AI-powered ETA prediction
- [ ] Route optimization algorithms
- [ ] Parent dashboard
- [ ] Integration with university LMS
- [ ] Advanced analytics

### Phase 3 (Scale) - 📅 Planned
- [ ] Multi-university support
- [ ] Native mobile apps (Swift/Kotlin)
- [ ] Progressive Web App (PWA)
- [ ] Machine learning insights
- [ ] IoT integration

---

## ⭐ Star History

If you find this project useful, please consider giving it a star ⭐

---

**Built with ❤️ by [Your Team Name]**

*Last Updated: December 26, 2025*

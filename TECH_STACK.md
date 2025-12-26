# Technology Stack
## University Bus Tracker System

**Version:** 1.0  
**Date:** December 26, 2025

---

## Table of Contents

1. [Technology Stack Overview](#1-technology-stack-overview)
2. [Frontend Technologies](#2-frontend-technologies)
3. [Backend Technologies](#3-backend-technologies)
4. [Database & Storage](#4-database--storage)
5. [Third-Party Services](#5-third-party-services)
6. [DevOps & Infrastructure](#6-devops--infrastructure)
7. [Development Tools](#7-development-tools)
8. [Technology Justifications](#8-technology-justifications)

---

## 1. Technology Stack Overview

### Tech Stack at a Glance

```
┌─────────────────────────────────────────────────────────┐
│                    FRONTEND LAYER                       │
├──────────────────┬──────────────────┬──────────────────┤
│  Student App     │   Driver App     │  Admin Dashboard │
│  React Native    │   React Native   │    React.js      │
│  TypeScript      │   TypeScript     │   TypeScript     │
└──────────────────┴──────────────────┴──────────────────┘
                            │
┌───────────────────────────┴─────────────────────────────┐
│                    BACKEND LAYER                        │
│  Node.js + Express.js + TypeScript                      │
│  Socket.io (WebSocket)                                  │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────┴──────────────────────────────────────┐
│                   DATA LAYER                            │
│  PostgreSQL + Redis + TimescaleDB + AWS S3              │
└──────────────────┬──────────────────────────────────────┘
                   │
┌──────────────────┴──────────────────────────────────────┐
│               THIRD-PARTY SERVICES                      │
│  KuickPay | Mapbox | FCM | SendGrid                    │
└─────────────────────────────────────────────────────────┘
```

---

## 2. Frontend Technologies

### 2.1 Student Mobile App

| Technology | Version | Purpose |
|------------|---------|---------|
| **React Native** | 0.73+ | Cross-platform mobile framework |
| **TypeScript** | 5.0+ | Type-safe JavaScript |
| **React Navigation** | 6.x | Navigation and routing |
| **Redux Toolkit** | 2.0+ | State management |
| **Redux Persist** | 6.0+ | Persist state across sessions |
| **React Query** | 5.0+ | Server state management & caching |
| **Axios** | 1.6+ | HTTP client |
| **Socket.io Client** | 4.6+ | Real-time WebSocket communication |
| **@rnmapbox/maps** | 10.0+ | Mapbox maps integration |
| **react-native-qrcode-svg** | 6.2+ | QR code generation |
| **react-native-vision-camera** | 3.6+ | Camera for QR scanning |
| **vision-camera-code-scanner** | 0.2+ | QR code scanner plugin |
| **@react-native-firebase/messaging** | 19.0+ | Push notifications |
| **react-native-geolocation-service** | 5.3+ | GPS location services |
| **AsyncStorage** | 1.21+ | Local storage |
| **react-native-mmkv** | 2.11+ | Fast key-value storage (alternative) |
| **React Native Paper** | 5.11+ | Material Design UI components |
| **react-native-vector-icons** | 10.0+ | Icon library |
| **date-fns** | 3.0+ | Date manipulation |
| **React Hook Form** | 7.49+ | Form handling |
| **Yup** | 1.3+ | Schema validation |

**Build Tools:**
- Metro Bundler (default React Native)
- Babel for transpilation
- ESLint + Prettier for code quality

---

### 2.2 Driver Mobile App

**Same stack as Student App** with additional:

| Technology | Version | Purpose |
|------------|---------|---------|
| **react-native-background-geolocation** | 4.13+ | Background GPS tracking |
| **react-native-background-timer** | 2.4+ | Background task execution |

---

### 2.3 Admin Web Dashboard

| Technology | Version | Purpose |
|------------|---------|---------|
| **React** | 18.2+ | UI library |
| **TypeScript** | 5.0+ | Type safety |
| **Vite** | 5.0+ | Build tool (faster than CRA) |
| **React Router** | 6.x | Client-side routing |
| **Redux Toolkit** | 2.0+ | State management |
| **React Query** | 5.0+ | Server state & caching |
| **Material-UI (MUI)** | 5.15+ | Component library |
| **Ant Design** | 5.12+ | Alternative UI framework |
| **Recharts** | 2.10+ | Charts and graphs |
| **AG Grid** | 31.0+ | Advanced data tables |
| **React Hook Form** | 7.49+ | Form management |
| **Yup** | 1.3+ | Validation |
| **react-map-gl** | 7.1+ | Mapbox GL JS for React |
| **Axios** | 1.6+ | HTTP client |
| **Socket.io Client** | 4.6+ | Real-time updates |
| **date-fns** | 3.0+ | Date utilities |
| **jsPDF** | 2.5+ | PDF generation |
| **xlsx** | 0.18+ | Excel export |
| **lodash** | 4.17+ | Utility functions |

**Build & Dev Tools:**
- Vite (build tool)
- ESLint + Prettier
- TypeScript compiler

---

## 3. Backend Technologies

### 3.1 Core Backend Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| **Node.js** | 20 LTS | JavaScript runtime |
| **Express.js** | 4.18+ | Web framework |
| **TypeScript** | 5.0+ | Type-safe development |
| **Socket.io** | 4.6+ | WebSocket server |
| **Prisma** | 5.8+ | ORM for PostgreSQL |
| **JWT (jsonwebtoken)** | 9.0+ | Authentication tokens |
| **bcrypt** | 5.1+ | Password hashing |
| **Joi / Zod** | Latest | Input validation |
| **Winston** | 3.11+ | Logging framework |
| **Morgan** | 1.10+ | HTTP request logging |
| **Helmet** | 7.1+ | Security headers |
| **CORS** | 2.8+ | Cross-origin resource sharing |
| **express-rate-limit** | 7.1+ | Rate limiting |
| **dotenv** | 16.3+ | Environment variables |
| **nodemailer** | 6.9+ | Email sending |
| **axios** | 1.6+ | HTTP client for external APIs |
| **ioredis** | 5.3+ | Redis client |
| **bull** | 4.12+ | Job queue with Redis |
| **node-cron** | 3.0+ | Scheduled tasks |
| **multer** | 1.4+ | File upload handling |
| **sharp** | 0.33+ | Image processing |
| **uuid** | 9.0+ | Unique ID generation |
| **moment** / **date-fns** | Latest | Date/time handling |

### 3.2 Testing Libraries

| Technology | Purpose |
|------------|---------|
| **Jest** | Unit & integration testing |
| **Supertest** | HTTP API testing |
| **ts-jest** | TypeScript support for Jest |
| **@faker-js/faker** | Test data generation |

### 3.3 API Documentation

| Technology | Purpose |
|------------|---------|
| **Swagger / OpenAPI** | API documentation |
| **swagger-jsdoc** | Generate docs from code |
| **swagger-ui-express** | Interactive API docs UI |

---

## 4. Database & Storage

### 4.1 Primary Database

**PostgreSQL 15+**
- **Why**: ACID compliance, robust, excellent for relational data
- **Client Library**: pg (node-postgres)
- **ORM**: Prisma
- **Extensions**: 
  - PostGIS (geospatial queries)
  - TimescaleDB (time-series data for GPS history)

### 4.2 Cache Layer

**Redis 7+**
- **Why**: In-memory speed, pub/sub, perfect for sessions and caching
- **Client Library**: ioredis
- **Usage**:
  - Session storage
  - Real-time GPS positions cache
  - Rate limiting
  - Job queue (Bull)

### 4.3 File Storage

**AWS S3 / Google Cloud Storage / MinIO**
- Profile images
- Bus documents
- Payment receipts
- Generated reports (PDF, CSV)
- **SDK**: aws-sdk / @aws-sdk/client-s3

---

## 5. Third-Party Services

### 5.1 Payment Gateway

**KuickPay**
- Integration: REST API
- Webhook handling for payment confirmations
- Custom SDK or direct HTTP integration

### 5.2 Maps & Geolocation

**Mapbox**
- Mapbox GL JS
- Maps SDK for iOS/Android
- Geocoding API
- Directions API
- Navigation SDK
- Static Images API
- **Pricing**: Free tier with 50,000 map loads/month, 100,000 geocoding requests/month

**Why Mapbox:**
- More cost-effective than Google Maps
- Highly customizable map styling with Mapbox Studio
- Better offline capabilities
- Modern WebGL rendering for smooth performance

### 5.3 Push Notifications

**Firebase Cloud Messaging (FCM)**
- Free push notifications for iOS and Android
- Admin SDK for backend
- Client SDK for mobile apps

### 5.4 Email Service

**SendGrid**
- Transactional email API
- Email templates
- Delivery tracking
- Free tier: 100 emails/day

**Alternative**: AWS SES
- Lower cost for high volume
- Requires more setup

### 5.5 SMS (Optional)

**Twilio**
- SMS notifications
- Pay-per-message pricing

**Alternative**: AWS SNS
- Integrated with AWS ecosystem

---

## 6. DevOps & Infrastructure

### 6.1 Version Control

**Git + GitHub**
- Git for version control
- GitHub for repository hosting
- GitHub Actions for CI/CD
- Branch protection rules

### 6.2 Containerization

| Technology | Purpose |
|------------|---------|
| **Docker** | Container runtime |
| **Docker Compose** | Local multi-container setup |
| **Docker Hub** / **ECR** | Container registry |

### 6.3 CI/CD Pipeline

**GitHub Actions**
```yaml
Workflow:
  - Checkout code
  - Install dependencies
  - Run linters (ESLint, Prettier)
  - Run tests (Jest)
  - Build applications
  - Build Docker images
  - Push to registry
  - Deploy to environment
```

**Alternative**: GitLab CI, Jenkins, CircleCI

### 6.4 Cloud Infrastructure (AWS Example)

| Service | Purpose |
|---------|---------|
| **EC2** | Application servers (API backend) |
| **ECS** / **EKS** | Container orchestration |
| **RDS** | Managed PostgreSQL database |
| **ElastiCache** | Managed Redis |
| **S3** | Object storage |
| **CloudFront** | CDN for static assets |
| **Route 53** | DNS management |
| **ALB** | Application load balancer |
| **CloudWatch** | Monitoring and logging |
| **Secrets Manager** | Secure secrets storage |
| **IAM** | Identity and access management |
| **Certificate Manager** | SSL/TLS certificates |

**GCP Equivalents**:
- Compute Engine / GKE
- Cloud SQL
- Memorystore
- Cloud Storage
- Cloud CDN
- Cloud DNS
- Cloud Load Balancing
- Cloud Logging

**Azure Equivalents**:
- Virtual Machines / AKS
- Azure Database for PostgreSQL
- Azure Cache for Redis
- Blob Storage
- Azure CDN
- Azure DNS
- Application Gateway
- Azure Monitor

### 6.5 Monitoring & Observability

| Tool | Purpose |
|------|---------|
| **Sentry** | Error tracking and monitoring |
| **New Relic** / **Datadog** | Application performance monitoring |
| **Grafana + Prometheus** | Metrics visualization (open-source) |
| **ELK Stack** | Logging (Elasticsearch, Logstash, Kibana) |
| **CloudWatch** / **Stackdriver** | Cloud-native monitoring |
| **UptimeRobot** | Uptime monitoring |

### 6.6 Security

| Tool | Purpose |
|------|---------|
| **Let's Encrypt** | Free SSL certificates |
| **AWS Certificate Manager** | Managed SSL certificates |
| **Snyk** | Dependency vulnerability scanning |
| **OWASP ZAP** | Security testing |
| **AWS WAF** | Web application firewall |

---

## 7. Development Tools

### 7.1 Code Editors

- **Visual Studio Code** (recommended)
- **WebStorm** / **IntelliJ IDEA**
- **Android Studio** (for native Android debugging)
- **Xcode** (for iOS development and debugging)

### 7.2 Code Quality

| Tool | Purpose |
|------|---------|
| **ESLint** | JavaScript/TypeScript linting |
| **Prettier** | Code formatting |
| **Husky** | Git hooks |
| **lint-staged** | Run linters on staged files |
| **Commitlint** | Enforce commit message conventions |

### 7.3 Testing Tools

| Tool | Purpose |
|------|---------|
| **Jest** | Unit and integration testing |
| **React Testing Library** | React component testing |
| **Cypress** | E2E testing |
| **Playwright** | E2E testing (alternative) |
| **k6** | Load testing |
| **Postman** | API testing |
| **Insomnia** | API testing (alternative) |

### 7.4 Database Tools

- **pgAdmin** - PostgreSQL GUI
- **DBeaver** - Universal database tool
- **Prisma Studio** - Prisma ORM GUI
- **Redis Commander** - Redis GUI
- **TablePlus** - Modern database client

### 7.5 API Development

- **Postman** - API testing and documentation
- **Insomnia** - API client
- **Thunder Client** - VS Code extension for API testing

### 7.6 Design & Collaboration

- **Figma** - UI/UX design
- **Excalidraw** - Architecture diagrams
- **Draw.io** - Flowcharts and diagrams
- **Notion** / **Confluence** - Documentation
- **Slack** / **Discord** - Team communication

---

## 8. Technology Justifications

### 8.1 Why React Native for Mobile Apps?

**Pros:**
✅ Single codebase for iOS and Android (50% less development time)  
✅ Large community and ecosystem  
✅ Good performance for most use cases  
✅ Hot reload for faster development  
✅ JavaScript/TypeScript - same language as backend  
✅ Mature libraries for maps, QR codes, push notifications  

**Cons:**
❌ Slightly slower than native apps  
❌ Some platform-specific code still needed  
❌ Larger app size compared to native  

**Alternatives Considered:**
- **Flutter**: Excellent performance, but Dart language learning curve
- **Native (Swift/Kotlin)**: Best performance, but 2x development effort

**Decision**: React Native chosen for faster development and shared codebase.

---

### 8.2 Why Node.js + Express for Backend?

**Pros:**
✅ JavaScript/TypeScript full-stack (same language everywhere)  
✅ Excellent for real-time applications (Socket.io)  
✅ Non-blocking I/O - handles concurrent connections well  
✅ Large ecosystem (npm)  
✅ Team familiarity (assumption)  
✅ Fast development with Express.js  

**Cons:**
❌ Single-threaded (but can use clustering)  
❌ CPU-intensive tasks not ideal  

**Alternatives Considered:**
- **Python FastAPI**: Great for ML/AI integrations, async support
- **Go**: Excellent performance, but steeper learning curve
- **Java Spring Boot**: Enterprise-grade, but verbose

**Decision**: Node.js chosen for real-time capabilities and full-stack JavaScript.

---

### 8.3 Why PostgreSQL?

**Pros:**
✅ ACID compliant (data integrity)  
✅ Excellent for relational data  
✅ PostGIS extension for geospatial queries  
✅ TimescaleDB extension for time-series GPS data  
✅ Strong community and support  
✅ Open-source and free  
✅ JSON support for flexible schemas  

**Alternatives Considered:**
- **MySQL**: Good, but PostgreSQL has better geospatial support
- **MongoDB**: NoSQL good for flexibility, but we need ACID transactions

**Decision**: PostgreSQL chosen for robustness and geospatial capabilities.

---

### 8.4 Why Redis?

**Pros:**
✅ In-memory speed (sub-millisecond latency)  
✅ Perfect for caching GPS positions  
✅ Session storage  
✅ Pub/Sub for real-time updates  
✅ Job queue support (Bull)  
✅ Rate limiting counters  

**Alternatives Considered:**
- **Memcached**: Simpler, but Redis has more features
- **In-memory caching**: Not scalable across multiple servers

**Decision**: Redis chosen for versatility and performance.

---

### 8.5 Why Mapbox over Google Maps?

**Pros (Mapbox):**
✅ More cost-effective (50,000 free map loads/month vs Google's $200 credit)  
✅ Highly customizable map styling with Mapbox Studio  
✅ Better offline support for mobile apps  
✅ Modern WebGL rendering for smooth performance  
✅ Excellent mobile SDK with native features  
✅ Navigation SDK included  
✅ More developer-friendly API  
✅ Lower cost at scale  

**Pros (Google Maps):**
✅ More comprehensive POI (points of interest) data  
✅ Familiar interface for users  
✅ Street View integration  

**Decision**: Mapbox chosen for cost-effectiveness, customization, and better mobile performance. Ideal for university budget with professional features.

**Pricing Comparison:**
- **Mapbox Free Tier:** 50,000 map loads/month, 100,000 geocoding requests/month
- **Google Maps:** $200 credit ≈ 28,000 map loads/month
- **At Scale:** Mapbox is 30-50% cheaper than Google Maps

---

### 8.6 Why TypeScript?

**Pros:**
✅ Type safety reduces runtime errors  
✅ Better IDE support (autocomplete, refactoring)  
✅ Easier to maintain large codebases  
✅ Self-documenting code  
✅ Catches errors at compile time  

**Cons:**
❌ Slightly more verbose  
❌ Initial learning curve  

**Decision**: TypeScript chosen for long-term maintainability and code quality.

---

### 8.7 Why Prisma ORM?

**Pros:**
✅ Type-safe database queries  
✅ Excellent TypeScript integration  
✅ Auto-generated types from schema  
✅ Database migrations built-in  
✅ Prisma Studio for data visualization  
✅ Modern and developer-friendly  

**Alternatives Considered:**
- **TypeORM**: Good, but less type-safe
- **Sequelize**: Mature, but older API design
- **Raw SQL**: Maximum control, but more boilerplate

**Decision**: Prisma chosen for type safety and developer experience.

---

## 9. Package Versions Summary

### Backend Dependencies (package.json)

```json
{
  "dependencies": {
    "express": "^4.18.2",
    "socket.io": "^4.6.0",
    "prisma": "^5.8.0",
    "@prisma/client": "^5.8.0",
    "jsonwebtoken": "^9.0.2",
    "bcrypt": "^5.1.1",
    "joi": "^17.11.0",
    "winston": "^3.11.0",
    "helmet": "^7.1.0",
    "cors": "^2.8.5",
    "express-rate-limit": "^7.1.5",
    "ioredis": "^5.3.2",
    "bull": "^4.12.0",
    "node-cron": "^3.0.3",
    "axios": "^1.6.5",
    "dotenv": "^16.3.1",
    "multer": "^1.4.5-lts.1",
    "uuid": "^9.0.1"
  },
  "devDependencies": {
    "@types/node": "^20.10.0",
    "@types/express": "^4.17.21",
    "typescript": "^5.3.3",
    "ts-node-dev": "^2.0.0",
    "jest": "^29.7.0",
    "ts-jest": "^29.1.1",
    "supertest": "^6.3.3",
    "eslint": "^8.56.0",
    "prettier": "^3.1.1"
  }
}
```

### Mobile App Dependencies (package.json)

```json
{
  "dependencies": {
    "react": "18.2.0",
    "react-native": "0.73.2",
    "typescript": "^5.3.3",
    "@react-navigation/native": "^6.1.9",
    "@react-navigation/stack": "^6.3.20",
    "@reduxjs/toolkit": "^2.0.1",
    "react-redux": "^9.0.4",
    "redux-persist": "^6.0.0",
    "@tanstack/react-query": "^5.17.9",
    "axios": "^1.6.5",
    "socket.io-client": "^4.6.0",
    "@rnmapbox/maps": "^10.1.0",
    "react-native-qrcode-svg": "^6.2.0",
    "react-native-vision-camera": "^3.6.0",
    "vision-camera-code-scanner": "^0.2.0",
    "@react-native-firebase/messaging": "^19.0.0",
    "react-native-geolocation-service": "^5.3.1",
    "@react-native-async-storage/async-storage": "^1.21.0",
    "react-native-paper": "^5.11.0",
    "react-native-vector-icons": "^10.0.3",
    "date-fns": "^3.0.0",
    "react-hook-form": "^7.49.2",
    "yup": "^1.3.3"
  }
}
```

### Web Dashboard Dependencies (package.json)

```json
{
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "typescript": "^5.3.3",
    "react-router-dom": "^6.21.1",
    "@reduxjs/toolkit": "^2.0.1",
    "react-redux": "^9.0.4",
    "@tanstack/react-query": "^5.17.9",
    "@mui/material": "^5.15.2",
    "@emotion/react": "^11.11.3",
    "@emotion/styled": "^11.11.0",
    "recharts": "^2.10.3",
    "ag-grid-react": "^31.0.1",
    "axios": "^1.6.5",
    "socket.io-client": "^4.6.0",
    "react-map-gl": "^7.1.0",
    "mapbox-gl": "^3.0.0",
    "date-fns": "^3.0.0",
    "react-hook-form": "^7.49.2",
    "yup": "^1.3.3",
    "jspdf": "^2.5.1",
    "xlsx": "^0.18.5"
  },
  "devDependencies": {
    "vite": "^5.0.10",
    "@vitejs/plugin-react": "^4.2.1",
    "eslint": "^8.56.0",
    "prettier": "^3.1.1"
  }
}
```

---

## 10. Development Environment

### Minimum System Requirements

**For Backend Development:**
- CPU: 4+ cores
- RAM: 8GB minimum, 16GB recommended
- Storage: 20GB free space
- OS: Windows 10/11, macOS 11+, Ubuntu 20.04+

**For Mobile Development:**
- CPU: 4+ cores (8+ recommended)
- RAM: 16GB minimum, 32GB recommended
- Storage: 50GB+ free (for Android Studio, Xcode)
- OS: macOS (for iOS), Windows/macOS/Linux (for Android)

### Required Software

- **Node.js**: 20 LTS
- **npm** / **yarn** / **pnpm**: Latest
- **Docker** & **Docker Compose**: Latest
- **Git**: 2.40+
- **PostgreSQL**: 15+ (or Docker)
- **Redis**: 7+ (or Docker)
- **Android Studio**: Latest (for Android development)
- **Xcode**: 15+ (for iOS development, macOS only)
- **VS Code**: Latest

---

**END OF TECH STACK DOCUMENT**

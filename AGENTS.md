# AGENTS.md - Developer Guide
## University Bus Tracker System

**Last Updated:** December 26, 2025

---

## Project Overview

### Purpose
Real-time bus tracking and management system for universities, enabling students to track buses, purchase digital passes, and receive notifications. Includes driver operations and comprehensive admin dashboard.

### Project Type
Final Year Project (FYP) - Computer Science

### Tech Stack
- **Mobile Apps:** React Native 0.73+ with TypeScript
- **Admin Dashboard:** React.js 18+ with TypeScript, Vite
- **Backend:** Node.js 20 LTS, Express.js, Socket.io, TypeScript
- **Database:** PostgreSQL 15+ (PostGIS, TimescaleDB), Redis 7+
- **ORM:** Prisma
- **Infrastructure:** AWS (EC2, RDS, ElastiCache, S3)
- **CI/CD:** GitHub Actions, Docker

### Key Features
- Real-time GPS tracking with WebSocket
- Digital bus pass system with QR codes
- Payment integration (KuickPay)
- Push notifications (Firebase FCM)
- Role-based access (Student, Driver, Admin)
- Analytics and reporting

---

## 📚 Documentation Structure

### Primary Documents (Read These First)
1. **README.md** - Project overview, quick start, getting started
2. **new_PRD.md** - Product requirements, user stories, timeline
3. **ARCHITECTURE.md** - System architecture, design principles, security
4. **TECH_STACK.md** - Complete technology stack with justifications
5. **PROJECT_STRUCTURE.md** - Code organization, folder structure, conventions
6. **DATABASE_SCHEMA.md** - Database design, 21 tables, relationships, ERD
7. **API_ENDPOINTS.md** - 80+ REST API endpoints with examples
8. **DEPLOYMENT.md** - AWS infrastructure, CI/CD, monitoring
9. **DOCUMENTATION_INDEX.md** - Navigation guide for all documentation

### Development Phases (Start Here for Implementation)
10. **DEVELOPMENT_PHASES_INDEX.md** - Complete 8-phase development roadmap (16-20 weeks)
11. **PHASE_1_PROJECT_SETUP.md** - Foundation, monorepo, Docker, CI/CD (1-2 weeks)
12. **PHASE_2_AUTHENTICATION.md** - Auth, users, roles, JWT (2 weeks)
13. **PHASE_3_BUS_ROUTE_MANAGEMENT.md** - Buses, routes, stops, Mapbox (2-3 weeks)
14. **PHASE_4_REALTIME_TRACKING.md** - WebSocket, GPS, live tracking (2-3 weeks)
15. **PHASE_5_BUS_PASS_PAYMENT.md** - QR codes, KuickPay, verification (2-3 weeks)
16. **PHASE_6_NOTIFICATIONS_ANALYTICS.md** - FCM, emails, analytics (2 weeks)
17. **PHASE_7_TESTING_QA.md** - Testing, security, optimization (2 weeks)
18. **PHASE_8_DEPLOYMENT_LAUNCH.md** - AWS deployment, app stores (1-2 weeks)

### Quick Reference by Role
- **Backend Dev:** ARCHITECTURE.md → TECH_STACK.md → PROJECT_STRUCTURE.md → DATABASE_SCHEMA.md → API_ENDPOINTS.md
- **Frontend Dev:** ARCHITECTURE.md → TECH_STACK.md → PROJECT_STRUCTURE.md → API_ENDPOINTS.md
- **DevOps:** DEPLOYMENT.md → ARCHITECTURE.md → TECH_STACK.md
- **PM/BA:** README.md → new_PRD.md → ARCHITECTURE.md (sections 1-3)
- **New Developer:** README.md → AGENTS.md → DEVELOPMENT_PHASES_INDEX.md → Start with Phase 1

---

## 🏗️ Project Structure

### Expected Repository Structure
```
university-bus-tracker/
├── apps/
│   ├── backend/              # Node.js Express API
│   ├── student-app/          # React Native student app
│   ├── driver-app/           # React Native driver app
│   └── admin-dashboard/      # React.js admin dashboard
├── packages/
│   ├── shared-types/         # Shared TypeScript types
│   ├── shared-utils/         # Shared utility functions
│   └── api-client/           # Shared API client
├── infra/
│   ├── docker/               # Docker configurations
│   └── terraform/            # Infrastructure as code (optional)
├── docs/                     # Additional documentation
├── scripts/                  # Utility scripts
├── .github/workflows/        # CI/CD pipelines
└── [documentation files]
```

### Key Files
- **package.json** - Root dependencies (monorepo)
- **docker-compose.yml** - Local development environment
- **turbo.json** - Turborepo configuration (if using monorepo)
- **.env.example** - Environment variable template

---

## 🚀 Getting Started

### Prerequisites
- Node.js 20 LTS
- Docker & Docker Compose
- Git
- PostgreSQL 15+ (or use Docker)
- Redis 7+ (or use Docker)
- Android Studio (for Android) or Xcode (for iOS)

### Quick Start
```bash
# Clone repository
git clone <repo-url>
cd university-bus-tracker

# Start with Docker Compose
docker-compose up -d

# Or manual setup
cd apps/backend
npm install
cp .env.example .env
# Edit .env with your configuration
npx prisma migrate dev
npm run dev
```

### Development Servers
- Backend API: http://localhost:3000
- Admin Dashboard: http://localhost:3001
- PostgreSQL: localhost:5432
- Redis: localhost:6379

---

## 💻 Development Guidelines

### Code Organization

#### Backend (apps/backend/src/)
- **modules/** - Feature-based modules (auth, users, buses, routes, trips, passes, payments)
- **config/** - Configuration files
- **database/prisma/** - Prisma schema and migrations
- **common/** - Shared middleware, guards, utils
- **websocket/** - WebSocket handlers for real-time tracking

#### Mobile Apps (apps/student-app/ or driver-app/)
- **screens/** - Screen components
- **components/** - Reusable UI components
- **navigation/** - Navigation configuration
- **redux/** - Redux state management
- **api/** - API integration
- **hooks/** - Custom React hooks
- **utils/** - Utility functions

#### Admin Dashboard (apps/admin-dashboard/)
- **pages/** - Page components
- **components/** - Reusable components
- **redux/** - Redux state management
- **routes/** - Route configuration
- **api/** - API integration

### Naming Conventions
- **Files:** PascalCase for components (`UserProfile.tsx`), camelCase for utilities (`validation.ts`)
- **Folders:** kebab-case (`user-management/`) or PascalCase for components (`UserProfile/`)
- **Variables:** camelCase (`userId`, `busLocation`)
- **Constants:** UPPER_SNAKE_CASE (`API_BASE_URL`, `MAX_RETRIES`)
- **Boolean variables:** Prefix with `is`, `has`, `should` (`isActive`, `hasPermission`)
- **Functions:** camelCase with verb prefix (`getUserById`, `calculateDistance`)
- **API endpoints:** RESTful with plural nouns (`/users`, `/buses`, `/routes`)

### Commit Conventions
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

---

## 🗄️ Database Guidelines

### Key Tables (21 total)
- **Core:** users, roles, user_roles, profiles
- **User Types:** students, drivers
- **Operations:** buses, routes, stops, route_stops, bus_assignments, trips, gps_locations
- **Business:** bus_passes, payments, trip_scans
- **System:** notifications, announcements, maintenance_records, sessions, audit_logs

### Database Best Practices
- Always use Prisma migrations for schema changes
- Run migrations in order: Dev → Staging → Production
- Use PostGIS geography types for GPS coordinates
- Use TimescaleDB hypertable for GPS_locations (time-series optimization)
- Add indexes on frequently queried columns
- Use UUID for primary keys
- Include `created_at`, `updated_at` timestamps
- Use `deleted_at` for soft deletes on important entities

### Common Queries
```typescript
// Get active buses with current location
const buses = await prisma.buses.findMany({
  where: { status: 'active', deleted_at: null },
  include: { current_trip: { include: { route: true } } }
});

// Get student with active pass
const student = await prisma.students.findUnique({
  where: { id: studentId },
  include: { 
    bus_passes: { 
      where: { status: 'active', expiry_date: { gt: new Date() } }
    }
  }
});
```

---

## 🔌 API Guidelines

### API Standards
- **Base URL:** `https://api.university-bus-tracker.com/v1`
- **Authentication:** JWT Bearer Token in Authorization header
- **Format:** JSON request/response
- **Rate Limit:** 100 requests/minute per user
- **Status Codes:** 200 (OK), 201 (Created), 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 422 (Validation Error), 500 (Server Error)

### Response Format
```typescript
// Success
{
  "success": true,
  "message": "Operation successful",
  "data": { /* response data */ },
  "meta": {
    "timestamp": "2025-12-26T10:30:00Z",
    "request_id": "uuid"
  }
}

// Error
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Error description",
    "details": []
  }
}

// Paginated
{
  "success": true,
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "total_pages": 5
  }
}
```

### Key API Categories
- **Authentication:** /auth/* (login, register, refresh, password reset)
- **Users:** /users/* (profile, avatar, password change)
- **Buses:** /buses/* (tracking, location, ETA)
- **Routes:** /routes/* (list, details, stops)
- **Passes:** /passes/* (purchase, verify, history)
- **Payments:** /payments/* (intent, webhook, history)
- **Trips:** /trips/* (start, end, scan, delay) - Driver only
- **Admin:** /admin/* (users, buses, routes, assignments) - Admin only
- **Analytics:** /analytics/* (usage, revenue, performance) - Admin only

### WebSocket Events
**URL:** `wss://api.university-bus-tracker.com/ws?token=jwt`

**Client Events:**
- `subscribe_bus` - Subscribe to bus location updates
- `subscribe_route` - Subscribe to route updates
- `subscribe_stop` - Subscribe to stop updates
- `unsubscribe` - Unsubscribe from updates

**Server Events:**
- `bus_location_update` - Real-time GPS position
- `bus_status_update` - Bus status changes
- `trip_started` - Trip started notification
- `trip_ended` - Trip ended notification
- `bus_arrival_alert` - Bus arriving at stop
- `notification` - New notification

---

## 🔐 Security Guidelines

### Authentication
- Use JWT tokens (1 hour expiry) with refresh tokens (7 days)
- Store tokens securely (AsyncStorage/MMKV on mobile, httpOnly cookies on web)
- Hash passwords with bcrypt (12 rounds)
- Implement account lockout after 5 failed login attempts
- Rate limit authentication endpoints (5 req/min)

### Authorization
- Implement Role-Based Access Control (RBAC)
- Roles: student, driver, admin, super_admin
- Use middleware to check permissions on protected routes
- Verify user roles on both client and server

### Data Protection
- TLS 1.3 for all API communication
- Encrypt sensitive data at rest (AES-256)
- Encrypt QR codes with expiry timestamps
- Never log sensitive data (passwords, tokens, card details)
- Use AWS Secrets Manager for production secrets
- Validate and sanitize all user inputs
- Use parameterized queries (Prisma handles this)

### API Security
```typescript
// Rate limiting middleware
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 60 * 1000, // 1 minute
  max: 100, // 100 requests per minute
  message: 'Too many requests'
});

app.use('/api/', limiter);

// JWT authentication middleware
const authenticate = async (req, res, next) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token) return res.status(401).json({ error: 'Unauthorized' });
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
};
```

---

## 🧪 Testing Guidelines

### Testing Strategy
- **Unit Tests:** >80% coverage target
- **Integration Tests:** All API endpoints
- **E2E Tests:** Critical user flows

### Test Structure
```typescript
// Unit test example (Jest)
describe('AuthService', () => {
  describe('login', () => {
    it('should return tokens for valid credentials', async () => {
      const result = await authService.login('user@test.com', 'password');
      expect(result).toHaveProperty('access_token');
      expect(result).toHaveProperty('refresh_token');
    });

    it('should throw error for invalid credentials', async () => {
      await expect(
        authService.login('user@test.com', 'wrong')
      ).rejects.toThrow('Invalid credentials');
    });
  });
});

// Integration test example (Supertest)
describe('POST /api/v1/auth/login', () => {
  it('should return 200 with tokens', async () => {
    const response = await request(app)
      .post('/api/v1/auth/login')
      .send({ email: 'user@test.com', password: 'password' });
    
    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('data.tokens');
  });
});
```

### Running Tests
```bash
# Unit tests
npm run test

# Watch mode
npm run test:watch

# Coverage
npm run test:coverage

# Integration tests
npm run test:integration

# E2E tests
npm run test:e2e
```

---

## 🚀 Deployment Guidelines

### Environments
- **Development:** Local Docker environment
- **Staging:** Pre-production on AWS (smaller instances)
- **Production:** Live system on AWS (Multi-AZ, auto-scaling)

### CI/CD Pipeline (GitHub Actions)
1. **Lint** - ESLint checks
2. **Test** - Run all tests
3. **Build** - Create production build
4. **Docker** - Build and push Docker image
5. **Deploy** - Deploy to target environment
6. **Health Check** - Verify deployment

### Deployment Commands
```bash
# Deploy to staging (automatic on push to staging branch)
git push origin staging

# Deploy to production (requires manual approval)
git push origin main

# Rollback
npm run rollback
```

### Environment Variables
```bash
# Required variables
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://user:pass@host:5432/db
REDIS_URL=redis://host:6379
JWT_SECRET=<strong-secret>
JWT_REFRESH_SECRET=<strong-secret>

# Third-party services
KUICKPAY_API_KEY=<key>
MAPBOX_ACCESS_TOKEN=<token>
FIREBASE_PROJECT_ID=<id>
SENDGRID_API_KEY=<key>
AWS_ACCESS_KEY_ID=<key>
AWS_SECRET_ACCESS_KEY=<secret>
```

---

## 🔍 Debugging & Troubleshooting

### Common Issues

#### Database Connection Failed
```bash
# Check PostgreSQL is running
docker-compose ps postgres

# Check connection string in .env
# Format: postgresql://user:password@host:port/database

# Test connection
psql postgresql://user:password@localhost:5432/bus_tracker
```

#### Redis Connection Failed
```bash
# Check Redis is running
docker-compose ps redis

# Test connection
redis-cli -h localhost -p 6379 ping
```

#### Mobile App Build Errors
```bash
# Clean and rebuild
cd apps/student-app

# Android
cd android && ./gradlew clean && cd ..
npm run android

# iOS
cd ios && pod install && cd ..
npm run ios
```

#### API Returns 401 Unauthorized
- Check JWT token is included in Authorization header
- Verify token hasn't expired (1 hour expiry)
- Use refresh token to get new access token
- Check user role has required permissions

### Logging
```typescript
// Use Winston logger
import logger from './logger';

logger.error('Critical error', { error, context });   // Investigate immediately
logger.warn('Warning condition', { details });        // Review regularly
logger.info('Important event', { data });             // Business events
logger.debug('Detailed info', { state });             // Development only
```

### Monitoring
- **CloudWatch:** AWS resource monitoring
- **Sentry:** Error tracking and crash reports
- **New Relic:** Application performance monitoring
- **Health Endpoint:** GET /health (API health check)

---

## 📦 Package Management

### Dependencies
```bash
# Install dependencies
npm install

# Add new dependency
npm install package-name

# Add dev dependency
npm install -D package-name

# Update dependencies
npm update

# Audit for vulnerabilities
npm audit
npm audit fix
```

### Important Packages
**Backend:**
- express, socket.io, @prisma/client, jsonwebtoken, bcrypt, joi, winston, helmet, cors, ioredis, bull

**Mobile:**
- react-native, @react-navigation/native, @reduxjs/toolkit, axios, react-query, socket.io-client, @rnmapbox/maps, react-native-vision-camera, @react-native-firebase/messaging

**Web Dashboard:**
- react, react-router-dom, @reduxjs/toolkit, axios, @tanstack/react-query, @mui/material, recharts, socket.io-client

---

## 🎨 UI/UX Guidelines

### Design System
- **Colors:** Defined in theme/colors.ts
- **Typography:** Defined in theme/typography.ts
- **Spacing:** 8px base unit (8, 16, 24, 32, 40, 48)
- **Icons:** react-native-vector-icons / Material Icons

### Mobile App Guidelines
- Follow platform design guidelines (Material Design for Android, Human Interface for iOS)
- Use native components where possible
- Optimize for offline functionality (AsyncStorage/MMKV)
- Implement pull-to-refresh on lists
- Show loading states for async operations
- Display error messages clearly

### Admin Dashboard Guidelines
- Responsive design (desktop-first, then tablet, mobile)
- Data tables with pagination, sorting, filtering
- Charts and graphs for analytics
- Real-time updates via WebSocket
- Export functionality (CSV, PDF)

---

## 🔄 Real-Time Features

### GPS Tracking Implementation
```typescript
// Driver app - Send GPS updates
const sendLocationUpdate = async (location) => {
  await axios.post(`/trips/${tripId}/location`, {
    latitude: location.coords.latitude,
    longitude: location.coords.longitude,
    speed: location.coords.speed,
    bearing: location.coords.heading,
    accuracy: location.coords.accuracy,
    timestamp: new Date().toISOString()
  });
};

// Backend - Broadcast to subscribers
io.to(`bus:${busId}`).emit('bus_location_update', {
  bus_id: busId,
  latitude,
  longitude,
  speed,
  bearing,
  timestamp
});

// Student app - Subscribe to updates
socket.emit('subscribe_bus', { bus_id: busId });
socket.on('bus_location_update', (data) => {
  updateBusMarkerPosition(data);
});
```

### Notification System
```typescript
// Send push notification via FCM
import admin from 'firebase-admin';

const sendNotification = async (userId, notification) => {
  const tokens = await getUserDeviceTokens(userId);
  
  await admin.messaging().sendMulticast({
    tokens,
    notification: {
      title: notification.title,
      body: notification.message
    },
    data: notification.data
  });
};
```

---

## 📊 Analytics & Reporting

### Key Metrics to Track
- Total trips per day/week/month
- Total passengers per route
- Bus utilization percentage
- Revenue by pass type
- Driver performance (on-time %, delay minutes)
- Popular routes and time slots
- Payment success rate

### Generating Reports
```typescript
// Example: Revenue report
const getRevenueReport = async (startDate, endDate) => {
  return await prisma.payments.groupBy({
    by: ['payment_date'],
    where: {
      payment_date: { gte: startDate, lte: endDate },
      status: 'completed'
    },
    _sum: { amount: true },
    _count: true
  });
};
```

---

## 🌍 Geospatial Queries

### Using PostGIS
```sql
-- Find stops within 500m of a location
SELECT * FROM stops
WHERE ST_DWithin(
  location,
  ST_SetSRID(ST_MakePoint(73.0479, 33.6844), 4326)::geography,
  500
);

-- Calculate distance between two points
SELECT ST_Distance(
  ST_SetSRID(ST_MakePoint(73.0479, 33.6844), 4326)::geography,
  ST_SetSRID(ST_MakePoint(73.0645, 33.6990), 4326)::geography
) / 1000 as distance_km;
```

### Using Prisma with PostGIS
```typescript
// Raw query for geospatial operations
const nearbyStops = await prisma.$queryRaw`
  SELECT * FROM stops
  WHERE ST_DWithin(
    location,
    ST_SetSRID(ST_MakePoint(${longitude}, ${latitude}), 4326)::geography,
    ${radiusMeters}
  )
`;
```

---

## 🔧 Useful Commands

### Docker
```bash
# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# View logs
docker-compose logs -f [service-name]

# Restart a service
docker-compose restart backend

# Rebuild a service
docker-compose up -d --build backend

# Execute command in container
docker-compose exec backend npm run migrate
```

### Prisma
```bash
# Generate Prisma client
npx prisma generate

# Create migration
npx prisma migrate dev --name migration_name

# Apply migrations
npx prisma migrate deploy

# Reset database (dev only!)
npx prisma migrate reset

# Open Prisma Studio
npx prisma studio

# Pull schema from database
npx prisma db pull
```

### Git
```bash
# Create feature branch
git checkout -b feature/feature-name

# Commit with conventional commit
git commit -m "feat: add feature description"

# Push and create PR
git push origin feature/feature-name

# Update from main
git checkout main && git pull
git checkout feature/feature-name
git rebase main
```

---

## 🎯 Best Practices

### Code Quality
- Write self-documenting code with clear variable and function names
- Keep functions small and focused (single responsibility)
- Use TypeScript strict mode
- Avoid any type, use proper types
- Handle errors gracefully with try-catch
- Log errors with context for debugging
- Write tests for new features
- Keep test coverage above 80%

### Performance
- Use Redis for caching frequently accessed data
- Implement pagination on list endpoints
- Use database indexes on frequently queried columns
- Optimize images before uploading
- Lazy load components and routes
- Debounce search inputs
- Use React.memo for expensive components
- Implement virtual lists for long lists

### Security
- Never commit secrets or API keys
- Use environment variables for configuration
- Validate all user inputs
- Sanitize data before database operations
- Implement rate limiting on all endpoints
- Use HTTPS in production
- Keep dependencies updated
- Run security audits regularly (npm audit)

### Git Workflow
- Create feature branches from develop
- Keep commits small and focused
- Write clear commit messages
- Squash commits before merging
- Review PRs thoroughly
- Test before pushing
- Never commit directly to main

---

## 📞 Support & Resources

### Getting Help
- Check documentation first (see DOCUMENTATION_INDEX.md)
- Search existing issues on GitHub
- Ask team members in Slack/Discord
- Create detailed issue with reproduction steps

### External Resources
- [React Native Docs](https://reactnative.dev/)
- [React Docs](https://react.dev/)
- [Node.js Docs](https://nodejs.org/)
- [Prisma Docs](https://www.prisma.io/docs)
- [PostgreSQL Manual](https://www.postgresql.org/docs/)
- [Socket.io Docs](https://socket.io/docs/)
- [AWS Documentation](https://docs.aws.amazon.com/)

### Project Links
- GitHub Repository: [Add URL]
- Staging Environment: [Add URL]
- Production: [Add URL]
- API Documentation (Swagger): [Add URL]

---

## ✅ Developer Checklist

### Before Starting Development
- [ ] Read README.md and understand project
- [ ] Review relevant documentation sections
- [ ] Set up development environment
- [ ] Install all prerequisites
- [ ] Clone repository and install dependencies
- [ ] Configure environment variables
- [ ] Run database migrations
- [ ] Start development servers
- [ ] Verify everything works

### Before Committing Code
- [ ] Code follows naming conventions
- [ ] No console.logs in production code
- [ ] Error handling implemented
- [ ] Tests written and passing
- [ ] Linting passes (npm run lint)
- [ ] Type checking passes (npm run type-check)
- [ ] Code reviewed by peer
- [ ] Documentation updated if needed

### Before Deployment
- [ ] All tests passing
- [ ] No merge conflicts
- [ ] Environment variables configured
- [ ] Database migrations ready
- [ ] API endpoints tested
- [ ] Error monitoring configured
- [ ] Rollback plan documented

---

## 🚀 Quick Start for New Developers

### First Day Setup
1. **Read This File** - You're already here! ✅
2. **Review README.md** - Understand the project
3. **Check DEVELOPMENT_PHASES_INDEX.md** - See the development roadmap
4. **Start Phase 1** - Follow PHASE_1_PROJECT_SETUP.md for environment setup
5. **Run Docker Compose** - `docker-compose up -d`
6. **Verify Setup** - Check all services are running

### Development Workflow
1. **Create Feature Branch** - `git checkout -b feature/your-feature`
2. **Follow Phase Documentation** - Use execution prompts in phase files
3. **Write Tests** - Aim for >80% coverage
4. **Code Review** - Create PR when ready
5. **Deploy** - Follow CI/CD pipeline

### Getting Help
- **Documentation:** Check DOCUMENTATION_INDEX.md for specific topics
- **Architecture Questions:** See ARCHITECTURE.md
- **API Questions:** See API_ENDPOINTS.md
- **Database Questions:** See DATABASE_SCHEMA.md
- **Deployment Questions:** See DEPLOYMENT.md
- **Phase-Specific Questions:** See relevant PHASE_X files

### Important Commands
```bash
# Start all services
docker-compose up -d

# Run backend
cd apps/backend && npm run dev

# Run mobile app
cd apps/student-app && npm run android

# Run admin dashboard
cd apps/admin-dashboard && npm run dev

# Run tests
npm run test

# Run migrations
npx prisma migrate dev
```

---

**Happy Coding! 🚀**

*This file is maintained by the development team. Last updated: December 26, 2025*

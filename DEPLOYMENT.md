# Deployment Architecture
## University Bus Tracker System

**Version:** 1.0  
**Date:** December 26, 2025

---

## Table of Contents

1. [Deployment Overview](#1-deployment-overview)
2. [Infrastructure Architecture](#2-infrastructure-architecture)
3. [Environment Configuration](#3-environment-configuration)
4. [CI/CD Pipeline](#4-cicd-pipeline)
5. [Production Deployment](#5-production-deployment)
6. [Monitoring & Maintenance](#6-monitoring--maintenance)
7. [Backup & Disaster Recovery](#7-backup--disaster-recovery)
8. [Security Configuration](#8-security-configuration)
9. [Scaling Strategy](#9-scaling-strategy)

---

## 1. Deployment Overview

### 1.1 Deployment Strategy

**Approach:** Blue-Green Deployment with Rolling Updates

**Environments:**
- **Development:** Local Docker environment
- **Staging:** Pre-production testing (AWS/Cloud)
- **Production:** Live system (AWS/Cloud)

### 1.2 Hosting Recommendations

**Recommended Cloud Provider:** AWS (Amazon Web Services)

**Why AWS:**
- Mature ecosystem with comprehensive services
- Excellent uptime and reliability (99.99%)
- Cost-effective for startups (free tier available)
- Strong security and compliance
- Easy to scale

**Alternatives:**
- Google Cloud Platform (GCP)
- Microsoft Azure
- DigitalOcean (simpler, good for smaller scale)

---

## 2. Infrastructure Architecture

### 2.1 AWS Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      USERS (Internet)                           │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Route 53 (DNS)                               │
│              bus-tracker.university.edu                         │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│              CloudFront (CDN) + WAF                             │
│          - Static assets caching                                │
│          - DDoS protection                                      │
│          - SSL/TLS termination                                  │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│         Application Load Balancer (ALB)                         │
│          - Health checks                                        │
│          - SSL certificates                                     │
│          - Request routing                                      │
└─────────┬────────────────────────────────┬──────────────────────┘
          │                                │
          ▼                                ▼
┌──────────────────────┐       ┌──────────────────────┐
│   Auto Scaling Group │       │   Auto Scaling Group │
│   (Web Servers)      │       │   (API Servers)      │
│                      │       │                      │
│  ┌────────────────┐  │       │  ┌────────────────┐  │
│  │  EC2 Instance  │  │       │  │  EC2 Instance  │  │
│  │  (Admin Web)   │  │       │  │  (Backend API) │  │
│  └────────────────┘  │       │  └────────────────┘  │
│  ┌────────────────┐  │       │  ┌────────────────┐  │
│  │  EC2 Instance  │  │       │  │  EC2 Instance  │  │
│  │  (Admin Web)   │  │       │  │  (Backend API) │  │
│  └────────────────┘  │       │  └────────────────┘  │
└──────────┬───────────┘       └──────────┬───────────┘
           │                              │
           │      ┌───────────────────────┘
           │      │
           ▼      ▼
┌─────────────────────────────────────────────────────────────────┐
│                    VPC (Virtual Private Cloud)                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                   Private Subnet                         │   │
│  │  ┌─────────────┐  ┌──────────────┐  ┌────────────────┐  │   │
│  │  │   RDS       │  │ ElastiCache  │  │  ECS/EKS       │  │   │
│  │  │ PostgreSQL  │  │   Redis      │  │  (Optional)    │  │   │
│  │  └─────────────┘  └──────────────┘  └────────────────┘  │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
           │                    │
           ▼                    ▼
┌──────────────────┐   ┌──────────────────┐
│   S3 Bucket      │   │  CloudWatch      │
│  - Files/Images  │   │  - Logs          │
│  - Backups       │   │  - Metrics       │
└──────────────────┘   └──────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│              External Services                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │  KuickPay    │  │   Mapbox     │  │   FCM        │          │
│  │  Gateway     │  │     API      │  │ (Firebase)   │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

### 2.2 Component Breakdown

#### Frontend (Mobile Apps)
- **Distribution:** App Store (iOS), Google Play Store (Android)
- **Updates:** Over-the-air updates via CodePush (optional)
- **API Connection:** HTTPS to backend API

#### Frontend (Admin Dashboard)
- **Hosting:** AWS S3 + CloudFront (static site)
- **Build:** React production build
- **CDN:** CloudFront for global distribution

#### Backend API
- **Compute:** EC2 instances (t3.medium) or ECS containers
- **Load Balancer:** Application Load Balancer
- **Auto Scaling:** 2-10 instances based on CPU/memory
- **Health Checks:** ALB health checks every 30 seconds

#### Database
- **Primary:** RDS PostgreSQL (Multi-AZ for high availability)
- **Instance Type:** db.t3.large (2 vCPU, 8GB RAM)
- **Backup:** Automated daily backups, 30-day retention
- **Read Replicas:** 1-2 replicas for read-heavy operations

#### Cache
- **Service:** ElastiCache for Redis
- **Instance Type:** cache.t3.medium
- **Replication:** Multi-AZ with automatic failover

#### Storage
- **Service:** S3 Standard for active files
- **S3 Glacier:** For archived data (old receipts, logs)
- **Lifecycle Policies:** Transition to Glacier after 90 days

---

## 3. Environment Configuration

### 3.1 Development Environment

**Setup:** Docker Compose for local development

```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: bus_tracker_dev
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: dev_password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  backend:
    build:
      context: ./apps/backend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://postgres:dev_password@postgres:5432/bus_tracker_dev
      REDIS_URL: redis://redis:6379
    volumes:
      - ./apps/backend:/app
      - /app/node_modules
    depends_on:
      - postgres
      - redis
    command: npm run dev

  admin-dashboard:
    build:
      context: ./apps/admin-dashboard
      dockerfile: Dockerfile.dev
    ports:
      - "3001:3001"
    volumes:
      - ./apps/admin-dashboard:/app
      - /app/node_modules
    command: npm run dev

volumes:
  postgres_data:
```

**Commands:**
```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f backend

# Stop all services
docker-compose down

# Reset database
docker-compose down -v && docker-compose up -d
```

---

### 3.2 Staging Environment

**Purpose:** Pre-production testing, UAT, integration testing

**Infrastructure:**
- Smaller EC2 instances (t3.small)
- Single RDS instance (no Multi-AZ)
- Shared ElastiCache instance
- S3 bucket for staging files

**Configuration:**
```bash
# Environment Variables
NODE_ENV=staging
API_BASE_URL=https://staging-api.university-bus-tracker.com
DATABASE_URL=postgresql://user:pass@staging-db.rds.amazonaws.com:5432/bus_tracker_staging
REDIS_URL=redis://staging-redis.cache.amazonaws.com:6379
```

**Deployment:** Automatic deployment on merge to `staging` branch

---

### 3.3 Production Environment

**Infrastructure:**
- EC2 Auto Scaling (t3.medium, 2-10 instances)
- RDS Multi-AZ (db.t3.large)
- ElastiCache Multi-AZ (cache.t3.medium)
- CloudFront + WAF
- Route 53 for DNS

**Configuration:**
```bash
# Environment Variables
NODE_ENV=production
API_BASE_URL=https://api.university-bus-tracker.com
DATABASE_URL=postgresql://user:pass@prod-db.rds.amazonaws.com:5432/bus_tracker
REDIS_URL=redis://prod-redis.cache.amazonaws.com:6379

# Security
JWT_SECRET=<strong-secret-from-secrets-manager>
KUICKPAY_API_KEY=<from-secrets-manager>
```

**Deployment:** Manual approval required after staging tests pass

---

## 4. CI/CD Pipeline

### 4.1 GitHub Actions Workflow

```yaml
# .github/workflows/deploy.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, staging, develop]
  pull_request:
    branches: [main, staging]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
          
      - name: Install dependencies
        run: |
          cd apps/backend
          npm ci
          
      - name: Run linter
        run: |
          cd apps/backend
          npm run lint
          
      - name: Run tests
        run: |
          cd apps/backend
          npm run test
          
      - name: Check test coverage
        run: |
          cd apps/backend
          npm run test:coverage
          
  build-backend:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/staging'
    steps:
      - uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
          
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1
        
      - name: Build and push Docker image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          ECR_REPOSITORY: bus-tracker-backend
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG ./apps/backend
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
          
  deploy-staging:
    needs: build-backend
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/staging'
    steps:
      - name: Deploy to Staging
        run: |
          # Deploy to staging environment
          # Update ECS service or trigger deployment script
          
  deploy-production:
    needs: build-backend
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://api.university-bus-tracker.com
    steps:
      - name: Deploy to Production
        run: |
          # Deploy to production environment
          # Blue-green deployment with health checks
```

### 4.2 Pipeline Stages

1. **Code Checkout** - Pull latest code from repository
2. **Dependency Installation** - Install npm packages
3. **Linting** - Check code quality with ESLint
4. **Unit Tests** - Run Jest tests
5. **Integration Tests** - Test API endpoints
6. **Build** - Create production build
7. **Docker Build** - Build Docker image
8. **Push to Registry** - Push to ECR/Docker Hub
9. **Deploy** - Deploy to target environment
10. **Health Check** - Verify deployment success
11. **Rollback** (if health check fails)

---

## 5. Production Deployment

### 5.1 Pre-Deployment Checklist

- [ ] All tests passing on staging
- [ ] Database migrations tested
- [ ] Environment variables configured
- [ ] Secrets stored in AWS Secrets Manager
- [ ] SSL certificates valid and renewed
- [ ] Monitoring and alerts configured
- [ ] Backup verified and tested
- [ ] Rollback plan documented
- [ ] Team notified of deployment window
- [ ] Maintenance page ready (if needed)

### 5.2 Deployment Steps

#### Step 1: Database Migration
```bash
# Run migrations on production database
npm run migrate:production

# Verify migration success
npm run migrate:status
```

#### Step 2: Build Production Images
```bash
# Build backend
docker build -t bus-tracker-backend:v1.0.0 ./apps/backend

# Tag and push to ECR
docker tag bus-tracker-backend:v1.0.0 <ecr-url>/bus-tracker-backend:v1.0.0
docker push <ecr-url>/bus-tracker-backend:v1.0.0
```

#### Step 3: Deploy to Production
```bash
# Update ECS service with new image
aws ecs update-service \
  --cluster bus-tracker-production \
  --service backend-api \
  --force-new-deployment

# Or deploy to EC2 with deployment script
./scripts/deploy-production.sh
```

#### Step 4: Health Checks
```bash
# Check API health
curl https://api.university-bus-tracker.com/health

# Check database connection
curl https://api.university-bus-tracker.com/health/db

# Monitor CloudWatch metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/ECS \
  --metric-name CPUUtilization \
  --start-time 2025-12-26T00:00:00Z \
  --end-time 2025-12-26T23:59:59Z \
  --period 300 \
  --statistics Average
```

#### Step 5: Smoke Tests
```bash
# Run automated smoke tests
npm run test:smoke:production

# Manual verification
- Login to admin dashboard
- Check bus tracking works
- Verify payment processing
- Test notifications
```

### 5.3 Rollback Procedure

**Automated Rollback:**
```bash
# Revert to previous version
aws ecs update-service \
  --cluster bus-tracker-production \
  --service backend-api \
  --task-definition backend-api:previous

# Or rollback via deployment script
./scripts/rollback.sh
```

**Manual Rollback:**
1. Stop accepting new traffic (update load balancer)
2. Revert to previous Docker image
3. Rollback database migrations (if necessary)
4. Clear Redis cache
5. Resume traffic to previous version
6. Investigate and fix issues

---

## 6. Monitoring & Maintenance

### 6.1 Monitoring Stack

#### CloudWatch Dashboards

**Metrics to Monitor:**
- **API Performance:**
  - Request count
  - Response time (p50, p95, p99)
  - Error rate (4xx, 5xx)
  - Active connections
  
- **Database:**
  - CPU utilization
  - Database connections
  - Query execution time
  - Storage usage
  
- **Cache (Redis):**
  - Hit rate
  - Memory usage
  - Evictions
  
- **Infrastructure:**
  - EC2 CPU/Memory
  - Network in/out
  - Disk I/O

#### Application Monitoring

**Tools:**
- **Sentry:** Error tracking and crash reporting
- **New Relic / Datadog:** APM (Application Performance Monitoring)
- **LogDNA / CloudWatch Logs:** Centralized logging

**Custom Metrics:**
```javascript
// Track business metrics
metrics.increment('bus.trip.started');
metrics.increment('pass.purchased');
metrics.gauge('buses.active', activeBusCount);
metrics.histogram('trip.duration', durationInMinutes);
```

### 6.2 Alerting Rules

**Critical Alerts (Page On-Call):**
- API error rate > 5%
- API response time p95 > 2 seconds
- Database CPU > 90%
- Any service down
- Payment processing failures

**Warning Alerts (Email/Slack):**
- API error rate > 2%
- Database connections > 80%
- Redis memory > 80%
- Disk usage > 80%

**Alert Channels:**
- PagerDuty (critical)
- Slack #alerts channel
- Email to on-call engineer

### 6.3 Logging Strategy

**Log Levels:**
```javascript
logger.error('Critical error', { error, context });   // Investigate immediately
logger.warn('Warning condition', { details });        // Review regularly
logger.info('Important event', { data });             // Business events
logger.debug('Detailed info', { state });             // Development only
```

**Structured Logging:**
```json
{
  "timestamp": "2025-12-26T10:30:00Z",
  "level": "info",
  "service": "backend-api",
  "request_id": "uuid",
  "user_id": "uuid",
  "action": "bus_pass_purchased",
  "details": {
    "pass_type": "monthly",
    "amount": 2000
  }
}
```

### 6.4 Maintenance Windows

**Scheduled Maintenance:**
- **Time:** Sundays 2:00 AM - 4:00 AM (low traffic)
- **Frequency:** Monthly
- **Activities:**
  - Database optimization
  - Security patches
  - Dependency updates
  - Data archival

**Maintenance Notification:**
1. Email to all users (3 days before)
2. In-app notification (1 day before)
3. Status page update
4. Social media announcement

---

## 7. Backup & Disaster Recovery

### 7.1 Backup Strategy

#### Database Backups

**Automated Backups (RDS):**
```
- Frequency: Daily at 3:00 AM UTC
- Retention: 30 days
- Backup window: 3:00 AM - 4:00 AM
- Snapshot to S3
```

**Manual Backups:**
```bash
# Create manual snapshot
aws rds create-db-snapshot \
  --db-instance-identifier bus-tracker-prod \
  --db-snapshot-identifier manual-backup-20251226

# Verify backup
aws rds describe-db-snapshots \
  --db-snapshot-identifier manual-backup-20251226
```

#### Application Backups

**Files & Media (S3):**
- Versioning enabled
- Cross-region replication (optional)
- Lifecycle policies for cost optimization

**Configuration Backups:**
- Store in Git repository
- AWS Systems Manager Parameter Store
- AWS Secrets Manager

### 7.2 Disaster Recovery Plan

**RTO (Recovery Time Objective):** 2 hours  
**RPO (Recovery Point Objective):** 24 hours

#### Disaster Scenarios

**Scenario 1: Database Failure**
```
1. Promote read replica to master (if Multi-AZ)
2. Update DNS/connection strings
3. Verify application connectivity
4. Monitor for data consistency
Time: ~15 minutes
```

**Scenario 2: Complete Region Failure**
```
1. Failover to backup region (if configured)
2. Restore database from latest snapshot
3. Deploy application to new region
4. Update Route 53 to point to new region
5. Verify all services operational
Time: ~2 hours
```

**Scenario 3: Data Corruption**
```
1. Identify corruption time
2. Restore database from snapshot before corruption
3. Replay logs/transactions after restore point
4. Verify data integrity
Time: ~4 hours
```

### 7.3 Recovery Procedures

#### Database Restore
```bash
# Restore from snapshot
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier bus-tracker-prod-restored \
  --db-snapshot-identifier auto-backup-20251226

# Point application to restored database
# Update DATABASE_URL environment variable
```

#### Application Restore
```bash
# Rollback to previous stable version
./scripts/rollback.sh

# Or deploy specific version
./scripts/deploy.sh --version v1.2.3
```

---

## 8. Security Configuration

### 8.1 Network Security

**VPC Configuration:**
```
- Public Subnet: Load Balancer, NAT Gateway
- Private Subnet: EC2 instances, RDS, ElastiCache
- Database in isolated subnet (no internet access)
```

**Security Groups:**
```
ALB Security Group:
- Inbound: 443 (HTTPS) from 0.0.0.0/0
- Outbound: 3000 to Backend SG

Backend Security Group:
- Inbound: 3000 from ALB SG
- Outbound: 5432 to RDS SG, 6379 to Redis SG

RDS Security Group:
- Inbound: 5432 from Backend SG only
- Outbound: None

Redis Security Group:
- Inbound: 6379 from Backend SG only
- Outbound: None
```

### 8.2 SSL/TLS Configuration

**Certificate Management:**
```bash
# AWS Certificate Manager
- Domain: *.university-bus-tracker.com
- Validation: DNS validation
- Auto-renewal: Enabled
```

**TLS Configuration:**
- Minimum TLS version: 1.2
- Cipher suites: Modern, secure ciphers only
- HSTS enabled (HTTP Strict Transport Security)

### 8.3 Secrets Management

**AWS Secrets Manager:**
```json
{
  "DB_PASSWORD": "encrypted-db-password",
  "JWT_SECRET": "encrypted-jwt-secret",
  "KUICKPAY_API_KEY": "encrypted-api-key",
  "REDIS_PASSWORD": "encrypted-redis-password"
}
```

**Access from Application:**
```javascript
const AWS = require('aws-sdk');
const secretsManager = new AWS.SecretsManager();

async function getSecret(secretName) {
  const data = await secretsManager.getSecretValue({ SecretId: secretName }).promise();
  return JSON.parse(data.SecretString);
}
```

### 8.4 IAM Roles & Policies

**Backend IAM Role:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::bus-tracker-files/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue"
      ],
      "Resource": "arn:aws:secretsmanager:*:*:secret:bus-tracker/*"
    }
  ]
}
```

---

## 9. Scaling Strategy

### 9.1 Horizontal Scaling

**Auto Scaling Policies:**

**Scale Out (Add Instances):**
```
Trigger: CPU > 70% for 5 minutes
Action: Add 1 instance
Cooldown: 5 minutes
Max Instances: 10
```

**Scale In (Remove Instances):**
```
Trigger: CPU < 30% for 10 minutes
Action: Remove 1 instance
Cooldown: 10 minutes
Min Instances: 2
```

### 9.2 Database Scaling

**Read Replicas:**
- Add 1-2 read replicas for read-heavy operations
- Route read queries to replicas
- Master handles writes only

**Vertical Scaling:**
```
Current: db.t3.large (2 vCPU, 8GB)
Next: db.r5.large (2 vCPU, 16GB)
Future: db.r5.xlarge (4 vCPU, 32GB)
```

### 9.3 Caching Strategy

**Multi-Level Caching:**
1. **Browser Cache:** Static assets (24 hours)
2. **CDN Cache:** Images, JS, CSS (7 days)
3. **Redis Cache:** API responses (5-60 minutes)
4. **Database Query Cache:** Frequently accessed data

**Cache Invalidation:**
```javascript
// Invalidate on update
await redis.del(`bus:${busId}`);
await redis.del('buses:active');

// Pattern-based invalidation
await redis.keys('route:*').then(keys => redis.del(keys));
```

---

## 10. Cost Optimization

### 10.1 Estimated Monthly Costs (AWS)

**Production Environment:**

| Service | Configuration | Monthly Cost |
|---------|--------------|--------------|
| EC2 (Backend) | 2x t3.medium | $60 |
| RDS (PostgreSQL) | db.t3.large Multi-AZ | $145 |
| ElastiCache (Redis) | cache.t3.medium | $50 |
| S3 Storage | 100GB Standard | $3 |
| CloudFront | 500GB transfer | $40 |
| Load Balancer | ALB | $25 |
| Route 53 | Hosted zone | $1 |
| **Total** | | **~$324/month** |

**Note:** Costs may vary based on actual usage and data transfer.

### 10.2 Cost Saving Tips

1. **Use Reserved Instances** - Save 30-70% on EC2/RDS
2. **S3 Lifecycle Policies** - Move old data to Glacier
3. **Auto Scaling** - Scale down during low traffic
4. **Spot Instances** - Use for non-critical workloads
5. **CloudFront Caching** - Reduce origin requests
6. **Database Connection Pooling** - Reduce RDS connections

---

**END OF DEPLOYMENT DOCUMENT**
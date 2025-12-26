# Database Schema Design
## University Bus Tracker System

**Version:** 1.0  
**Date:** December 26, 2025  
**Database:** PostgreSQL 15+

---

## Table of Contents

1. [Database Overview](#1-database-overview)
2. [Entity Relationship Diagram](#2-entity-relationship-diagram)
3. [Tables Schema](#3-tables-schema)
4. [Indexes](#4-indexes)
5. [Constraints](#5-constraints)
6. [Sample Data](#6-sample-data)

---

## 1. Database Overview

### 1.1 Database Design Principles

- **Normalization**: 3NF (Third Normal Form) to reduce redundancy
- **Scalability**: Designed for horizontal scaling with proper indexing
- **Performance**: Strategic indexes on frequently queried columns
- **Data Integrity**: Foreign keys, constraints, and validation
- **Audit Trail**: Created_at and updated_at timestamps on all tables
- **Soft Deletes**: deleted_at column for important entities

### 1.2 Database Extensions

```sql
-- PostGIS for geospatial queries
CREATE EXTENSION IF NOT EXISTS postgis;

-- TimescaleDB for time-series GPS data
CREATE EXTENSION IF NOT EXISTS timescaledb;

-- UUID generation
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

---

## 2. Entity Relationship Diagram

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│    Users    │◄───────►│   Sessions   │         │    Roles    │
└──────┬──────┘         └──────────────┘         └──────┬──────┘
       │                                                  │
       │                                                  │
       │                                         ┌────────▼────────┐
       │                                         │  User_Roles     │
       │                                         └─────────────────┘
       │
       ├─────────────────┬─────────────────┬──────────────────┐
       │                 │                 │                  │
┌──────▼──────┐   ┌─────▼──────┐   ┌─────▼──────┐   ┌──────▼──────┐
│  Students   │   │  Drivers   │   │   Admins   │   │   Profiles  │
└──────┬──────┘   └─────┬──────┘   └────────────┘   └─────────────┘
       │                │
       │                │
┌──────▼──────┐   ┌─────▼──────┐
│ Bus_Passes  │   │   Trips    │
└──────┬──────┘   └─────┬──────┘
       │                │
       │          ┌─────▼──────────┐
┌──────▼──────┐   │  Trip_Scans    │
│  Payments   │   └────────────────┘
└─────────────┘   
                  ┌─────▼──────────┐
                  │ GPS_Locations  │
                  └────────────────┘

┌──────────────┐         ┌──────────────┐         ┌─────────────┐
│    Buses     │◄───────►│Bus_Assignments│◄──────►│   Routes    │
└──────┬───────┘         └──────────────┘         └──────┬──────┘
       │                                                  │
       │                                          ┌───────▼──────┐
┌──────▼───────┐                                  │    Stops     │
│Maintenance   │                                  └───────┬──────┘
└──────────────┘                                          │
                                                  ┌───────▼──────┐
                                                  │ Route_Stops  │
                                                  └──────────────┘

┌──────────────┐
│Notifications │
└──────────────┘

┌──────────────┐
│Announcements │
└──────────────┘
```

---

## 3. Tables Schema

### 3.1 Users Table

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  email_verified BOOLEAN DEFAULT FALSE,
  email_verification_token VARCHAR(255),
  email_verification_expires_at TIMESTAMP,
  password_reset_token VARCHAR(255),
  password_reset_expires_at TIMESTAMP,
  last_login_at TIMESTAMP,
  login_attempts INTEGER DEFAULT 0,
  locked_until TIMESTAMP,
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_verification_token ON users(email_verification_token);
CREATE INDEX idx_users_reset_token ON users(password_reset_token);
```

### 3.2 Roles Table

```sql
CREATE TABLE roles (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name VARCHAR(50) UNIQUE NOT NULL, -- 'student', 'driver', 'admin', 'super_admin'
  description TEXT,
  permissions JSONB, -- Store permissions as JSON
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 3.3 User_Roles Table (Many-to-Many)

```sql
CREATE TABLE user_roles (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  role_id UUID NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
  assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE(user_id, role_id)
);

CREATE INDEX idx_user_roles_user_id ON user_roles(user_id);
CREATE INDEX idx_user_roles_role_id ON user_roles(role_id);
```

### 3.4 Profiles Table

```sql
CREATE TABLE profiles (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID UNIQUE NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  phone VARCHAR(20),
  avatar_url TEXT,
  date_of_birth DATE,
  address TEXT,
  city VARCHAR(100),
  postal_code VARCHAR(20),
  country VARCHAR(100) DEFAULT 'Pakistan',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_profiles_user_id ON profiles(user_id);
```

### 3.5 Students Table

```sql
CREATE TABLE students (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID UNIQUE NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  student_id VARCHAR(50) UNIQUE NOT NULL, -- University student ID
  department VARCHAR(100),
  year_of_study INTEGER,
  program VARCHAR(100),
  enrollment_date DATE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_students_user_id ON students(user_id);
CREATE INDEX idx_students_student_id ON students(student_id);
```

### 3.6 Drivers Table

```sql
CREATE TABLE drivers (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID UNIQUE NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  license_number VARCHAR(50) UNIQUE NOT NULL,
  license_expiry_date DATE NOT NULL,
  license_type VARCHAR(20), -- 'LTV', 'HTV', etc.
  experience_years INTEGER,
  emergency_contact_name VARCHAR(100),
  emergency_contact_phone VARCHAR(20),
  is_available BOOLEAN DEFAULT TRUE,
  current_status VARCHAR(20) DEFAULT 'idle', -- 'idle', 'on_trip', 'break', 'off_duty'
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_drivers_user_id ON drivers(user_id);
CREATE INDEX idx_drivers_license_number ON drivers(license_number);
CREATE INDEX idx_drivers_status ON drivers(current_status);
```

### 3.7 Buses Table

```sql
CREATE TABLE buses (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  registration_number VARCHAR(50) UNIQUE NOT NULL,
  model VARCHAR(100),
  manufacturer VARCHAR(100),
  year INTEGER,
  capacity INTEGER NOT NULL,
  color VARCHAR(50),
  fuel_type VARCHAR(20), -- 'diesel', 'petrol', 'cng', 'electric'
  status VARCHAR(20) DEFAULT 'active', -- 'active', 'inactive', 'maintenance'
  last_maintenance_date DATE,
  next_maintenance_date DATE,
  mileage INTEGER DEFAULT 0,
  image_url TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_buses_registration ON buses(registration_number);
CREATE INDEX idx_buses_status ON buses(status);
```

### 3.8 Routes Table

```sql
CREATE TABLE routes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name VARCHAR(100) NOT NULL,
  code VARCHAR(20) UNIQUE NOT NULL, -- e.g., 'R-01', 'R-02'
  description TEXT,
  color VARCHAR(7) DEFAULT '#3B82F6', -- Hex color for map display
  is_active BOOLEAN DEFAULT TRUE,
  start_point VARCHAR(255),
  end_point VARCHAR(255),
  estimated_duration INTEGER, -- in minutes
  distance DECIMAL(10, 2), -- in kilometers
  frequency VARCHAR(50), -- 'daily', 'weekdays', 'weekends'
  start_time TIME,
  end_time TIME,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_routes_code ON routes(code);
CREATE INDEX idx_routes_active ON routes(is_active);
```

### 3.9 Stops Table

```sql
CREATE TABLE stops (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name VARCHAR(255) NOT NULL,
  code VARCHAR(20) UNIQUE,
  description TEXT,
  latitude DECIMAL(10, 8) NOT NULL,
  longitude DECIMAL(11, 8) NOT NULL,
  location GEOGRAPHY(POINT, 4326), -- PostGIS geography type
  address TEXT,
  landmark VARCHAR(255),
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_stops_code ON stops(code);
CREATE INDEX idx_stops_location ON stops USING GIST(location);
CREATE INDEX idx_stops_active ON stops(is_active);
```

### 3.10 Route_Stops Table (Many-to-Many with Order)

```sql
CREATE TABLE route_stops (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  route_id UUID NOT NULL REFERENCES routes(id) ON DELETE CASCADE,
  stop_id UUID NOT NULL REFERENCES stops(id) ON DELETE CASCADE,
  stop_order INTEGER NOT NULL, -- Order of stop in route (1, 2, 3, ...)
  estimated_arrival_time TIME, -- Estimated time at this stop
  distance_from_previous DECIMAL(10, 2), -- km from previous stop
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  UNIQUE(route_id, stop_id),
  UNIQUE(route_id, stop_order)
);

CREATE INDEX idx_route_stops_route_id ON route_stops(route_id);
CREATE INDEX idx_route_stops_stop_id ON route_stops(stop_id);
CREATE INDEX idx_route_stops_order ON route_stops(route_id, stop_order);
```

### 3.11 Bus_Assignments Table

```sql
CREATE TABLE bus_assignments (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  bus_id UUID NOT NULL REFERENCES buses(id) ON DELETE CASCADE,
  route_id UUID NOT NULL REFERENCES routes(id) ON DELETE CASCADE,
  driver_id UUID REFERENCES drivers(id) ON DELETE SET NULL,
  assigned_date DATE NOT NULL,
  start_time TIME,
  end_time TIME,
  is_active BOOLEAN DEFAULT TRUE,
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_bus_assignments_bus_id ON bus_assignments(bus_id);
CREATE INDEX idx_bus_assignments_route_id ON bus_assignments(route_id);
CREATE INDEX idx_bus_assignments_driver_id ON bus_assignments(driver_id);
CREATE INDEX idx_bus_assignments_date ON bus_assignments(assigned_date);
CREATE INDEX idx_bus_assignments_active ON bus_assignments(is_active);
```

### 3.12 Trips Table

```sql
CREATE TABLE trips (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  bus_id UUID NOT NULL REFERENCES buses(id),
  route_id UUID NOT NULL REFERENCES routes(id),
  driver_id UUID NOT NULL REFERENCES drivers(id),
  assignment_id UUID REFERENCES bus_assignments(id),
  start_time TIMESTAMP NOT NULL,
  end_time TIMESTAMP,
  status VARCHAR(20) DEFAULT 'scheduled', -- 'scheduled', 'in_progress', 'completed', 'cancelled'
  start_odometer INTEGER,
  end_odometer INTEGER,
  distance_traveled DECIMAL(10, 2),
  total_passengers INTEGER DEFAULT 0,
  delay_minutes INTEGER DEFAULT 0,
  delay_reason TEXT,
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_trips_bus_id ON trips(bus_id);
CREATE INDEX idx_trips_route_id ON trips(route_id);
CREATE INDEX idx_trips_driver_id ON trips(driver_id);
CREATE INDEX idx_trips_status ON trips(status);
CREATE INDEX idx_trips_start_time ON trips(start_time);
```

### 3.13 GPS_Locations Table (Time-Series)

```sql
CREATE TABLE gps_locations (
  id UUID DEFAULT uuid_generate_v4(),
  trip_id UUID NOT NULL REFERENCES trips(id) ON DELETE CASCADE,
  bus_id UUID NOT NULL REFERENCES buses(id),
  latitude DECIMAL(10, 8) NOT NULL,
  longitude DECIMAL(11, 8) NOT NULL,
  location GEOGRAPHY(POINT, 4326), -- PostGIS geography type
  speed DECIMAL(6, 2), -- km/h
  bearing DECIMAL(5, 2), -- 0-360 degrees
  accuracy DECIMAL(6, 2), -- meters
  altitude DECIMAL(8, 2), -- meters
  timestamp TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Convert to TimescaleDB hypertable for time-series optimization
SELECT create_hypertable('gps_locations', 'timestamp', chunk_time_interval => INTERVAL '1 day');

CREATE INDEX idx_gps_trip_id ON gps_locations(trip_id, timestamp DESC);
CREATE INDEX idx_gps_bus_id ON gps_locations(bus_id, timestamp DESC);
CREATE INDEX idx_gps_location ON gps_locations USING GIST(location);
```

### 3.14 Bus_Passes Table

```sql
CREATE TABLE bus_passes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  student_id UUID NOT NULL REFERENCES students(id) ON DELETE CASCADE,
  pass_type VARCHAR(20) NOT NULL, -- 'daily', 'weekly', 'monthly', 'semester'
  price DECIMAL(10, 2) NOT NULL,
  purchase_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  activation_date TIMESTAMP,
  expiry_date TIMESTAMP NOT NULL,
  status VARCHAR(20) DEFAULT 'active', -- 'active', 'expired', 'cancelled', 'pending'
  qr_code TEXT UNIQUE NOT NULL, -- Encrypted QR code data
  qr_code_url TEXT, -- URL to QR code image (if stored)
  payment_id UUID REFERENCES payments(id),
  usage_count INTEGER DEFAULT 0,
  max_usage INTEGER, -- Optional: limit scans per day
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_bus_passes_student_id ON bus_passes(student_id);
CREATE INDEX idx_bus_passes_qr_code ON bus_passes(qr_code);
CREATE INDEX idx_bus_passes_status ON bus_passes(status);
CREATE INDEX idx_bus_passes_expiry ON bus_passes(expiry_date);
```

### 3.15 Payments Table

```sql
CREATE TABLE payments (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  student_id UUID NOT NULL REFERENCES students(id),
  amount DECIMAL(10, 2) NOT NULL,
  currency VARCHAR(3) DEFAULT 'PKR',
  payment_method VARCHAR(50), -- 'card', 'mobile_wallet', 'bank_transfer'
  status VARCHAR(20) DEFAULT 'pending', -- 'pending', 'completed', 'failed', 'refunded'
  gateway_transaction_id VARCHAR(255), -- KuickPay transaction ID
  gateway_response JSONB, -- Store full gateway response
  payment_date TIMESTAMP,
  refund_date TIMESTAMP,
  refund_reason TEXT,
  receipt_url TEXT,
  metadata JSONB, -- Additional payment info
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_payments_student_id ON payments(student_id);
CREATE INDEX idx_payments_status ON payments(status);
CREATE INDEX idx_payments_transaction_id ON payments(gateway_transaction_id);
CREATE INDEX idx_payments_date ON payments(payment_date);
```

### 3.16 Trip_Scans Table

```sql
CREATE TABLE trip_scans (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  trip_id UUID NOT NULL REFERENCES trips(id) ON DELETE CASCADE,
  bus_pass_id UUID NOT NULL REFERENCES bus_passes(id),
  student_id UUID NOT NULL REFERENCES students(id),
  driver_id UUID NOT NULL REFERENCES drivers(id),
  scan_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  location GEOGRAPHY(POINT, 4326),
  is_valid BOOLEAN DEFAULT TRUE,
  validation_message TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_trip_scans_trip_id ON trip_scans(trip_id);
CREATE INDEX idx_trip_scans_pass_id ON trip_scans(bus_pass_id);
CREATE INDEX idx_trip_scans_student_id ON trip_scans(student_id);
CREATE INDEX idx_trip_scans_scan_time ON trip_scans(scan_time);
```

### 3.17 Notifications Table

```sql
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  type VARCHAR(50) NOT NULL, -- 'bus_arrival', 'pass_expiry', 'payment', 'delay', 'announcement'
  title VARCHAR(255) NOT NULL,
  message TEXT NOT NULL,
  data JSONB, -- Additional notification data
  is_read BOOLEAN DEFAULT FALSE,
  read_at TIMESTAMP,
  sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  expires_at TIMESTAMP,
  priority VARCHAR(20) DEFAULT 'normal', -- 'low', 'normal', 'high', 'urgent'
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_is_read ON notifications(user_id, is_read);
CREATE INDEX idx_notifications_type ON notifications(type);
CREATE INDEX idx_notifications_sent_at ON notifications(sent_at);
```

### 3.18 Announcements Table

```sql
CREATE TABLE announcements (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  created_by UUID NOT NULL REFERENCES users(id),
  title VARCHAR(255) NOT NULL,
  content TEXT NOT NULL,
  target_audience VARCHAR(20) DEFAULT 'all', -- 'all', 'students', 'drivers'
  priority VARCHAR(20) DEFAULT 'normal',
  is_active BOOLEAN DEFAULT TRUE,
  publish_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  expiry_date TIMESTAMP,
  image_url TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_announcements_active ON announcements(is_active);
CREATE INDEX idx_announcements_audience ON announcements(target_audience);
CREATE INDEX idx_announcements_publish_date ON announcements(publish_date);
```

### 3.19 Maintenance_Records Table

```sql
CREATE TABLE maintenance_records (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  bus_id UUID NOT NULL REFERENCES buses(id) ON DELETE CASCADE,
  maintenance_type VARCHAR(50) NOT NULL, -- 'routine', 'repair', 'inspection'
  description TEXT NOT NULL,
  cost DECIMAL(10, 2),
  scheduled_date DATE NOT NULL,
  completed_date DATE,
  status VARCHAR(20) DEFAULT 'scheduled', -- 'scheduled', 'in_progress', 'completed', 'cancelled'
  mechanic_name VARCHAR(100),
  notes TEXT,
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_maintenance_bus_id ON maintenance_records(bus_id);
CREATE INDEX idx_maintenance_status ON maintenance_records(status);
CREATE INDEX idx_maintenance_scheduled_date ON maintenance_records(scheduled_date);
```

### 3.20 Sessions Table (Optional - for JWT alternative)

```sql
CREATE TABLE sessions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  token VARCHAR(500) UNIQUE NOT NULL,
  refresh_token VARCHAR(500) UNIQUE,
  ip_address INET,
  user_agent TEXT,
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_sessions_user_id ON sessions(user_id);
CREATE INDEX idx_sessions_token ON sessions(token);
CREATE INDEX idx_sessions_expires_at ON sessions(expires_at);
```

### 3.21 Audit_Logs Table

```sql
CREATE TABLE audit_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id),
  action VARCHAR(100) NOT NULL, -- 'create', 'update', 'delete', 'login', 'logout'
  entity_type VARCHAR(50) NOT NULL, -- 'user', 'bus', 'route', 'pass', etc.
  entity_id UUID,
  old_values JSONB,
  new_values JSONB,
  ip_address INET,
  user_agent TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_entity ON audit_logs(entity_type, entity_id);
CREATE INDEX idx_audit_logs_created_at ON audit_logs(created_at);
```

---

## 4. Indexes

### 4.1 Performance Indexes

```sql
-- Composite indexes for common queries

-- Find active trips with bus and route info
CREATE INDEX idx_trips_active_with_details ON trips(status, bus_id, route_id) 
WHERE status = 'in_progress';

-- Find active passes for students
CREATE INDEX idx_active_passes ON bus_passes(student_id, status, expiry_date) 
WHERE status = 'active';

-- Find recent GPS locations for live tracking
CREATE INDEX idx_recent_gps_locations ON gps_locations(bus_id, timestamp DESC);

-- Find unread notifications
CREATE INDEX idx_unread_notifications ON notifications(user_id, is_read, sent_at DESC) 
WHERE is_read = FALSE;

-- Search users by email pattern
CREATE INDEX idx_users_email_pattern ON users(email varchar_pattern_ops);
```

### 4.2 Full-Text Search Indexes (Optional)

```sql
-- Full-text search on routes
ALTER TABLE routes ADD COLUMN search_vector tsvector;

CREATE INDEX idx_routes_search ON routes USING gin(search_vector);

-- Update search vector on insert/update
CREATE TRIGGER routes_search_update BEFORE INSERT OR UPDATE ON routes
FOR EACH ROW EXECUTE FUNCTION
tsvector_update_trigger(search_vector, 'pg_catalog.english', name, description);
```

---

## 5. Constraints

### 5.1 Check Constraints

```sql
-- Ensure valid coordinates
ALTER TABLE stops ADD CONSTRAINT chk_stops_latitude 
  CHECK (latitude >= -90 AND latitude <= 90);

ALTER TABLE stops ADD CONSTRAINT chk_stops_longitude 
  CHECK (longitude >= -180 AND longitude <= 180);

-- Ensure positive values
ALTER TABLE buses ADD CONSTRAINT chk_buses_capacity 
  CHECK (capacity > 0);

ALTER TABLE payments ADD CONSTRAINT chk_payments_amount 
  CHECK (amount >= 0);

-- Ensure valid dates
ALTER TABLE bus_passes ADD CONSTRAINT chk_passes_dates 
  CHECK (expiry_date > purchase_date);

ALTER TABLE trips ADD CONSTRAINT chk_trips_times 
  CHECK (end_time IS NULL OR end_time > start_time);

-- Ensure valid enums
ALTER TABLE users ADD CONSTRAINT chk_users_email 
  CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');
```

### 5.2 Unique Constraints

```sql
-- Prevent duplicate active assignments for same bus on same day
CREATE UNIQUE INDEX idx_unique_active_assignment 
ON bus_assignments(bus_id, assigned_date, is_active) 
WHERE is_active = TRUE;

-- Prevent multiple active trips for same bus
CREATE UNIQUE INDEX idx_unique_active_trip 
ON trips(bus_id, status) 
WHERE status = 'in_progress';
```

---

## 6. Sample Data

### 6.1 Insert Roles

```sql
INSERT INTO roles (name, description, permissions) VALUES
('student', 'Student user role', '{"can_view_buses": true, "can_purchase_pass": true}'::jsonb),
('driver', 'Bus driver role', '{"can_manage_trips": true, "can_scan_passes": true}'::jsonb),
('admin', 'Administrator role', '{"can_manage_all": true}'::jsonb),
('super_admin', 'Super administrator', '{"full_access": true}'::jsonb);
```

### 6.2 Insert Sample User

```sql
-- Password: Password123!
INSERT INTO users (email, password_hash, email_verified) VALUES
('student@university.edu', '$2b$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/LewY5oe2sOLjDX8ey', TRUE);

INSERT INTO profiles (user_id, first_name, last_name, phone) VALUES
((SELECT id FROM users WHERE email = 'student@university.edu'), 'Ali', 'Ahmed', '+92-300-1234567');

INSERT INTO students (user_id, student_id, department, year_of_study) VALUES
((SELECT id FROM users WHERE email = 'student@university.edu'), 'CS-2021-001', 'Computer Science', 3);

INSERT INTO user_roles (user_id, role_id) VALUES
((SELECT id FROM users WHERE email = 'student@university.edu'), 
 (SELECT id FROM roles WHERE name = 'student'));
```

### 6.3 Insert Sample Bus

```sql
INSERT INTO buses (registration_number, model, manufacturer, capacity, status) VALUES
('ISB-123', 'Coaster', 'Toyota', 30, 'active'),
('ISB-124', 'Sprinter', 'Mercedes', 25, 'active'),
('ISB-125', 'Rosa', 'Mitsubishi', 28, 'active');
```

### 6.4 Insert Sample Route

```sql
INSERT INTO routes (name, code, description, color, is_active, distance) VALUES
('Main Campus to City', 'R-01', 'Route from main campus to city center', '#3B82F6', TRUE, 15.5),
('Hostel to Campus', 'R-02', 'Route from student hostel to campus', '#10B981', TRUE, 5.2);
```

### 6.5 Insert Sample Stops

```sql
INSERT INTO stops (name, code, latitude, longitude, location, address) VALUES
('Main Campus Gate', 'S-01', 33.6844, 73.0479, ST_SetSRID(ST_MakePoint(73.0479, 33.6844), 4326), 'University Main Gate'),
('Library Stop', 'S-02', 33.6850, 73.0485, ST_SetSRID(ST_MakePoint(73.0485, 33.6850), 4326), 'Central Library'),
('City Center', 'S-03', 33.6990, 73.0645, ST_SetSRID(ST_MakePoint(73.0645, 33.6990), 4326), 'City Center Plaza');
```

### 6.6 Link Route with Stops

```sql
INSERT INTO route_stops (route_id, stop_id, stop_order, estimated_arrival_time) VALUES
((SELECT id FROM routes WHERE code = 'R-01'), (SELECT id FROM stops WHERE code = 'S-01'), 1, '08:00:00'),
((SELECT id FROM routes WHERE code = 'R-01'), (SELECT id FROM stops WHERE code = 'S-02'), 2, '08:10:00'),
((SELECT id FROM routes WHERE code = 'R-01'), (SELECT id FROM stops WHERE code = 'S-03'), 3, '08:30:00');
```

---

## 7. Views (Optional)

### 7.1 Active Buses View

```sql
CREATE VIEW v_active_buses AS
SELECT 
  b.id,
  b.registration_number,
  b.model,
  b.capacity,
  r.name AS route_name,
  r.code AS route_code,
  d.license_number,
  u.email AS driver_email,
  p.first_name || ' ' || p.last_name AS driver_name
FROM buses b
LEFT JOIN bus_assignments ba ON b.id = ba.bus_id AND ba.is_active = TRUE
LEFT JOIN routes r ON ba.route_id = r.id
LEFT JOIN drivers d ON ba.driver_id = d.id
LEFT JOIN users u ON d.user_id = u.id
LEFT JOIN profiles p ON u.id = p.user_id
WHERE b.status = 'active' AND b.deleted_at IS NULL;
```

### 7.2 Student Pass Summary View

```sql
CREATE VIEW v_student_pass_summary AS
SELECT 
  s.id AS student_id,
  s.student_id,
  u.email,
  p.first_name || ' ' || p.last_name AS full_name,
  COUNT(bp.id) AS total_passes,
  COUNT(bp.id) FILTER (WHERE bp.status = 'active') AS active_passes,
  SUM(pay.amount) FILTER (WHERE pay.status = 'completed') AS total_spent
FROM students s
JOIN users u ON s.user_id = u.id
JOIN profiles p ON u.id = p.user_id
LEFT JOIN bus_passes bp ON s.id = bp.student_id
LEFT JOIN payments pay ON bp.payment_id = pay.id
GROUP BY s.id, s.student_id, u.email, p.first_name, p.last_name;
```

---

## 8. Functions & Triggers

### 8.1 Update Timestamp Trigger

```sql
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = CURRENT_TIMESTAMP;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Apply to all tables with updated_at
CREATE TRIGGER update_users_updated_at BEFORE UPDATE ON users
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_buses_updated_at BEFORE UPDATE ON buses
  FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

-- Apply to other tables...
```

### 8.2 Auto-expire Passes Function

```sql
CREATE OR REPLACE FUNCTION auto_expire_passes()
RETURNS void AS $$
BEGIN
  UPDATE bus_passes
  SET status = 'expired'
  WHERE status = 'active' 
  AND expiry_date < CURRENT_TIMESTAMP;
END;
$$ LANGUAGE plpgsql;

-- Schedule with cron (using pg_cron extension)
-- SELECT cron.schedule('expire-passes', '0 0 * * *', 'SELECT auto_expire_passes()');
```

---

**END OF DATABASE SCHEMA DOCUMENT**

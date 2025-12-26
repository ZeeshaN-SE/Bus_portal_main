# Phase 3: Bus & Route Management
## University Bus Tracker System

**Duration:** 2-3 weeks  
**Status:** 🟡 Not Started  
**Priority:** 🔴 Critical  
**Depends On:** Phase 2 ✅

---

## 🎯 Goal

Implement the core bus and route management system. Allow admins to create and manage buses, routes, stops, and bus assignments. Display routes and buses to students.

---

## 📋 Key Features / Tasks

### 1. Database Schema - Buses, Routes & Stops
- [ ] Create Buses model (registration_number, model, capacity, status, etc.)
- [ ] Create Routes model (name, code, description, color, distance, etc.)
- [ ] Create Stops model (name, code, latitude, longitude, location geography)
- [ ] Create RouteStops junction table (route_id, stop_id, stop_order)
- [ ] Create BusAssignments model (bus_id, route_id, driver_id, date)
- [ ] Create MaintenanceRecords model (bus_id, type, date, cost, status)
- [ ] Enable PostGIS extension for geography types
- [ ] Run migrations and seed with sample data

### 2. Backend - Bus Management API
- [ ] Create bus (POST /admin/buses)
- [ ] Get all buses (GET /admin/buses) - with pagination and filters
- [ ] Get bus by ID (GET /admin/buses/:id)
- [ ] Update bus (PATCH /admin/buses/:id)
- [ ] Delete bus (DELETE /admin/buses/:id)
- [ ] Get active buses (GET /buses/active)
- [ ] Upload bus image (POST /admin/buses/:id/image)
- [ ] Get bus maintenance records (GET /admin/buses/:id/maintenance)
- [ ] Create maintenance record (POST /admin/buses/:id/maintenance)

### 3. Backend - Route Management API
- [ ] Create route (POST /admin/routes)
- [ ] Get all routes (GET /routes) - public endpoint
- [ ] Get route by ID (GET /routes/:id) - with stops
- [ ] Update route (PATCH /admin/routes/:id)
- [ ] Delete route (DELETE /admin/routes/:id)
- [ ] Add stop to route (POST /admin/routes/:id/stops)
- [ ] Reorder route stops (PATCH /admin/routes/:id/stops/reorder)
- [ ] Remove stop from route (DELETE /admin/routes/:id/stops/:stopId)
- [ ] Calculate route distance (using Mapbox Directions API)

### 4. Backend - Stop Management API
- [ ] Create stop (POST /admin/stops)
- [ ] Get all stops (GET /stops)
- [ ] Get stop by ID (GET /stops/:id)
- [ ] Update stop (PATCH /admin/stops/:id)
- [ ] Delete stop (DELETE /admin/stops/:id)
- [ ] Find nearby stops (GET /stops/nearby?lat=x&lng=y&radius=500)
- [ ] Get stops on route (GET /routes/:id/stops)

### 5. Backend - Bus Assignment API
- [ ] Create assignment (POST /admin/assignments)
- [ ] Get assignments (GET /admin/assignments) - with filters
- [ ] Update assignment (PATCH /admin/assignments/:id)
- [ ] Delete assignment (DELETE /admin/assignments/:id)
- [ ] Get current assignment for bus (GET /buses/:id/current-assignment)
- [ ] Get assignments by date (GET /admin/assignments?date=YYYY-MM-DD)

### 6. Admin Dashboard - Bus Management
- [ ] Buses List Page (table view)
- [ ] Create Bus Form (modal or page)
- [ ] Edit Bus Form
- [ ] Bus Detail Page (show all info, maintenance history)
- [ ] Bus image upload
- [ ] Bus status toggle (active/inactive/maintenance)
- [ ] Search and filter buses
- [ ] Export buses to CSV

### 7. Admin Dashboard - Route Management
- [ ] Routes List Page
- [ ] Create Route Form with map integration
- [ ] Edit Route Form
- [ ] Route Detail Page with map showing full route
- [ ] Route Builder (drag-and-drop stops on map)
- [ ] Stop selection from map
- [ ] Reorder stops functionality
- [ ] Route visualization with Mapbox
- [ ] Calculate and display route distance

### 8. Admin Dashboard - Stop Management
- [ ] Stops List Page with map view
- [ ] Create Stop Form with map picker
- [ ] Edit Stop Form
- [ ] Stop Detail Page
- [ ] Map view showing all stops
- [ ] Click on map to add stop
- [ ] Search stops by name or location

### 9. Admin Dashboard - Bus Assignment
- [ ] Assignment Calendar View
- [ ] Create Assignment Form (select bus, route, driver, date)
- [ ] Edit Assignment
- [ ] View assignments by date
- [ ] View assignments by bus
- [ ] View assignments by route
- [ ] Conflict detection (same bus assigned twice)

### 10. Mobile Apps - Routes & Stops View
- [ ] Routes List Screen (student app)
- [ ] Route Detail Screen with map
- [ ] Stops List Screen
- [ ] Stop Detail Screen
- [ ] Map view of all routes
- [ ] Filter routes by active/inactive
- [ ] Show bus count per route
- [ ] Show estimated travel time
- [ ] Favorite routes functionality

### 11. Mapbox Integration
- [ ] Set up Mapbox SDK in mobile apps (@rnmapbox/maps)
- [ ] Set up Mapbox GL JS in admin dashboard (react-map-gl)
- [ ] Display interactive maps
- [ ] Add markers for stops
- [ ] Draw route polylines
- [ ] Implement geocoding (address to coordinates)
- [ ] Implement reverse geocoding (coordinates to address)
- [ ] Use Directions API for route calculation
- [ ] Custom map styling (optional)

### 12. Geospatial Queries (PostGIS)
- [ ] Find stops within radius (ST_DWithin)
- [ ] Calculate distance between points (ST_Distance)
- [ ] Order stops by proximity (ST_Distance ordering)
- [ ] Validate stop coordinates are valid
- [ ] Create spatial indexes on geography columns

### 13. Testing
- [ ] Unit tests for bus service
- [ ] Unit tests for route service
- [ ] Unit tests for stop service
- [ ] Integration tests for all endpoints
- [ ] Test geospatial queries
- [ ] Test Mapbox integration
- [ ] Test file upload (bus images)
- [ ] Test validation and error handling

---

## 📦 Deliverables

### Backend Deliverables
- ✅ Bus management API (9 endpoints)
- ✅ Route management API (8 endpoints)
- ✅ Stop management API (6 endpoints)
- ✅ Bus assignment API (6 endpoints)
- ✅ Geospatial queries with PostGIS
- ✅ File upload for bus images
- ✅ Database migrations for all tables
- ✅ Seed data with sample buses, routes, stops

### Admin Dashboard Deliverables
- ✅ Complete bus management interface
- ✅ Route builder with map integration
- ✅ Stop management with map picker
- ✅ Assignment calendar view
- ✅ Mapbox map integration
- ✅ Search, filter, and export functionality

### Mobile App Deliverables
- ✅ Routes list and detail screens
- ✅ Stops list and detail screens
- ✅ Map view with routes and stops
- ✅ Mapbox integration
- ✅ Favorite routes feature

### Documentation Deliverables
- ✅ API documentation for all endpoints
- ✅ Mapbox integration guide
- ✅ PostGIS query examples
- ✅ Route builder user guide

---

## 🔧 Technical Implementation

### Database Schema (Prisma)

```prisma
model Bus {
  id                     String    @id @default(uuid())
  registration_number    String    @unique
  model                  String
  manufacturer           String
  year                   Int?
  capacity               Int
  color                  String?
  fuel_type              String?   // diesel, petrol, cng, electric
  status                 String    @default("active") // active, inactive, maintenance
  last_maintenance_date  DateTime?
  next_maintenance_date  DateTime?
  mileage                Int       @default(0)
  image_url              String?
  created_at             DateTime  @default(now())
  updated_at             DateTime  @updatedAt
  deleted_at             DateTime?

  assignments          BusAssignment[]
  maintenance_records  MaintenanceRecord[]
}

model Route {
  id                  String    @id @default(uuid())
  name                String
  code                String    @unique
  description         String?
  color               String    @default("#3B82F6")
  is_active           Boolean   @default(true)
  start_point         String
  end_point           String
  estimated_duration  Int?      // in minutes
  distance            Decimal?  // in kilometers
  frequency           String?   // daily, weekdays, weekends
  start_time          String?   // 08:00:00
  end_time            String?   // 22:00:00
  created_at          DateTime  @default(now())
  updated_at          DateTime  @updatedAt
  deleted_at          DateTime?

  route_stops  RouteStop[]
  assignments  BusAssignment[]
}

model Stop {
  id          String    @id @default(uuid())
  name        String
  code        String?   @unique
  description String?
  latitude    Decimal
  longitude   Decimal
  location    Unsupported("geography(Point, 4326)")? // PostGIS type
  address     String?
  landmark    String?
  is_active   Boolean   @default(true)
  created_at  DateTime  @default(now())
  updated_at  DateTime  @updatedAt
  deleted_at  DateTime?

  route_stops RouteStop[]
}

model RouteStop {
  id                      String   @id @default(uuid())
  route_id                String
  stop_id                 String
  stop_order              Int
  estimated_arrival_time  String?  // 08:15:00
  distance_from_previous  Decimal? // in km
  created_at              DateTime @default(now())

  route Route @relation(fields: [route_id], references: [id], onDelete: Cascade)
  stop  Stop  @relation(fields: [stop_id], references: [id], onDelete: Cascade)

  @@unique([route_id, stop_id])
  @@unique([route_id, stop_order])
}

model BusAssignment {
  id             String   @id @default(uuid())
  bus_id         String
  route_id       String
  driver_id      String?
  assigned_date  DateTime
  start_time     String?
  end_time       String?
  is_active      Boolean  @default(true)
  notes          String?
  created_at     DateTime @default(now())
  updated_at     DateTime @updatedAt

  bus    Bus    @relation(fields: [bus_id], references: [id], onDelete: Cascade)
  route  Route  @relation(fields: [route_id], references: [id], onDelete: Cascade)
  driver Driver? @relation(fields: [driver_id], references: [id], onDelete: SetNull)
}

model MaintenanceRecord {
  id              String   @id @default(uuid())
  bus_id          String
  maintenance_type String  // routine, repair, inspection
  description     String
  cost            Decimal?
  scheduled_date  DateTime
  completed_date  DateTime?
  status          String   @default("scheduled") // scheduled, in_progress, completed, cancelled
  mechanic_name   String?
  notes           String?
  created_by      String?
  created_at      DateTime @default(now())
  updated_at      DateTime @updatedAt

  bus Bus @relation(fields: [bus_id], references: [id], onDelete: Cascade)
}
```

### PostGIS Queries

```typescript
// Find stops within 500m of a location
const nearbyStops = await prisma.$queryRaw`
  SELECT id, name, latitude, longitude, address,
         ST_Distance(location, ST_SetSRID(ST_MakePoint(${lng}, ${lat}), 4326)::geography) as distance
  FROM stops
  WHERE ST_DWithin(
    location,
    ST_SetSRID(ST_MakePoint(${lng}, ${lat}), 4326)::geography,
    ${radiusMeters}
  )
  AND deleted_at IS NULL
  ORDER BY distance
  LIMIT 10
`;

// Calculate distance between two stops
const distance = await prisma.$queryRaw`
  SELECT ST_Distance(
    (SELECT location FROM stops WHERE id = ${stop1Id})::geography,
    (SELECT location FROM stops WHERE id = ${stop2Id})::geography
  ) / 1000 as distance_km
`;
```

---

## 🚀 Execution Prompts

### Prompt 1: Create Bus Management Backend
```
Implement bus management API for admin:

1. Database Model (Prisma):
   - Bus model with all fields (registration_number, model, capacity, status, etc.)
   - MaintenanceRecord model linked to Bus
   - Proper indexes and constraints

2. API Endpoints:
   - POST /api/v1/admin/buses - Create bus
   - GET /api/v1/admin/buses - List all buses (paginated, filterable)
   - GET /api/v1/admin/buses/:id - Get bus details
   - PATCH /api/v1/admin/buses/:id - Update bus
   - DELETE /api/v1/admin/buses/:id - Soft delete bus
   - GET /api/v1/buses/active - Get active buses (public)
   - POST /api/v1/admin/buses/:id/image - Upload bus image
   - GET /api/v1/admin/buses/:id/maintenance - Get maintenance records
   - POST /api/v1/admin/buses/:id/maintenance - Add maintenance record

3. Features:
   - Validation: unique registration_number, capacity > 0, valid status
   - Filter by: status, model, year
   - Search by: registration_number, model
   - Pagination (default 20 per page)
   - File upload for bus images (multer + sharp for resizing)
   - Store images in S3 or local filesystem
   - Admin-only access (except /buses/active)

4. Business Logic:
   - Cannot delete bus with active assignments
   - Auto-update status to 'maintenance' when maintenance record added
   - Validate maintenance dates (scheduled_date < completed_date)

Include TypeScript types, validation, error handling, and tests.
```

### Prompt 2: Create Route Management Backend
```
Implement route management API:

1. Database Models (Prisma):
   - Route model with all fields
   - Stop model with PostGIS geography type
   - RouteStop junction table with stop_order
   - Proper spatial indexes on Stop.location

2. API Endpoints:
   - POST /api/v1/admin/routes - Create route
   - GET /api/v1/routes - List all routes (public)
   - GET /api/v1/routes/:id - Get route with stops
   - PATCH /api/v1/admin/routes/:id - Update route
   - DELETE /api/v1/admin/routes/:id - Delete route
   - POST /api/v1/admin/routes/:id/stops - Add stop to route
   - PATCH /api/v1/admin/routes/:id/stops/reorder - Reorder stops
   - DELETE /api/v1/admin/routes/:id/stops/:stopId - Remove stop

3. Features:
   - Validate route code is unique
   - Calculate route distance using Mapbox Directions API
   - Store stops in correct order
   - Return route with stops ordered by stop_order
   - Filter routes by is_active
   - Search routes by name or code

4. Mapbox Integration:
   - Use Mapbox Directions API to calculate route distance
   - Get turn-by-turn directions
   - Calculate estimated travel time
   - Store polyline geometry (optional)

5. Geospatial:
   - Validate stop coordinates (latitude: -90 to 90, longitude: -180 to 180)
   - Use PostGIS to store geography points
   - Create spatial index on Stop.location

Include validation, error handling, and integration with Mapbox API.
```

### Prompt 3: Create Stop Management Backend
```
Implement stop management API with geospatial features:

1. API Endpoints:
   - POST /api/v1/admin/stops - Create stop
   - GET /api/v1/stops - List all stops (public)
   - GET /api/v1/stops/:id - Get stop details
   - PATCH /api/v1/admin/stops/:id - Update stop
   - DELETE /api/v1/admin/stops/:id - Delete stop
   - GET /api/v1/stops/nearby?lat=x&lng=y&radius=500 - Find nearby stops

2. Features:
   - Store coordinates as PostGIS geography type
   - Use ST_SetSRID(ST_MakePoint(lng, lat), 4326) for creating points
   - Validate coordinates are within valid ranges
   - Geocoding: convert address to coordinates using Mapbox Geocoding API
   - Reverse geocoding: get address from coordinates
   - Cannot delete stop if it's part of any route

3. Geospatial Queries (PostGIS):
   - Find stops within radius: ST_DWithin(location, point, radius)
   - Calculate distance: ST_Distance(location1, location2)
   - Order by proximity: ORDER BY ST_Distance(location, point)
   - All distances in meters/kilometers

4. Validation:
   - Latitude: -90 to 90
   - Longitude: -180 to 180
   - Name is required and unique per area
   - Code is unique if provided

Include PostGIS raw queries, proper TypeScript types, and error handling.
```

### Prompt 4: Create Bus Assignment Backend
```
Implement bus assignment system:

1. API Endpoints:
   - POST /api/v1/admin/assignments - Create assignment
   - GET /api/v1/admin/assignments - List assignments (filtered)
   - GET /api/v1/admin/assignments/:id - Get assignment details
   - PATCH /api/v1/admin/assignments/:id - Update assignment
   - DELETE /api/v1/admin/assignments/:id - Delete assignment
   - GET /api/v1/buses/:id/current-assignment - Get current assignment

2. Features:
   - Assign bus to route for specific date
   - Assign driver to assignment (optional in this phase)
   - Validate no double-booking (same bus, same date, overlapping times)
   - Filter by: bus_id, route_id, date range, is_active
   - Get assignments for specific date
   - Get assignments for date range

3. Validation:
   - Bus must exist and be active
   - Route must exist and be active
   - Date cannot be in the past (for new assignments)
   - No conflicting assignments for same bus on same date
   - Start time < end time

4. Business Logic:
   - Auto-set is_active to false when date passes
   - Mark as active only for current date assignments
   - Cannot delete assignment if trip has started

Include validation, conflict detection, and proper error handling.
```

### Prompt 5: Create Admin Dashboard - Bus Management UI
```
Create bus management interface for admin dashboard:

1. BusesListPage.tsx:
   - Data table with columns: Image, Registration, Model, Capacity, Status, Actions
   - Search bar (by registration or model)
   - Filter dropdown (status: all, active, inactive, maintenance)
   - "Add New Bus" button
   - Actions: View, Edit, Delete
   - Pagination
   - Export to CSV button

2. CreateBusModal.tsx or CreateBusPage.tsx:
   - Form fields: registration_number, model, manufacturer, year, capacity, color, fuel_type
   - Bus image upload with preview
   - Status dropdown
   - Validation (all required fields)
   - Save button with loading state

3. EditBusModal.tsx:
   - Same as create form but pre-filled with existing data
   - Cannot change registration_number
   - Update button

4. BusDetailPage.tsx:
   - Display all bus information
   - Bus image
   - Status badge
   - Edit and Delete buttons
   - Maintenance history section (table)
   - Current assignment section
   - "Add Maintenance Record" button

5. AddMaintenanceModal.tsx:
   - Form: type, description, scheduled_date, cost
   - Save button

Use Material-UI or Ant Design, proper state management, and error handling.
```

### Prompt 6: Create Admin Dashboard - Route Builder
```
Create route management with interactive map:

1. RoutesListPage.tsx:
   - Table with columns: Code, Name, Distance, Stops Count, Active, Actions
   - Search and filter
   - "Create Route" button
   - Actions: View, Edit, Delete

2. CreateRoutePage.tsx:
   - Left panel: Route form (name, code, description, color, start_point, end_point)
   - Right panel: Interactive Mapbox map
   - Stop selection: Click on map or search stops list
   - Selected stops list with drag-to-reorder functionality
   - Map shows: all available stops (markers), selected stops (different color), route polyline
   - "Calculate Distance" button (uses Mapbox Directions API)
   - Save button

3. RouteDetailPage.tsx:
   - Route information
   - Map showing full route with all stops
   - Stops list with order, arrival time
   - Edit and Delete buttons
   - Assign Bus button

4. Mapbox Integration:
   - Initialize map with react-map-gl
   - Add markers for stops (using <Marker> component)
   - Draw route polyline (using <Source> and <Layer>)
   - Click on marker to select stop
   - Use Mapbox Directions API to calculate route between stops
   - Display route on map

5. Features:
   - Drag and drop to reorder stops
   - Search stops by name
   - Filter stops by location
   - Auto-calculate distance when stops reordered
   - Color picker for route color
   - Real-time map preview

Include proper state management, Mapbox integration, and validation.
```

### Prompt 7: Create Admin Dashboard - Stop Management
```
Create stop management with map picker:

1. StopsListPage.tsx:
   - Split view: Left side = stops list, Right side = map with all stops
   - Stops list with: name, code, address, actions
   - Search stops by name
   - Filter by active/inactive
   - "Add Stop" button
   - Click on map marker to view stop details

2. CreateStopModal.tsx:
   - Modal with map picker
   - Click on map to set location (latitude, longitude)
   - Form fields: name, code (optional), address, landmark
   - Can also enter coordinates manually
   - Reverse geocode: auto-fill address when coordinates selected
   - Save button

3. EditStopModal.tsx:
   - Same as create but pre-filled
   - Can drag marker to change location
   - Update button

4. StopDetailPage.tsx:
   - Stop information
   - Map showing stop location
   - List of routes using this stop
   - Edit and Delete buttons
   - Cannot delete if used in any route

5. Map Features:
   - Display all stops as markers
   - Custom marker icons
   - Marker clustering for many stops
   - Click marker to show stop info popup
   - Search location on map
   - Zoom to specific stop

Use react-map-gl, proper state management, and error handling.
```

### Prompt 8: Create Mobile App - Routes & Stops
```
Create routes and stops screens for student mobile app:

1. RoutesListScreen.tsx:
   - List of all routes (FlatList)
   - Each item shows: route name, code, color badge, stops count, estimated time
   - Search bar
   - Filter: active/all routes
   - Pull-to-refresh
   - Tap to navigate to RouteDetailScreen

2. RouteDetailScreen.tsx:
   - Route information at top (name, description, distance, time)
   - Mapbox map showing route with all stops
   - Stops list below map (scrollable)
   - Each stop: name, estimated arrival time, distance from previous
   - "View on Map" button
   - "Add to Favorites" button

3. StopsListScreen.tsx:
   - List of all stops
   - Each item: name, address, routes count
   - Search bar
   - Show distance from user's location (if location permission granted)
   - Tap to navigate to StopDetailScreen

4. StopDetailScreen.tsx:
   - Stop information
   - Map showing stop location
   - List of routes passing through this stop
   - "Get Directions" button (open in maps app)
   - Upcoming buses at this stop (placeholder for now)

5. Mapbox Integration:
   - Use @rnmapbox/maps
   - Display route as polyline
   - Add markers for stops
   - Custom marker icons
   - Map camera animations
   - User location marker (if permission granted)

6. Features:
   - Favorite routes (stored locally)
   - Share route with others
   - Offline caching of routes
   - Pull-to-refresh to update data

Include proper state management, Mapbox SDK integration, and error handling.
```

---

## ✅ Acceptance Criteria

Phase 3 is complete when:

- [ ] Admin can create, edit, delete buses
- [ ] Admin can upload bus images
- [ ] Admin can track bus maintenance
- [ ] Admin can create routes with interactive map builder
- [ ] Admin can add/remove/reorder stops on routes
- [ ] Admin can create and manage stops with map picker
- [ ] Admin can assign buses to routes
- [ ] Routes are calculated with actual distances (Mapbox)
- [ ] Geospatial queries work correctly (nearby stops)
- [ ] Students can view all routes and stops
- [ ] Mobile app displays routes on Mapbox map
- [ ] All CRUD operations work correctly
- [ ] Data validation prevents invalid entries
- [ ] Tests pass with >80% coverage
- [ ] Maps display correctly in admin and mobile

---

## 🧪 Testing Checklist

### Backend Testing
- [ ] Create bus with valid data succeeds
- [ ] Create bus with duplicate registration fails
- [ ] Update bus status works
- [ ] Cannot delete bus with assignments
- [ ] Create route with stops works
- [ ] Reorder stops works correctly
- [ ] Calculate route distance works
- [ ] Nearby stops query returns correct results
- [ ] Assignment conflict detection works
- [ ] All validations work

### Admin Dashboard Testing
- [ ] Buses list loads and paginates
- [ ] Create bus form works
- [ ] Edit bus works
- [ ] Upload bus image works
- [ ] Route builder map displays
- [ ] Can select stops on map
- [ ] Can reorder stops
- [ ] Distance calculation works
- [ ] Stop map picker works
- [ ] Assignment form validates conflicts

### Mobile App Testing
- [ ] Routes list loads
- [ ] Route detail shows map correctly
- [ ] Stops list loads
- [ ] Stop detail shows location
- [ ] Map displays routes and stops
- [ ] Can search routes
- [ ] Can favorite routes
- [ ] Maps work offline (cached tiles)

---

## 📊 Success Metrics

- **API Response Time:** <200ms
- **Map Load Time:** <2 seconds
- **Route Calculation Time:** <3 seconds
- **Geospatial Query Time:** <100ms
- **Test Coverage:** >80%
- **Data Accuracy:** 100% (coordinates, distances)

---

## 🔄 Next Phase

After completing Phase 3, proceed to:
➡️ **Phase 4: Real-Time GPS Tracking**

---

## 📝 Notes

- Use Mapbox free tier (50,000 map loads/month)
- Cache map tiles for offline use in mobile apps
- Optimize PostGIS queries with spatial indexes
- Keep route colors consistent across platforms
- Test with real GPS coordinates
- Consider bus capacity when assigning
- Document Mapbox API usage for team

---

**Phase Owner:** Backend Lead + Frontend Lead  
**Created:** December 26, 2025  
**Last Updated:** December 26, 2025

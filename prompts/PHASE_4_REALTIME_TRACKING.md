# Phase 4: Real-Time GPS Tracking & Trip Management
## University Bus Tracker System

**Duration:** 2-3 weeks  
**Status:** 🟡 Not Started  
**Priority:** 🔴 Critical  
**Depends On:** Phase 3 ✅

---

## 🎯 Goal

Implement real-time GPS tracking system with WebSocket communication. Allow drivers to start/end trips, send location updates, and enable students to track buses in real-time on the map.

---

## 📋 Key Features / Tasks

### 1. Database Schema - Trips & GPS Tracking
- [ ] Create Trips model (bus_id, route_id, driver_id, start_time, end_time, status)
- [ ] Create GPSLocations model with TimescaleDB hypertable (trip_id, latitude, longitude, speed, bearing, timestamp)
- [ ] Add indexes for efficient querying
- [ ] Enable TimescaleDB extension
- [ ] Run migrations

### 2. Backend - WebSocket Server Setup
- [ ] Set up Socket.io server
- [ ] Configure CORS for WebSocket connections
- [ ] Implement JWT authentication for WebSocket
- [ ] Create connection/disconnection handlers
- [ ] Set up Redis adapter for horizontal scaling
- [ ] Create room management (bus rooms, route rooms)

### 3. Backend - Trip Management API
- [ ] Start trip (POST /trips/start) - Driver only
- [ ] End trip (POST /trips/:id/end) - Driver only
- [ ] Get active trips (GET /trips/active)
- [ ] Get trip details (GET /trips/:id)
- [ ] Get trip history (GET /trips/history)
- [ ] Update trip location (POST /trips/:id/location) - Driver only
- [ ] Report delay (POST /trips/:id/delay) - Driver only
- [ ] Get trip statistics (GET /trips/:id/stats)

### 4. Backend - GPS Location Tracking
- [ ] Store GPS location in TimescaleDB
- [ ] Cache current location in Redis (for fast retrieval)
- [ ] Validate GPS coordinates
- [ ] Calculate speed and bearing
- [ ] Detect if bus is moving or stopped
- [ ] Clean up old GPS data (retention policy)

### 5. Backend - WebSocket Events
- [ ] Implement 'subscribe_bus' event (student subscribes to bus updates)
- [ ] Implement 'subscribe_route' event (subscribe to all buses on route)
- [ ] Implement 'unsubscribe' event
- [ ] Implement 'bus_location_update' broadcast
- [ ] Implement 'trip_started' broadcast
- [ ] Implement 'trip_ended' broadcast
- [ ] Implement 'bus_status_update' broadcast
- [ ] Throttle location updates (max 1 per 5 seconds)

### 6. Backend - ETA Calculation
- [ ] Calculate ETA to next stop
- [ ] Calculate ETA to specific stop
- [ ] Use current speed and distance
- [ ] Consider traffic (using Mapbox Traffic API)
- [ ] Update ETA in real-time

### 7. Driver App - Trip Management
- [ ] Driver Dashboard Screen (show assigned trips)
- [ ] Start Trip Screen (confirm route and bus)
- [ ] Active Trip Screen (show map, current location, controls)
- [ ] End Trip Screen (confirm and add notes)
- [ ] Trip History Screen

### 8. Driver App - GPS Location Tracking
- [ ] Request location permissions
- [ ] Start background location tracking
- [ ] Send location updates every 5-10 seconds
- [ ] Handle location errors gracefully
- [ ] Stop tracking when trip ends
- [ ] Show current speed and accuracy
- [ ] Display location on map

### 9. Driver App - Trip Controls
- [ ] Start trip button
- [ ] Pause trip button (for breaks)
- [ ] Resume trip button
- [ ] End trip button (with confirmation)
- [ ] Report delay button (with reason input)
- [ ] Emergency alert button
- [ ] Show trip duration and distance traveled

### 10. Student App - Live Tracking
- [ ] Live Tracking Screen (map with all active buses)
- [ ] Subscribe to bus location updates via WebSocket
- [ ] Display buses as moving markers on map
- [ ] Show bus direction (bearing)
- [ ] Show bus speed
- [ ] Show ETA to stops
- [ ] Filter by route
- [ ] Follow selected bus

### 11. Student App - Bus Arrival Alerts
- [ ] Calculate when bus is approaching student's stop
- [ ] Send push notification when bus is 5 mins away
- [ ] Send push notification when bus is 1 min away
- [ ] Allow students to set their preferred stop
- [ ] Toggle notifications on/off

### 12. Admin Dashboard - Live Monitoring
- [ ] Live Map Dashboard (show all active buses)
- [ ] Real-time location updates via WebSocket
- [ ] Filter by route
- [ ] Click bus to see details
- [ ] Show trip status (on-time, delayed)
- [ ] Show bus speed and last update time
- [ ] Alert for stopped buses (>10 minutes)

### 13. Performance Optimization
- [ ] Use Redis for caching current locations
- [ ] Use TimescaleDB for GPS history
- [ ] Implement connection pooling for WebSocket
- [ ] Compress WebSocket messages
- [ ] Batch location updates
- [ ] Implement backpressure handling

### 14. Testing
- [ ] Unit tests for trip service
- [ ] Integration tests for trip API
- [ ] WebSocket connection tests
- [ ] GPS location validation tests
- [ ] ETA calculation tests
- [ ] Load testing (simulate 50 active buses)
- [ ] Test offline handling in driver app
- [ ] Test reconnection logic

---

## 📦 Deliverables

### Backend Deliverables
- ✅ Trip management API (8 endpoints)
- ✅ WebSocket server with Socket.io
- ✅ Real-time location broadcasting
- ✅ GPS location storage (TimescaleDB)
- ✅ Redis caching for current locations
- ✅ ETA calculation system
- ✅ Database migrations

### Driver App Deliverables
- ✅ Trip management screens
- ✅ Background GPS tracking
- ✅ Location updates via WebSocket
- ✅ Active trip monitoring
- ✅ Trip controls (start, pause, end)

### Student App Deliverables
- ✅ Live tracking map
- ✅ Real-time bus location updates
- ✅ WebSocket subscription
- ✅ ETA display
- ✅ Bus arrival notifications

### Admin Dashboard Deliverables
- ✅ Live monitoring dashboard
- ✅ Real-time map with all buses
- ✅ Trip status monitoring
- ✅ Alerts for issues

### Documentation Deliverables
- ✅ WebSocket API documentation
- ✅ GPS tracking guide
- ✅ ETA calculation algorithm
- ✅ Performance optimization guide

---

## 🔧 Technical Implementation

### Database Schema (Prisma)

```prisma
model Trip {
  id                 String    @id @default(uuid())
  bus_id             String
  route_id           String
  driver_id          String
  assignment_id      String?
  start_time         DateTime
  end_time           DateTime?
  status             String    @default("scheduled") // scheduled, in_progress, completed, cancelled
  start_odometer     Int?
  end_odometer       Int?
  distance_traveled  Decimal?
  total_passengers   Int       @default(0)
  delay_minutes      Int       @default(0)
  delay_reason       String?
  notes              String?
  created_at         DateTime  @default(now())
  updated_at         DateTime  @updatedAt

  bus            Bus            @relation(fields: [bus_id], references: [id])
  route          Route          @relation(fields: [route_id], references: [id])
  driver         Driver         @relation(fields: [driver_id], references: [id])
  gps_locations  GPSLocation[]
}

model GPSLocation {
  id         String   @id @default(uuid())
  trip_id    String
  bus_id     String
  latitude   Decimal
  longitude  Decimal
  location   Unsupported("geography(Point, 4326)")?
  speed      Decimal? // km/h
  bearing    Decimal? // 0-360 degrees
  accuracy   Decimal? // meters
  altitude   Decimal? // meters
  timestamp  DateTime
  created_at DateTime @default(now())

  trip Trip @relation(fields: [trip_id], references: [id], onDelete: Cascade)
  bus  Bus  @relation(fields: [bus_id], references: [id])

  @@index([trip_id, timestamp(sort: Desc)])
  @@index([bus_id, timestamp(sort: Desc)])
}
```

### WebSocket Event Structure

```typescript
// Client to Server Events
interface ClientToServerEvents {
  subscribe_bus: (data: { bus_id: string }) => void;
  subscribe_route: (data: { route_id: string }) => void;
  subscribe_stop: (data: { stop_id: string }) => void;
  unsubscribe: (data: { type: string; id: string }) => void;
}

// Server to Client Events
interface ServerToClientEvents {
  bus_location_update: (data: BusLocationUpdate) => void;
  bus_status_update: (data: BusStatusUpdate) => void;
  trip_started: (data: TripStarted) => void;
  trip_ended: (data: TripEnded) => void;
  bus_arrival_alert: (data: BusArrivalAlert) => void;
  notification: (data: Notification) => void;
}

interface BusLocationUpdate {
  bus_id: string;
  trip_id: string;
  latitude: number;
  longitude: number;
  speed: number;
  bearing: number;
  timestamp: string;
}
```

### Redis Cache Structure

```typescript
// Current bus locations cache
interface BusLocationCache {
  bus_id: string;
  trip_id: string;
  latitude: number;
  longitude: number;
  speed: number;
  bearing: number;
  timestamp: number;
  route_id: string;
  driver_id: string;
}

// Cache key: bus:location:{bus_id}
// TTL: 60 seconds
await redis.setex(
  `bus:location:${bus_id}`,
  60,
  JSON.stringify(locationData)
);
```

---

## 🚀 Execution Prompts

### Prompt 1: Create Trip Management Backend
```
Implement trip management system for drivers:

1. Database Models (Prisma):
   - Trip model with all fields
   - GPSLocation model with TimescaleDB hypertable
   - Enable TimescaleDB: SELECT create_hypertable('gps_locations', 'timestamp')
   - Create indexes on trip_id, bus_id, timestamp

2. API Endpoints:
   - POST /api/v1/trips/start - Start new trip (driver only)
     * Validate driver has assignment for today
     * Check no active trip exists for bus
     * Create trip with status 'in_progress'
     * Return trip_id
   
   - POST /api/v1/trips/:id/end - End trip (driver only)
     * Validate trip belongs to driver
     * Set end_time, calculate distance_traveled
     * Update status to 'completed'
   
   - POST /api/v1/trips/:id/location - Update location (driver only)
     * Validate trip is active
     * Store in GPSLocation table
     * Cache in Redis with 60s TTL
     * Broadcast via WebSocket
   
   - POST /api/v1/trips/:id/delay - Report delay (driver only)
     * Update delay_minutes and delay_reason
     * Broadcast delay notification
   
   - GET /api/v1/trips/active - Get all active trips
   - GET /api/v1/trips/:id - Get trip details
   - GET /api/v1/trips/history - Get trip history (paginated)

3. Business Logic:
   - Calculate distance using GPS coordinates
   - Calculate average speed
   - Detect if bus is stopped (speed < 5 km/h for >5 mins)
   - Auto-end trip if no location updates for 2 hours

4. Validation:
   - Driver can only manage their own trips
   - Cannot start trip without valid assignment
   - Cannot have multiple active trips for same bus
   - GPS coordinates must be valid
   - Trip must be in_progress to update location

Include TypeScript types, validation, error handling.
```

### Prompt 2: Set Up WebSocket Server with Socket.io
```
Implement WebSocket server for real-time tracking:

1. Socket.io Server Setup:
   - Initialize Socket.io with Express server
   - Configure CORS for WebSocket
   - Enable Redis adapter for scaling (ioredis + socket.io-redis)
   - Set up connection authentication with JWT

2. Authentication Middleware:
   - Verify JWT token from query params or handshake auth
   - Attach user info to socket
   - Disconnect if authentication fails

3. Connection Handler:
   - Log connection (user_id, socket_id, timestamp)
   - Join user to personal room
   - Send connection success event
   - Handle disconnection (cleanup rooms, log)

4. Event Handlers:
   - 'subscribe_bus': Join bus room (bus:{bus_id})
   - 'subscribe_route': Join route room (route:{route_id})
   - 'subscribe_stop': Join stop room (stop:{stop_id})
   - 'unsubscribe': Leave specific room

5. Broadcasting Events:
   - bus_location_update: Broadcast to bus and route rooms
   - trip_started: Broadcast to route subscribers
   - trip_ended: Broadcast to route subscribers
   - bus_arrival_alert: Send to specific users

6. Performance:
   - Throttle location updates (max 1 per 5 seconds per bus)
   - Compress messages
   - Use rooms for efficient broadcasting
   - Clean up disconnected sockets

7. Error Handling:
   - Handle connection errors
   - Retry logic for Redis
   - Log all errors

Example usage:
```typescript
// Broadcast location update
io.to(`bus:${bus_id}`).to(`route:${route_id}`).emit('bus_location_update', {
  bus_id,
  latitude,
  longitude,
  speed,
  bearing,
  timestamp
});
```

Include proper TypeScript types, authentication, and error handling.
```

### Prompt 3: Implement GPS Tracking in Driver App
```
Create GPS tracking system for driver mobile app:

1. Location Permissions:
   - Request location permissions on app start
   - Handle permission denied gracefully
   - Request background location permission
   - Show explanation before requesting

2. Background Location Service:
   - Use react-native-background-geolocation
   - Configure for high accuracy (desiredAccuracy: 10 meters)
   - Set location update interval: 5-10 seconds
   - Enable background tracking
   - Battery optimization settings

3. Location Updates:
   - Get current position (latitude, longitude, speed, bearing, accuracy)
   - Validate location accuracy (< 50 meters)
   - Send to backend API: POST /trips/:id/location
   - Handle API errors (queue updates if offline)
   - Show location update status to driver

4. Active Trip Screen:
   - Display Mapbox map with current location
   - Show route polyline
   - Show stops on route
   - Update map camera to follow bus
   - Display current speed
   - Display location accuracy
   - Show trip duration
   - Show distance traveled

5. Trip Controls:
   - Start Trip button: 
     * Confirm route and bus
     * Start GPS tracking
     * Call API to start trip
     * Navigate to Active Trip screen
   
   - End Trip button:
     * Stop GPS tracking
     * Show confirmation dialog
     * Call API to end trip
     * Navigate back to dashboard

6. Offline Handling:
   - Queue location updates if no internet
   - Sync when connection restored
   - Show offline indicator

7. Error Handling:
   - Handle GPS errors
   - Handle API errors
   - Show user-friendly error messages
   - Log errors for debugging

Include proper state management, error handling, and battery optimization.
```

### Prompt 4: Create Live Tracking UI for Student App
```
Create live tracking screen for student app:

1. LiveTrackingScreen.tsx:
   - Full-screen Mapbox map
   - Show user's current location (if permission granted)
   - Show all active buses as markers
   - Show route polylines
   - Show stops as markers
   - Filter selector (all routes, specific route)
   - Selected bus detail panel (bottom sheet)

2. WebSocket Integration:
   - Connect to WebSocket on screen mount
   - Subscribe to all active buses (or specific route)
   - Listen for 'bus_location_update' events
   - Update bus marker positions in real-time
   - Handle connection errors and reconnection

3. Bus Markers:
   - Custom marker icon (bus icon)
   - Rotate marker based on bearing
   - Show different colors based on route
   - Animate marker movement (smooth transitions)
   - Click marker to show bus details
   - Show speed and ETA

4. Bus Detail Panel:
   - Bus registration number
   - Route name
   - Current speed
   - ETA to user's stop
   - Last updated time
   - "Track this bus" button
   - "Get notifications" button

5. Map Features:
   - Zoom to user location button
   - Zoom to selected bus button
   - Show/hide stops toggle
   - Show/hide routes toggle
   - Refresh button (force update)

6. Notifications Setup:
   - "Notify Me" button
   - Select preferred stop
   - Set notification preferences
   - Register for push notifications

7. Performance:
   - Use React.memo for bus markers
   - Debounce map movements
   - Lazy load map features
   - Cache map tiles for offline

Include proper WebSocket handling, map optimization, and error handling.
```

### Prompt 5: Create ETA Calculation System
```
Implement ETA calculation for bus arrivals:

1. ETA Calculation Service:
   - Calculate distance from bus to stop (using current location)
   - Get current bus speed
   - Estimate time = distance / average_speed
   - Add buffer time for stops (2 minutes per stop)
   - Consider traffic data (Mapbox Traffic API)

2. API Endpoint:
   - GET /api/v1/buses/:busId/eta/:stopId - Get ETA to specific stop
   - Response: { eta_minutes, eta_timestamp, distance_km, confidence }

3. Real-time Updates:
   - Recalculate ETA on each location update
   - Broadcast ETA updates via WebSocket
   - Update Redis cache

4. Arrival Detection:
   - Detect when bus is within 500m of stop
   - Trigger "approaching" event
   - Send push notification to subscribed users
   - Detect when bus is within 100m ("arriving")
   - Detect when bus passes stop

5. Notification Triggers:
   - 10 minutes away: "Your bus will arrive in 10 minutes"
   - 5 minutes away: "Your bus is 5 minutes away"
   - Approaching (500m): "Your bus is approaching"
   - Arriving (100m): "Your bus is arriving now"

6. Edge Cases:
   - Handle stopped buses (traffic, passengers)
   - Handle route deviations
   - Handle missing GPS data
   - Handle low accuracy GPS

7. Caching:
   - Cache ETA for 30 seconds
   - Invalidate on location update

Example calculation:
```typescript
const calculateETA = async (busLocation, stopLocation, currentSpeed) => {
  const distance = calculateDistance(busLocation, stopLocation);
  const averageSpeed = currentSpeed || 30; // km/h default
  const baseTime = (distance / averageSpeed) * 60; // minutes
  const bufferTime = 2; // 2 minutes buffer
  const eta = baseTime + bufferTime;
  
  return {
    eta_minutes: Math.round(eta),
    distance_km: distance,
    confidence: calculateConfidence(currentSpeed, accuracy)
  };
};
```

Include distance calculation, traffic consideration, and caching.
```

### Prompt 6: Create Admin Live Monitoring Dashboard
```
Create real-time monitoring dashboard for admin:

1. LiveMonitoringPage.tsx:
   - Full-screen Mapbox map
   - Show all active buses
   - Show all routes
   - Show all stops
   - Stats panel (top): Active buses, Total trips today, Delayed buses
   - Side panel (right): List of active buses

2. WebSocket Integration:
   - Connect on page load
   - Subscribe to all buses
   - Listen for location updates
   - Listen for trip events
   - Auto-reconnect on disconnect

3. Bus Markers:
   - Show bus icon with route color
   - Rotate based on bearing
   - Show speed label
   - Click to show details popup

4. Bus Details Popup:
   - Bus registration
   - Driver name
   - Route name
   - Current speed
   - Status (on-time, delayed, stopped)
   - Last updated time
   - Trip duration
   - Passengers count (from last scan)
   - "View Trip" button

5. Bus List Panel:
   - List all active buses
   - Show: Registration, Route, Status, Speed, Last Update
   - Status indicators: 🟢 Moving, 🟡 Delayed, 🔴 Stopped
   - Search and filter
   - Click to zoom to bus on map

6. Alerts:
   - Show alert banner for stopped buses (>10 mins)
   - Show alert for delayed buses
   - Show alert for buses with no updates (>5 mins)
   - Play sound for critical alerts
   - Notification count badge

7. Map Controls:
   - Zoom to fit all buses
   - Toggle layers (buses, routes, stops)
   - Time filter (show buses active in last X minutes)
   - Refresh all data button

8. Performance:
   - Use React.memo for bus markers
   - Debounce updates
   - Limit visible buses (zoom level based)
   - Use map clustering for many buses

Include WebSocket integration, real-time updates, and performance optimization.
```

### Prompt 7: Implement Push Notifications for Bus Arrivals
```
Create push notification system for bus arrival alerts:

1. Firebase Cloud Messaging Setup:
   - Configure FCM in backend
   - Store device tokens in database
   - Handle token refresh

2. Notification Service:
   - sendBusArrivalNotification(userId, busId, stopId, etaMinutes)
   - sendDelayNotification(userId, busId, delayMinutes, reason)
   - sendTripStartedNotification(subscribedUsers, busId, routeName)

3. Subscription Management:
   - API: POST /api/v1/notifications/subscribe
   - Body: { bus_id, stop_id, notification_types: ['5_min', '1_min', 'arriving'] }
   - Store subscriptions in database
   - Allow multiple subscriptions per user

4. Notification Triggers:
   - Check ETA on each location update
   - Trigger notification when conditions met
   - Prevent duplicate notifications (track sent notifications)
   - Clear old notification records (>24 hours)

5. Student App Integration:
   - Request FCM permission
   - Get device token
   - Send token to backend
   - Handle incoming notifications
   - Navigate to tracking screen on tap

6. Notification Payload:
   ```json
   {
     "notification": {
       "title": "Bus Arriving Soon",
       "body": "Bus R-01 will arrive at Main Gate in 5 minutes"
     },
     "data": {
       "type": "bus_arrival",
       "bus_id": "uuid",
       "stop_id": "uuid",
       "eta_minutes": "5"
     }
   }
   ```

7. User Preferences:
   - Enable/disable notifications
   - Select preferred stops
   - Select notification timing (5 min, 10 min, etc.)
   - Quiet hours (no notifications at night)

Include FCM setup, notification scheduling, and user preferences.
```

---

## ✅ Acceptance Criteria

Phase 4 is complete when:

- [ ] Driver can start and end trips
- [ ] Driver app sends GPS updates every 5-10 seconds
- [ ] GPS locations stored in TimescaleDB
- [ ] Current locations cached in Redis
- [ ] WebSocket server handles 50+ concurrent connections
- [ ] Students can see buses moving in real-time on map
- [ ] Bus markers update smoothly without flickering
- [ ] ETA calculation works accurately
- [ ] Push notifications sent at correct times
- [ ] Admin can monitor all active buses in real-time
- [ ] System handles network interruptions gracefully
- [ ] Offline GPS updates queue and sync when online
- [ ] All tests pass with >80% coverage
- [ ] Load testing shows system can handle 50 active buses

---

## 🧪 Testing Checklist

### Backend Testing
- [ ] Trip start/end works
- [ ] GPS location storage works
- [ ] WebSocket connections authenticate
- [ ] Location updates broadcast to subscribers
- [ ] ETA calculation is accurate
- [ ] Redis caching works
- [ ] TimescaleDB queries are fast (<100ms)
- [ ] Handle 50 concurrent location updates
- [ ] Notification triggers work correctly

### Driver App Testing
- [ ] Location permissions requested
- [ ] Background tracking works
- [ ] Location updates sent every 5-10 seconds
- [ ] Trip controls work (start, pause, end)
- [ ] Map displays current location
- [ ] Offline queue works
- [ ] Battery usage is acceptable (<10% per hour)

### Student App Testing
- [ ] Live map loads
- [ ] WebSocket connects successfully
- [ ] Bus markers update in real-time
- [ ] Can subscribe to specific bus/route
- [ ] ETA displays correctly
- [ ] Push notifications received
- [ ] Tapping notification opens tracking screen
- [ ] Map performance is smooth (60 FPS)

### Admin Dashboard Testing
- [ ] Live monitoring page loads
- [ ] All active buses visible
- [ ] Real-time updates work
- [ ] Alerts show for issues
- [ ] Can filter and search buses
- [ ] Map performance is smooth

---

## 📊 Success Metrics

- **Location Update Frequency:** 5-10 seconds
- **WebSocket Latency:** <100ms
- **GPS Accuracy:** <20 meters
- **ETA Accuracy:** ±3 minutes
- **Notification Delivery:** >95%
- **Map FPS:** 60 FPS
- **API Response Time:** <200ms
- **Battery Usage:** <10% per hour (driver app)
- **Concurrent Buses:** 50+
- **Test Coverage:** >80%

---

## 🔄 Next Phase

After completing Phase 4, proceed to:
➡️ **Phase 5: Bus Pass & Payment System**

---

## 📝 Notes

- Use TimescaleDB for efficient time-series GPS storage
- Cache current locations in Redis (TTL: 60 seconds)
- Throttle WebSocket updates to prevent flooding
- Optimize battery usage in driver app
- Test with actual GPS devices, not simulators
- Consider network quality when sending updates
- Log all GPS updates for debugging
- Monitor WebSocket connection health
- Implement reconnection logic with exponential backoff

---

**Phase Owner:** Backend Lead + Mobile Lead  
**Created:** December 26, 2025  
**Last Updated:** December 26, 2025

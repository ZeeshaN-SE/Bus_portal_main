# Phase 6: Notifications & Analytics System
## University Bus Tracker System

**Duration:** 2 weeks  
**Status:** 🟡 Not Started  
**Priority:** 🟠 High  
**Depends On:** Phase 5 ✅

---

## 🎯 Goal

Implement comprehensive notification system (push, email, in-app) and analytics dashboard with reports for administrators to track system usage, revenue, and performance metrics.

---

## 📋 Key Features / Tasks

### 1. Database Schema - Notifications & Announcements
- [ ] Create Notifications model (user_id, type, title, message, is_read, sent_at)
- [ ] Create Announcements model (title, content, target_audience, priority, is_active)
- [ ] Create AuditLogs model (user_id, action, entity_type, entity_id, old_values, new_values)
- [ ] Add indexes for efficient querying
- [ ] Run migrations

### 2. Backend - Notification System
- [ ] Create notification service
- [ ] Implement Firebase Cloud Messaging (FCM)
- [ ] Store device tokens in database
- [ ] Create notification templates
- [ ] Queue notifications (Bull with Redis)
- [ ] Schedule notifications (cron jobs)
- [ ] Track notification delivery
- [ ] Handle notification errors

### 3. Backend - Notification API
- [ ] Get notifications (GET /notifications)
- [ ] Mark as read (PATCH /notifications/:id/read)
- [ ] Mark all as read (POST /notifications/mark-all-read)
- [ ] Delete notification (DELETE /notifications/:id)
- [ ] Get notification preferences (GET /notifications/preferences)
- [ ] Update preferences (PATCH /notifications/preferences)
- [ ] Register device token (POST /notifications/device-token)
- [ ] Unregister device token (DELETE /notifications/device-token)

### 4. Backend - Email Notifications
- [ ] Set up SendGrid templates
- [ ] Verification email
- [ ] Password reset email
- [ ] Payment confirmation email
- [ ] Pass expiry reminder email
- [ ] Trip delay notification email
- [ ] Weekly summary email
- [ ] Announcement email

### 5. Backend - Announcement System
- [ ] Create announcement (POST /admin/announcements)
- [ ] Get announcements (GET /announcements)
- [ ] Update announcement (PATCH /admin/announcements/:id)
- [ ] Delete announcement (DELETE /admin/announcements/:id)
- [ ] Send announcement to target audience
- [ ] Schedule announcements

### 6. Backend - Analytics API
- [ ] Usage analytics (GET /admin/analytics/usage)
- [ ] Revenue analytics (GET /admin/analytics/revenue)
- [ ] Bus performance (GET /admin/analytics/bus-performance)
- [ ] Driver performance (GET /admin/analytics/driver-performance)
- [ ] Route analytics (GET /admin/analytics/routes)
- [ ] Student engagement (GET /admin/analytics/engagement)
- [ ] Export reports (POST /admin/analytics/export)

### 7. Backend - Audit Logging
- [ ] Log all user actions
- [ ] Log admin actions
- [ ] Log system events
- [ ] Query audit logs (GET /admin/audit-logs)
- [ ] Filter by user, action, entity
- [ ] Export audit logs

### 8. Mobile Apps - Push Notifications
- [ ] Request notification permission
- [ ] Get FCM token
- [ ] Register token with backend
- [ ] Handle incoming notifications
- [ ] Display notification badge
- [ ] Navigate to relevant screen on tap
- [ ] Handle background notifications
- [ ] Handle notification actions

### 9. Mobile Apps - In-App Notifications
- [ ] Notifications Screen (list all notifications)
- [ ] Notification item (with read/unread indicator)
- [ ] Mark as read functionality
- [ ] Delete notification
- [ ] Notification badge on tab/icon
- [ ] Pull-to-refresh
- [ ] Empty state

### 10. Mobile Apps - Notification Preferences
- [ ] Notification Settings Screen
- [ ] Toggle for each notification type
- [ ] Select notification channels (push, email)
- [ ] Quiet hours setting
- [ ] Sound/vibration preferences
- [ ] Save preferences

### 11. Student App - Announcements
- [ ] Announcements Screen
- [ ] Announcement detail view
- [ ] Filter by date/priority
- [ ] Mark as read
- [ ] Share announcement

### 12. Admin Dashboard - Analytics Overview
- [ ] Dashboard with key metrics
- [ ] Total users (students, drivers)
- [ ] Active buses count
- [ ] Total trips today
- [ ] Revenue today/month
- [ ] Active passes count
- [ ] Charts (line, bar, pie)
- [ ] Date range selector
- [ ] Export data button

### 13. Admin Dashboard - Usage Analytics
- [ ] Daily/weekly/monthly active users
- [ ] Most used routes
- [ ] Peak usage hours
- [ ] Bus utilization rate
- [ ] Average trip duration
- [ ] Passenger count trends
- [ ] Interactive charts

### 14. Admin Dashboard - Revenue Analytics
- [ ] Revenue over time (chart)
- [ ] Revenue by pass type
- [ ] Revenue by payment method
- [ ] Average transaction value
- [ ] Conversion rate
- [ ] Refund statistics
- [ ] Export revenue report

### 15. Admin Dashboard - Performance Reports
- [ ] Bus performance table
- [ ] Driver performance table
- [ ] Route efficiency
- [ ] On-time percentage
- [ ] Delay analysis
- [ ] Maintenance cost tracking
- [ ] Generate PDF reports

### 16. Admin Dashboard - Announcement Management
- [ ] Create announcement form
- [ ] Announcements list
- [ ] Edit announcement
- [ ] Delete announcement
- [ ] Schedule announcement
- [ ] Target audience selector
- [ ] Preview before sending

### 17. Testing
- [ ] Unit tests for notification service
- [ ] Test FCM integration
- [ ] Test email delivery
- [ ] Test notification scheduling
- [ ] Test analytics calculations
- [ ] Test report generation
- [ ] Load test notification system

---

## 📦 Deliverables

### Backend Deliverables
- ✅ Notification system with FCM
- ✅ Email notification service
- ✅ Notification API (8 endpoints)
- ✅ Announcement system
- ✅ Analytics API (7 endpoints)
- ✅ Audit logging system
- ✅ Report generation
- ✅ Database migrations

### Mobile App Deliverables
- ✅ Push notification handling
- ✅ In-app notifications screen
- ✅ Notification preferences
- ✅ Announcements screen
- ✅ Notification badges

### Admin Dashboard Deliverables
- ✅ Analytics dashboard
- ✅ Usage analytics
- ✅ Revenue analytics
- ✅ Performance reports
- ✅ Announcement management
- ✅ Report export (CSV, PDF)

### Documentation Deliverables
- ✅ Notification types documentation
- ✅ Analytics metrics guide
- ✅ Report generation guide

---

## 🔧 Technical Implementation

### Notification Types

```typescript
enum NotificationType {
  BUS_ARRIVAL = 'bus_arrival',
  PASS_EXPIRY = 'pass_expiry',
  PAYMENT_SUCCESS = 'payment',
  TRIP_DELAY = 'delay',
  ANNOUNCEMENT = 'announcement',
  SYSTEM = 'system',
}

interface NotificationPayload {
  type: NotificationType;
  title: string;
  message: string;
  data?: Record<string, any>;
  priority?: 'low' | 'normal' | 'high' | 'urgent';
  scheduled_at?: Date;
}
```

### FCM Integration

```typescript
import admin from 'firebase-admin';

class NotificationService {
  async sendPushNotification(
    userIds: string[],
    notification: NotificationPayload
  ) {
    // Get device tokens for users
    const tokens = await this.getDeviceTokens(userIds);
    
    if (tokens.length === 0) return;
    
    // Send multicast message
    const message = {
      notification: {
        title: notification.title,
        body: notification.message,
      },
      data: notification.data || {},
      tokens: tokens,
    };
    
    const response = await admin.messaging().sendMulticast(message);
    
    // Handle failures
    if (response.failureCount > 0) {
      await this.handleFailedTokens(response, tokens);
    }
    
    return response;
  }
  
  async scheduleNotification(
    userIds: string[],
    notification: NotificationPayload,
    scheduledAt: Date
  ) {
    // Add to queue with delay
    await notificationQueue.add(
      'send-notification',
      { userIds, notification },
      { delay: scheduledAt.getTime() - Date.now() }
    );
  }
}
```

### Analytics Calculations

```typescript
// Usage Analytics
const getUsageAnalytics = async (startDate: Date, endDate: Date) => {
  return {
    total_trips: await prisma.trip.count({
      where: { 
        start_time: { gte: startDate, lte: endDate },
        status: 'completed'
      }
    }),
    total_passengers: await prisma.tripScan.count({
      where: { 
        scan_time: { gte: startDate, lte: endDate }
      }
    }),
    active_students: await prisma.student.count({
      where: {
        bus_passes: {
          some: {
            status: 'active',
            purchase_date: { gte: startDate, lte: endDate }
          }
        }
      }
    }),
    peak_hours: await getPeakHours(startDate, endDate),
    most_used_routes: await getMostUsedRoutes(startDate, endDate),
  };
};

// Revenue Analytics
const getRevenueAnalytics = async (startDate: Date, endDate: Date) => {
  const payments = await prisma.payment.groupBy({
    by: ['payment_date'],
    where: {
      payment_date: { gte: startDate, lte: endDate },
      status: 'completed'
    },
    _sum: { amount: true },
    _count: true,
  });
  
  const byPassType = await prisma.payment.groupBy({
    by: ['bus_pass.pass_type'],
    where: {
      payment_date: { gte: startDate, lte: endDate },
      status: 'completed'
    },
    _sum: { amount: true },
  });
  
  return {
    total_revenue: payments.reduce((sum, p) => sum + p._sum.amount, 0),
    daily_revenue: payments,
    revenue_by_pass_type: byPassType,
    average_transaction: calculateAverage(payments),
  };
};
```

---

## 🚀 Execution Prompts

### Prompt 1: Implement Notification System with FCM
```
Create comprehensive notification system:

1. Firebase Setup:
   - Initialize Firebase Admin SDK
   - Configure FCM credentials
   - Set up service account

2. Notification Service:
   ```typescript
   class NotificationService {
     sendPushNotification(userIds, title, message, data)
     sendEmailNotification(userIds, template, data)
     scheduleNotification(userIds, notification, scheduledAt)
     sendBulkNotifications(notifications)
     trackDelivery(notificationId)
   }
   ```

3. Notification Types:
   - Bus arrival (5 min, 1 min, arriving)
   - Pass expiry (3 days, 1 day, expired)
   - Payment success/failure
   - Trip delay
   - System announcements
   - Account-related (verification, password reset)

4. Queue System:
   - Use Bull queue with Redis
   - Priority queue (urgent, high, normal, low)
   - Retry failed notifications (3 attempts)
   - Schedule notifications for future delivery
   - Rate limiting (prevent spam)

5. Device Token Management:
   - Store device tokens in database
   - Update tokens on app start
   - Remove invalid tokens
   - Handle token refresh

6. API Endpoints:
   - POST /api/v1/notifications/device-token - Register device
   - DELETE /api/v1/notifications/device-token - Unregister
   - GET /api/v1/notifications - Get user notifications
   - PATCH /api/v1/notifications/:id/read - Mark as read
   - POST /api/v1/notifications/mark-all-read - Mark all read
   - GET /api/v1/notifications/preferences - Get preferences
   - PATCH /api/v1/notifications/preferences - Update preferences

7. Notification Preferences:
   ```typescript
   {
     bus_arrival: { enabled: true, push: true, email: false },
     pass_expiry: { enabled: true, push: true, email: true },
     payment: { enabled: true, push: true, email: true },
     announcements: { enabled: true, push: true, email: false },
     quiet_hours: { enabled: true, start: '22:00', end: '07:00' }
   }
   ```

Include FCM integration, queue system, and preferences management.
```

### Prompt 2: Implement Email Notification Service
```
Create email notification service with SendGrid:

1. SendGrid Setup:
   - Configure API key
   - Create email templates
   - Set up sender verification

2. Email Templates:
   - Email verification
   - Password reset
   - Payment confirmation (with receipt)
   - Pass activated
   - Pass expiry reminder
   - Trip delay notification
   - Weekly summary
   - Announcement

3. Email Service:
   ```typescript
   class EmailService {
     sendVerificationEmail(email, name, token)
     sendPasswordResetEmail(email, name, token)
     sendPaymentConfirmation(email, payment, pass)
     sendPassExpiryReminder(email, pass, daysRemaining)
     sendWeeklySummary(email, stats)
   }
   ```

4. Template Variables:
   - User name
   - Dynamic content
   - Action buttons
   - Branding (logo, colors)

5. Features:
   - HTML templates with inline CSS
   - Plain text fallback
   - Unsubscribe link
   - Tracking (opens, clicks)
   - Queue emails for async sending
   - Retry failed emails

6. Error Handling:
   - Log all email attempts
   - Handle SendGrid errors
   - Fallback to alternative service
   - Alert on high failure rate

Include email templates, queue system, and error handling.
```

### Prompt 3: Create Push Notifications in Mobile Apps
```
Implement push notifications in React Native apps:

1. FCM Setup:
   - Install @react-native-firebase/messaging
   - Configure iOS (APNs) and Android
   - Request notification permissions

2. Token Management:
   - Get FCM token on app start
   - Send token to backend
   - Update token on refresh
   - Handle token errors

3. Notification Handling:
   - Foreground notifications (show in-app)
   - Background notifications (system tray)
   - Notification opened (navigate to screen)
   - Notification actions (like, dismiss)

4. Implementation:
   ```typescript
   // Request permission
   const requestPermission = async () => {
     const authStatus = await messaging().requestPermission();
     const enabled = authStatus === messaging.AuthorizationStatus.AUTHORIZED;
     
     if (enabled) {
       const token = await messaging().getToken();
       await registerDeviceToken(token);
     }
   };
   
   // Handle foreground notifications
   messaging().onMessage(async remoteMessage => {
     showInAppNotification(remoteMessage);
   });
   
   // Handle background notification tap
   messaging().onNotificationOpenedApp(remoteMessage => {
     navigateToScreen(remoteMessage.data);
   });
   
   // Handle notification when app opened from quit state
   messaging().getInitialNotification().then(remoteMessage => {
     if (remoteMessage) {
       navigateToScreen(remoteMessage.data);
     }
   });
   ```

5. In-App Notification Display:
   - Show toast/banner at top
   - Display for 3-5 seconds
   - Tap to navigate
   - Swipe to dismiss

6. Notification Badge:
   - Show unread count on tab icon
   - Update on new notification
   - Clear on mark as read

7. Notification Preferences Screen:
   - Toggle for each notification type
   - Select channels (push, email)
   - Quiet hours setting
   - Test notification button

Include permission handling, token management, and preferences UI.
```

### Prompt 4: Create Analytics Dashboard
```
Create comprehensive analytics dashboard for admin:

1. DashboardPage.tsx:
   - Grid layout with cards
   - Key metrics at top:
     * Total Students
     * Active Buses
     * Total Trips Today
     * Revenue Today
     * Active Passes
   - Charts below:
     * Revenue over time (line chart)
     * Trips per route (bar chart)
     * Pass distribution (pie chart)
     * Usage by hour (area chart)
   - Date range selector
   - Refresh button
   - Export button

2. UsageAnalyticsPage.tsx:
   - Active users trend (line chart)
   - Most used routes (bar chart)
   - Peak usage hours (heatmap)
   - Bus utilization (gauge chart)
   - Trip completion rate
   - Average trip duration
   - Filter by date range
   - Export report

3. RevenueAnalyticsPage.tsx:
   - Revenue trend (line chart)
   - Revenue by pass type (pie chart)
   - Revenue by payment method (bar chart)
   - Top spenders table
   - Conversion rate
   - Refund statistics
   - Key metrics:
     * Total Revenue
     * Average Transaction Value
     * Payment Success Rate
   - Export revenue report

4. PerformanceReportsPage.tsx:
   - Bus performance table:
     * Registration
     * Total Trips
     * Distance Traveled
     * Passengers Count
     * Utilization %
     * On-Time %
   - Driver performance table:
     * Name
     * Total Trips
     * Completion Rate
     * On-Time %
     * Average Delay
   - Route efficiency table
   - Sort and filter
   - Export to PDF/CSV

5. Charts:
   - Use Recharts library
   - Responsive design
   - Interactive tooltips
   - Zoom and pan
   - Legend
   - Custom colors

6. Data Fetching:
   - Fetch analytics from API
   - Cache with React Query
   - Refresh every 5 minutes
   - Loading skeletons
   - Error states

7. Export Functionality:
   - Export to CSV (using xlsx library)
   - Export to PDF (using jsPDF)
   - Include filters applied
   - Add timestamp and user info

Include proper data visualization, caching, and export functionality.
```

### Prompt 5: Create Announcement Management System
```
Implement announcement system for admin and students:

1. Backend - Announcement API:
   - POST /api/v1/admin/announcements - Create
   - GET /api/v1/announcements - Get all (filtered)
   - GET /api/v1/announcements/:id - Get one
   - PATCH /api/v1/admin/announcements/:id - Update
   - DELETE /api/v1/admin/announcements/:id - Delete
   - POST /api/v1/admin/announcements/:id/send - Send to audience

2. Announcement Model:
   ```prisma
   model Announcement {
     id               String   @id
     created_by       String
     title            String
     content          Text
     target_audience  String   // all, students, drivers
     priority         String   // low, normal, high, urgent
     is_active        Boolean
     publish_date     DateTime
     expiry_date      DateTime?
     image_url        String?
     created_at       DateTime
     updated_at       DateTime
   }
   ```

3. Admin - Create Announcement Form:
   - Title input
   - Content textarea (rich text editor)
   - Target audience selector (all, students, drivers)
   - Priority selector
   - Publish date picker
   - Expiry date picker (optional)
   - Image upload
   - "Save Draft" button
   - "Publish" button
   - Preview mode

4. Admin - Announcements List:
   - Table view with columns:
     * Title
     * Target Audience
     * Priority
     * Status (active/expired)
     * Publish Date
     * Actions (edit, delete, view)
   - Filter by: status, audience, date range
   - Search by title
   - Sort by date/priority

5. Student App - Announcements Screen:
   - List of announcements (cards)
   - Each card shows:
     * Title
     * Date
     * Priority badge
     * Preview text (truncated)
   - Filter by priority
   - Mark as read
   - Pull-to-refresh
   - Unread badge

6. Announcement Detail Screen:
   - Full title
   - Content (formatted)
   - Image (if any)
   - Publish date
   - Priority badge
   - Share button
   - Mark as read automatically

7. Notification Integration:
   - Send push notification on publish
   - Send email to target audience
   - Store in notifications table
   - Link to announcement detail

Include rich text editor, image upload, and audience targeting.
```

---

## ✅ Acceptance Criteria

Phase 6 is complete when:

- [ ] Push notifications work on iOS and Android
- [ ] Email notifications sent successfully
- [ ] In-app notifications display correctly
- [ ] Notification preferences can be managed
- [ ] Announcements can be created and published
- [ ] Analytics dashboard shows all metrics
- [ ] Usage analytics display correctly
- [ ] Revenue analytics accurate
- [ ] Performance reports generated
- [ ] Reports can be exported (CSV, PDF)
- [ ] Audit logs track all actions
- [ ] Notification badges update in real-time
- [ ] All tests pass with >80% coverage

---

## 🧪 Testing Checklist

- [ ] FCM notifications received on devices
- [ ] Email notifications delivered
- [ ] Notification preferences saved
- [ ] Announcements sent to target audience
- [ ] Analytics calculations accurate
- [ ] Charts display correctly
- [ ] Export to CSV works
- [ ] Export to PDF works
- [ ] Notification queue processes messages
- [ ] Failed notifications retried

---

## 📊 Success Metrics

- **Notification Delivery Rate:** >95%
- **Email Delivery Rate:** >98%
- **Analytics Load Time:** <2 seconds
- **Report Generation Time:** <5 seconds
- **Notification Queue Processing:** <1 second per notification
- **Test Coverage:** >80%

---

## 🔄 Next Phase

After completing Phase 6, proceed to:
➡️ **Phase 7: Testing & Quality Assurance**

---

## 📝 Notes

- Test notifications on real devices
- Monitor FCM quota and limits
- Use SendGrid test mode for development
- Cache analytics data for performance
- Schedule notification cleanup (old notifications)
- Implement notification throttling
- Consider time zones for scheduled notifications
- Test email templates in different clients
- Monitor analytics accuracy
- Set up alerts for system issues

---

**Phase Owner:** Backend Lead + Frontend Lead  
**Created:** December 26, 2025  
**Last Updated:** December 26, 2025

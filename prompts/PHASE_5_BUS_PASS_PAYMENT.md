# Phase 5: Bus Pass & Payment System
## University Bus Tracker System

**Duration:** 2-3 weeks  
**Status:** 🟡 Not Started  
**Priority:** 🔴 Critical  
**Depends On:** Phase 4 ✅

---

## 🎯 Goal

Implement digital bus pass system with QR code generation, secure payment processing via KuickPay, and pass verification functionality for drivers.

---

## 📋 Key Features / Tasks

### 1. Database Schema - Passes & Payments
- [ ] Create BusPasses model (student_id, pass_type, price, status, qr_code, expiry_date)
- [ ] Create Payments model (student_id, amount, status, gateway_transaction_id, receipt_url)
- [ ] Create TripScans model (trip_id, pass_id, student_id, scan_time, is_valid)
- [ ] Add indexes for efficient querying
- [ ] Run migrations

### 2. Backend - Pass Management API
- [ ] Get pass types (GET /passes/types) - public
- [ ] Get my passes (GET /passes/my-passes) - student only
- [ ] Get active pass (GET /passes/active) - student only
- [ ] Purchase pass (POST /passes/purchase) - student only
- [ ] Verify pass (POST /passes/verify) - driver only
- [ ] Get pass details (GET /passes/:id)
- [ ] Get pass usage history (GET /passes/:id/usage)
- [ ] Admin: List all passes (GET /admin/passes)
- [ ] Admin: Cancel pass (POST /admin/passes/:id/cancel)

### 3. Backend - Payment Integration (KuickPay)
- [ ] Set up KuickPay API credentials
- [ ] Create payment intent (POST /payments/intent)
- [ ] Handle payment webhook (POST /payments/webhook)
- [ ] Get payment history (GET /payments/history)
- [ ] Get payment receipt (GET /payments/:id/receipt)
- [ ] Admin: Process refund (POST /admin/payments/:id/refund)
- [ ] Verify webhook signatures
- [ ] Handle payment failures

### 4. Backend - QR Code Generation
- [ ] Generate secure QR codes with encryption
- [ ] Include: pass_id, student_id, expiry_date, signature
- [ ] Encrypt QR data with AES-256
- [ ] Generate QR code image (using qrcode library)
- [ ] Store QR code in S3 or local storage
- [ ] Implement QR code verification

### 5. Backend - Pass Verification Logic
- [ ] Decrypt QR code data
- [ ] Verify pass is active and not expired
- [ ] Check pass hasn't been used multiple times today (if daily limit)
- [ ] Check pass belongs to student
- [ ] Record scan in TripScans table
- [ ] Return student info and pass details
- [ ] Handle offline verification (cache valid passes)

### 6. Student App - Pass Purchase Flow
- [ ] Pass Types Screen (show all available passes)
- [ ] Pass Detail Screen (show pass benefits and price)
- [ ] Purchase Confirmation Screen
- [ ] Payment Screen (redirect to KuickPay or in-app)
- [ ] Payment Success Screen
- [ ] Payment Failed Screen
- [ ] Handle payment callbacks

### 7. Student App - My Passes
- [ ] My Passes Screen (list all passes)
- [ ] Active Pass Screen (show QR code prominently)
- [ ] Pass Detail Screen (show usage history)
- [ ] QR Code Display (large, scannable)
- [ ] Pass expiry countdown
- [ ] Renew pass button
- [ ] Download receipt button

### 8. Driver App - QR Scanner
- [ ] QR Scanner Screen (camera view)
- [ ] Request camera permission
- [ ] Scan QR code
- [ ] Show verification result (valid/invalid)
- [ ] Display student information
- [ ] Display pass information
- [ ] Record scan
- [ ] Sound/vibration feedback
- [ ] Offline mode (verify from cache)

### 9. Driver App - Scan History
- [ ] Today's Scans Screen (list of all scans)
- [ ] Scan statistics (total scans, valid, invalid)
- [ ] Filter by valid/invalid
- [ ] Search by student ID or name
- [ ] Export scan report

### 10. Admin Dashboard - Pass Management
- [ ] Passes List Page (all passes with filters)
- [ ] Pass Detail Page
- [ ] Pass analytics (by type, status)
- [ ] Search and filter passes
- [ ] Cancel/refund pass
- [ ] Export passes data
- [ ] Generate pass reports

### 11. Admin Dashboard - Payment Management
- [ ] Payments List Page
- [ ] Payment Detail Page
- [ ] Payment analytics (revenue by period)
- [ ] Search and filter payments
- [ ] Process refund
- [ ] Download receipts
- [ ] Export payment data
- [ ] Revenue reports

### 12. Notifications
- [ ] Payment confirmation email
- [ ] Pass activation email
- [ ] Pass expiry reminder (3 days before)
- [ ] Pass expiry notification (day of)
- [ ] Payment receipt email
- [ ] Refund confirmation email

### 13. Testing
- [ ] Unit tests for pass service
- [ ] Unit tests for payment service
- [ ] Integration tests for purchase flow
- [ ] Test KuickPay webhook handling
- [ ] Test QR code generation and verification
- [ ] Test payment failures
- [ ] Test offline pass verification
- [ ] Test concurrent scan scenarios

---

## 📦 Deliverables

### Backend Deliverables
- ✅ Pass management API (9 endpoints)
- ✅ Payment API (6 endpoints)
- ✅ KuickPay integration
- ✅ QR code generation and encryption
- ✅ Pass verification logic
- ✅ Webhook handler
- ✅ Database migrations

### Student App Deliverables
- ✅ Pass purchase flow
- ✅ Payment integration
- ✅ My passes screen with QR display
- ✅ Pass management
- ✅ Receipt download

### Driver App Deliverables
- ✅ QR code scanner
- ✅ Pass verification
- ✅ Scan history
- ✅ Offline verification

### Admin Dashboard Deliverables
- ✅ Pass management interface
- ✅ Payment management
- ✅ Analytics and reports
- ✅ Refund processing

### Documentation Deliverables
- ✅ Payment integration guide
- ✅ QR code implementation
- ✅ Pass types documentation
- ✅ Refund policy

---

## 🔧 Technical Implementation

### Database Schema (Prisma)

```prisma
model BusPass {
  id             String    @id @default(uuid())
  student_id     String
  pass_type      String    // daily, weekly, monthly, semester
  price          Decimal
  purchase_date  DateTime  @default(now())
  activation_date DateTime?
  expiry_date    DateTime
  status         String    @default("active") // active, expired, cancelled, pending
  qr_code        String    @unique
  qr_code_url    String?
  payment_id     String?   @unique
  usage_count    Int       @default(0)
  max_usage      Int?      // daily limit if applicable
  notes          String?
  created_at     DateTime  @default(now())
  updated_at     DateTime  @updatedAt

  student  Student   @relation(fields: [student_id], references: [id], onDelete: Cascade)
  payment  Payment?  @relation(fields: [payment_id], references: [id])
  scans    TripScan[]

  @@index([student_id])
  @@index([status])
  @@index([expiry_date])
}

model Payment {
  id                      String    @id @default(uuid())
  student_id              String
  amount                  Decimal
  currency                String    @default("PKR")
  payment_method          String?   // card, mobile_wallet, bank_transfer
  status                  String    @default("pending") // pending, completed, failed, refunded
  gateway_transaction_id  String?   @unique
  gateway_response        Json?
  payment_date            DateTime?
  refund_date             DateTime?
  refund_reason           String?
  receipt_url             String?
  metadata                Json?
  created_at              DateTime  @default(now())
  updated_at              DateTime  @updatedAt

  student Student  @relation(fields: [student_id], references: [id])
  bus_pass BusPass?

  @@index([student_id])
  @@index([status])
  @@index([payment_date])
}

model TripScan {
  id                 String    @id @default(uuid())
  trip_id            String
  bus_pass_id        String
  student_id         String
  driver_id          String
  scan_time          DateTime  @default(now())
  latitude           Decimal?
  longitude          Decimal?
  location           Unsupported("geography(Point, 4326)")?
  is_valid           Boolean   @default(true)
  validation_message String?
  created_at         DateTime  @default(now())

  trip     Trip     @relation(fields: [trip_id], references: [id], onDelete: Cascade)
  bus_pass BusPass  @relation(fields: [bus_pass_id], references: [id])
  student  Student  @relation(fields: [student_id], references: [id])
  driver   Driver   @relation(fields: [driver_id], references: [id])

  @@index([trip_id])
  @@index([bus_pass_id])
  @@index([student_id])
  @@index([scan_time])
}
```

### QR Code Structure

```typescript
// QR Code Payload (before encryption)
interface QRCodePayload {
  pass_id: string;
  student_id: string;
  pass_type: string;
  issue_date: string;
  expiry_date: string;
  signature: string; // HMAC signature
}

// Encryption
import crypto from 'crypto';

const encryptQRData = (data: QRCodePayload): string => {
  const algorithm = 'aes-256-gcm';
  const key = Buffer.from(process.env.QR_ENCRYPTION_KEY, 'hex');
  const iv = crypto.randomBytes(16);
  
  const cipher = crypto.createCipheriv(algorithm, key, iv);
  let encrypted = cipher.update(JSON.stringify(data), 'utf8', 'hex');
  encrypted += cipher.final('hex');
  
  const authTag = cipher.getAuthTag();
  
  return iv.toString('hex') + ':' + encrypted + ':' + authTag.toString('hex');
};

const decryptQRData = (encryptedData: string): QRCodePayload => {
  const [ivHex, encrypted, authTagHex] = encryptedData.split(':');
  const algorithm = 'aes-256-gcm';
  const key = Buffer.from(process.env.QR_ENCRYPTION_KEY, 'hex');
  const iv = Buffer.from(ivHex, 'hex');
  const authTag = Buffer.from(authTagHex, 'hex');
  
  const decipher = crypto.createDecipheriv(algorithm, key, iv);
  decipher.setAuthTag(authTag);
  
  let decrypted = decipher.update(encrypted, 'hex', 'utf8');
  decrypted += decipher.final('utf8');
  
  return JSON.parse(decrypted);
};
```

### KuickPay Integration

```typescript
// Payment Intent
const createPaymentIntent = async (amount: number, metadata: any) => {
  const response = await axios.post(
    `${KUICKPAY_BASE_URL}/payment-intents`,
    {
      amount,
      currency: 'PKR',
      payment_method_types: ['card', 'mobile_wallet'],
      metadata,
      return_url: `${APP_URL}/payment/success`,
      cancel_url: `${APP_URL}/payment/cancel`,
    },
    {
      headers: {
        'Authorization': `Bearer ${KUICKPAY_API_KEY}`,
        'Content-Type': 'application/json',
      }
    }
  );
  
  return response.data;
};

// Webhook Handler
const handlePaymentWebhook = async (req, res) => {
  const signature = req.headers['kuickpay-signature'];
  const payload = req.body;
  
  // Verify signature
  const isValid = verifyWebhookSignature(payload, signature, KUICKPAY_WEBHOOK_SECRET);
  if (!isValid) {
    return res.status(401).json({ error: 'Invalid signature' });
  }
  
  const { event, data } = payload;
  
  switch (event) {
    case 'payment.success':
      await handlePaymentSuccess(data);
      break;
    case 'payment.failed':
      await handlePaymentFailed(data);
      break;
    case 'refund.processed':
      await handleRefundProcessed(data);
      break;
  }
  
  res.status(200).json({ received: true });
};
```

---

## 🚀 Execution Prompts

### Prompt 1: Create Pass Management Backend
```
Implement bus pass management system:

1. Database Models (Prisma):
   - BusPass model with all fields
   - Payment model linked to BusPass
   - TripScan model for verification records

2. Pass Types Configuration:
   ```typescript
   const PASS_TYPES = {
     daily: { name: 'Daily Pass', price: 100, validity_days: 1 },
     weekly: { name: 'Weekly Pass', price: 600, validity_days: 7 },
     monthly: { name: 'Monthly Pass', price: 2000, validity_days: 30 },
     semester: { name: 'Semester Pass', price: 8000, validity_days: 150 }
   };
   ```

3. API Endpoints:
   - GET /api/v1/passes/types - Get all pass types with pricing
   - GET /api/v1/passes/my-passes - Get student's passes (auth required)
   - GET /api/v1/passes/active - Get active pass with QR code
   - POST /api/v1/passes/purchase - Purchase new pass
     * Create payment intent
     * Create pass with status 'pending'
     * Return payment URL
   - GET /api/v1/passes/:id - Get pass details
   - GET /api/v1/passes/:id/usage - Get scan history
   - POST /api/v1/passes/verify - Verify QR code (driver only)
     * Decrypt QR code
     * Validate pass (active, not expired)
     * Check usage limits
     * Record scan
     * Return student and pass info

4. QR Code Generation:
   - Generate on pass activation
   - Encrypt pass data with AES-256
   - Generate QR image using 'qrcode' library
   - Store QR image in S3 or filesystem
   - Return QR code URL

5. Business Logic:
   - Pass activates after successful payment
   - Calculate expiry date based on pass type
   - Cannot have multiple active passes of same type
   - Daily usage limit (if configured)
   - Auto-expire passes after expiry date (cron job)

6. Validation:
   - Student must exist
   - Pass type must be valid
   - Cannot purchase if active pass exists
   - QR code must be unique

Include TypeScript types, encryption, validation, and error handling.
```

### Prompt 2: Integrate KuickPay Payment Gateway
```
Implement KuickPay payment integration:

1. KuickPay Service Class:
   ```typescript
   class KuickPayService {
     createPaymentIntent(amount, metadata)
     verifyPaymentSignature(payload, signature)
     getPaymentStatus(transactionId)
     processRefund(transactionId, amount, reason)
   }
   ```

2. Payment Flow:
   a. Student selects pass type
   b. Backend creates payment intent with KuickPay
   c. Return payment URL to student app
   d. Student completes payment on KuickPay
   e. KuickPay sends webhook to backend
   f. Backend verifies webhook and activates pass
   g. Send confirmation to student

3. API Endpoints:
   - POST /api/v1/payments/intent - Create payment intent
     * Input: pass_type
     * Create payment record with status 'pending'
     * Call KuickPay API
     * Return payment URL and payment_id
   
   - POST /api/v1/payments/webhook - Handle KuickPay webhooks
     * Verify signature
     * Parse event (payment.success, payment.failed, refund.processed)
     * Update payment status
     * Activate pass if payment successful
     * Send email notification
   
   - GET /api/v1/payments/history - Get payment history
   - GET /api/v1/payments/:id/receipt - Download receipt PDF
   - POST /api/v1/admin/payments/:id/refund - Process refund (admin only)

4. Webhook Verification:
   - Verify KuickPay signature using HMAC-SHA256
   - Validate payload structure
   - Check transaction hasn't been processed already
   - Log all webhook attempts

5. Error Handling:
   - Handle payment failures gracefully
   - Retry failed payments
   - Send failure notifications
   - Log all errors with context

6. Security:
   - Never store card details
   - Use HTTPS for all API calls
   - Validate all webhook signatures
   - Encrypt sensitive data

7. Testing:
   - Use KuickPay test environment
   - Test successful payment flow
   - Test failed payment handling
   - Test webhook processing
   - Test refund processing

Include proper error handling, logging, and webhook verification.
```

### Prompt 3: Create QR Code Scanner in Driver App
```
Implement QR code scanner for driver app:

1. QRScannerScreen.tsx:
   - Full-screen camera view
   - QR code detection overlay
   - Scan instructions text
   - Manual entry button (backup)
   - Flash toggle button
   - Back button

2. Camera Setup:
   - Use react-native-vision-camera
   - Request camera permission
   - Configure for QR code scanning
   - Handle permission denied

3. QR Code Detection:
   - Use vision-camera-code-scanner plugin
   - Detect QR codes in real-time
   - Vibrate on successful scan
   - Play sound on scan
   - Show visual feedback

4. Verification Flow:
   - Scan QR code
   - Show loading indicator
   - Call API: POST /passes/verify with QR data
   - Show result modal (valid/invalid)
   - Display student info if valid
   - Display error message if invalid
   - Auto-dismiss after 3 seconds
   - Reset scanner for next scan

5. Verification Result Modal:
   - Valid Pass:
     * ✅ Success icon
     * Student name
     * Student ID
     * Pass type
     * Expiry date
     * "OK" button
   
   - Invalid Pass:
     * ❌ Error icon
     * Error message (expired, already used, etc.)
     * "OK" button

6. Offline Mode:
   - Cache valid passes in local storage
   - Verify against cache if offline
   - Sync scans when back online
   - Show offline indicator

7. Manual Entry:
   - Input field for QR code text
   - "Verify" button
   - Use when camera not working

8. Edge Cases:
   - Handle poor lighting
   - Handle damaged QR codes
   - Handle network errors
   - Handle concurrent scans
   - Prevent double scanning

9. Performance:
   - Optimize camera preview
   - Debounce scan events
   - Release camera on blur
   - Minimize battery usage

Include proper permission handling, offline support, and error handling.
```

### Prompt 4: Create Pass Purchase Flow in Student App
```
Create pass purchase flow for student app:

1. PassTypesScreen.tsx:
   - List of available pass types (cards)
   - Each card shows:
     * Pass name
     * Price
     * Validity period
     * Benefits
     * "Purchase" button
   - Filter/sort options
   - Active pass indicator (if already has one)

2. PassDetailScreen.tsx:
   - Large pass icon/image
   - Pass name and description
   - Price (prominent)
   - Validity details
   - Benefits list
   - Terms and conditions
   - "Buy Now" button
   - "Already have this pass" message if applicable

3. PurchaseConfirmationScreen.tsx:
   - Pass summary
   - Price breakdown
   - Selected payment method
   - "Confirm Purchase" button
   - "Cancel" button
   - Terms acceptance checkbox

4. Payment Flow:
   - Call API to create payment intent
   - Receive payment URL
   - Open payment URL in:
     a. In-app WebView (if supported), OR
     b. External browser
   - Handle payment callback:
     * Success: Show success screen, fetch new pass
     * Failed: Show error screen, allow retry
     * Cancelled: Return to pass types screen

5. PaymentSuccessScreen.tsx:
   - ✅ Success animation
   - "Payment Successful" message
   - Pass details
   - "View My Pass" button
   - "Download Receipt" button
   - Auto-redirect to My Passes (5 seconds)

6. PaymentFailedScreen.tsx:
   - ❌ Error icon
   - "Payment Failed" message
   - Error reason
   - "Try Again" button
   - "Contact Support" button

7. MyPassesScreen.tsx:
   - List of all passes (active, expired)
   - Active pass highlighted at top
   - Each pass card shows:
     * Pass type
     * Status badge
     * Expiry date
     * "View QR Code" button (if active)
   - Filter by status
   - Refresh button

8. ActivePassScreen.tsx:
   - Large, scannable QR code (center)
   - Pass type and status
   - Expiry countdown
   - Student name and ID
   - Usage count (if daily limit)
   - "Renew" button (when near expiry)
   - Brightness increase hint
   - "Download Receipt" button

9. Features:
   - Pull-to-refresh on all list screens
   - Show loading states during payment
   - Cache passes for offline viewing
   - Download receipts as PDF
   - Share pass via message/email

Include proper state management, error handling, and payment callbacks.
```

### Prompt 5: Create Admin Pass Management Interface
```
Create pass and payment management for admin dashboard:

1. PassesListPage.tsx:
   - Data table with columns:
     * Student Name
     * Student ID
     * Pass Type
     * Status (badge)
     * Purchase Date
     * Expiry Date
     * Price
     * Actions
   - Search by student name or ID
   - Filter by: pass_type, status, date range
   - Pagination
   - Export to CSV
   - Stats cards at top:
     * Total Active Passes
     * Expiring Soon (next 7 days)
     * Expired This Month
     * Total Revenue

2. PassDetailPage.tsx:
   - Pass information
   - Student information
   - Payment information
   - QR code image
   - Usage history (scans)
   - Timeline (purchase → activation → scans → expiry)
   - "Cancel Pass" button (with refund option)
   - "Extend Pass" button

3. PaymentsListPage.tsx:
   - Data table with columns:
     * Transaction ID
     * Student
     * Amount
     * Payment Method
     * Status
     * Date
     * Actions
   - Search by transaction ID or student
   - Filter by: status, payment_method, date range
   - Pagination
   - Export to CSV
   - Stats cards:
     * Total Revenue
     * Successful Payments
     * Failed Payments
     * Refunds Processed

4. PaymentDetailPage.tsx:
   - Payment information
   - Student information
   - Pass information
   - Gateway response data
   - Transaction timeline
   - "Download Receipt" button
   - "Process Refund" button (if eligible)

5. RefundModal.tsx:
   - Refund amount input (pre-filled with payment amount)
   - Refund reason dropdown
   - Additional notes textarea
   - "Confirm Refund" button
   - Warning message about pass cancellation

6. RevenueAnalyticsPage.tsx:
   - Revenue chart (line/bar chart)
   - Revenue by pass type (pie chart)
   - Revenue by payment method
   - Date range selector
   - Export report button
   - Key metrics:
     * Total Revenue
     * Average Transaction Value
     * Most Popular Pass Type
     * Conversion Rate

7. PassUsageAnalyticsPage.tsx:
   - Active passes over time
   - Usage frequency
   - Peak usage hours
   - Most used routes
   - Student engagement metrics

Include proper data visualization, export functionality, and analytics.
```

---

## ✅ Acceptance Criteria

Phase 5 is complete when:

- [ ] Students can view all pass types
- [ ] Students can purchase passes
- [ ] Payment integration with KuickPay works
- [ ] Pass activates after successful payment
- [ ] QR codes generated and encrypted
- [ ] Students can view QR code in app
- [ ] Drivers can scan QR codes
- [ ] Pass verification works (online and offline)
- [ ] Scan history recorded
- [ ] Payment webhooks processed correctly
- [ ] Email confirmations sent
- [ ] Admin can manage all passes
- [ ] Admin can process refunds
- [ ] Analytics show revenue data
- [ ] All tests pass with >80% coverage
- [ ] Receipt generation works

---

## 🧪 Testing Checklist

### Backend Testing
- [ ] Pass creation works
- [ ] QR code encryption/decryption works
- [ ] Payment intent creation works
- [ ] Webhook signature verification works
- [ ] Pass verification works
- [ ] Expired pass detected
- [ ] Usage limits enforced
- [ ] Refund processing works

### Student App Testing
- [ ] Can view pass types
- [ ] Purchase flow completes
- [ ] Payment success handled
- [ ] Payment failure handled
- [ ] QR code displays correctly
- [ ] QR code is scannable
- [ ] Pass list shows all passes
- [ ] Receipt download works

### Driver App Testing
- [ ] Camera permission requested
- [ ] QR scanner detects codes
- [ ] Valid pass verified
- [ ] Invalid pass rejected
- [ ] Student info displayed
- [ ] Scan recorded
- [ ] Offline verification works
- [ ] Scan history displays

### Admin Dashboard Testing
- [ ] Passes list loads
- [ ] Payment list loads
- [ ] Can search and filter
- [ ] Can view details
- [ ] Refund processing works
- [ ] Analytics display correctly
- [ ] Export to CSV works

---

## 📊 Success Metrics

- **Payment Success Rate:** >95%
- **QR Code Scan Time:** <2 seconds
- **Pass Activation Time:** <30 seconds after payment
- **Webhook Processing Time:** <5 seconds
- **QR Code Scan Success Rate:** >98%
- **API Response Time:** <200ms
- **Test Coverage:** >80%

---

## 🔄 Next Phase

After completing Phase 5, proceed to:
➡️ **Phase 6: Notifications & Analytics**

---

## 📝 Notes

- Store QR encryption key in environment variables
- Use strong encryption (AES-256-GCM)
- Verify all webhook signatures
- Never store card details
- Log all payment attempts
- Handle payment failures gracefully
- Test with KuickPay sandbox environment
- Implement idempotency for payments
- Consider PCI compliance requirements
- Monitor payment success rates

---

**Phase Owner:** Backend Lead + Mobile Lead  
**Created:** December 26, 2025  
**Last Updated:** December 26, 2025

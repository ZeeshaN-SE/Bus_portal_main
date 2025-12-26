# Phase 2: Authentication & User Management
## University Bus Tracker System

**Duration:** 2 weeks  
**Status:** 🟡 Not Started  
**Priority:** 🔴 Critical  
**Depends On:** Phase 1 ✅

---

## 🎯 Goal

Implement complete authentication and user management system with role-based access control (RBAC). Enable users to register, login, manage profiles, and handle password reset functionality.

---

## 📋 Key Features / Tasks

### 1. Database Schema - Users & Roles
- [ ] Create User model (id, email, password_hash, email_verified, etc.)
- [ ] Create Role model (student, driver, admin, super_admin)
- [ ] Create UserRole junction table
- [ ] Create Profile model (first_name, last_name, phone, avatar_url, etc.)
- [ ] Create Student model (student_id, department, year_of_study)
- [ ] Create Driver model (license_number, license_expiry_date)
- [ ] Create Session model (for JWT refresh tokens)
- [ ] Run migrations and test with seed data

### 2. Backend - Authentication API
- [ ] Implement user registration endpoint
- [ ] Implement login with JWT generation
- [ ] Implement refresh token mechanism
- [ ] Implement email verification
- [ ] Implement password reset flow
- [ ] Implement logout (invalidate tokens)
- [ ] Create authentication middleware
- [ ] Create role-based authorization middleware
- [ ] Implement rate limiting for auth endpoints
- [ ] Hash passwords with bcrypt (12 rounds)

### 3. Backend - User Management API
- [ ] Get current user profile endpoint
- [ ] Update user profile endpoint
- [ ] Change password endpoint
- [ ] Upload avatar endpoint
- [ ] Get user by ID (admin only)
- [ ] List users with pagination (admin only)
- [ ] Deactivate/activate user (admin only)

### 4. Mobile Apps - Authentication UI
- [ ] Design and implement Splash Screen
- [ ] Design and implement Login Screen
- [ ] Design and implement Register Screen
- [ ] Design and implement Forgot Password Screen
- [ ] Design and implement Email Verification Screen
- [ ] Implement form validation
- [ ] Implement error handling and user feedback
- [ ] Store JWT tokens securely (AsyncStorage/MMKV)
- [ ] Implement auto-login on app start
- [ ] Implement token refresh logic

### 5. Mobile Apps - Profile Management
- [ ] Design and implement Profile Screen
- [ ] Implement Edit Profile functionality
- [ ] Implement Change Password functionality
- [ ] Implement Avatar Upload functionality
- [ ] Implement Logout functionality

### 6. Admin Dashboard - Authentication
- [ ] Design and implement Login Page
- [ ] Implement authentication flow
- [ ] Store tokens in httpOnly cookies or localStorage
- [ ] Implement auto-login
- [ ] Implement token refresh
- [ ] Implement protected routes

### 7. Admin Dashboard - User Management
- [ ] Design and implement Users List Page
- [ ] Implement user search and filtering
- [ ] Implement user detail view
- [ ] Implement create user functionality
- [ ] Implement edit user functionality
- [ ] Implement deactivate/activate user
- [ ] Implement role management

### 8. Security Implementation
- [ ] Implement JWT token generation and validation
- [ ] Set token expiry (1 hour for access, 7 days for refresh)
- [ ] Implement account lockout after failed attempts
- [ ] Implement email verification tokens
- [ ] Implement password reset tokens with expiry
- [ ] Add security headers (Helmet.js)
- [ ] Implement CORS properly
- [ ] Add rate limiting (5 req/min for auth endpoints)

### 9. Email Service Integration
- [ ] Set up SendGrid or AWS SES
- [ ] Create email templates (verification, password reset, welcome)
- [ ] Implement email sending service
- [ ] Test email delivery
- [ ] Handle email errors gracefully

### 10. Testing
- [ ] Write unit tests for authentication service
- [ ] Write integration tests for auth endpoints
- [ ] Write unit tests for user service
- [ ] Test password hashing and verification
- [ ] Test JWT generation and validation
- [ ] Test role-based access control
- [ ] Test email verification flow
- [ ] Test password reset flow

---

## 📦 Deliverables

### Backend Deliverables
- ✅ Complete authentication API (7 endpoints)
- ✅ User management API (7 endpoints)
- ✅ JWT authentication middleware
- ✅ Role-based authorization middleware
- ✅ Email service integration
- ✅ Database migrations for user tables
- ✅ Seed data with sample users

### Mobile App Deliverables
- ✅ Authentication flow (Login, Register, Forgot Password)
- ✅ Profile management screens
- ✅ Token management and auto-login
- ✅ Form validation and error handling

### Admin Dashboard Deliverables
- ✅ Login page
- ✅ User management interface
- ✅ Protected routes
- ✅ Role-based access control

### Documentation Deliverables
- ✅ API documentation for authentication endpoints
- ✅ User management API documentation
- ✅ Authentication flow diagram
- ✅ Security best practices document

---

## 🔧 Technical Implementation

### JWT Token Structure

```typescript
// Access Token (1 hour expiry)
{
  "user_id": "uuid",
  "email": "user@university.edu",
  "role": "student",
  "iat": 1703592000,
  "exp": 1703595600
}

// Refresh Token (7 days expiry)
{
  "user_id": "uuid",
  "session_id": "uuid",
  "iat": 1703592000,
  "exp": 1704196800
}
```

### Database Schema (Prisma)

```prisma
model User {
  id                         String    @id @default(uuid())
  email                      String    @unique
  password_hash              String
  email_verified             Boolean   @default(false)
  email_verification_token   String?
  email_verification_expires DateTime?
  password_reset_token       String?
  password_reset_expires     DateTime?
  last_login_at              DateTime?
  login_attempts             Int       @default(0)
  locked_until               DateTime?
  is_active                  Boolean   @default(true)
  created_at                 DateTime  @default(now())
  updated_at                 DateTime  @updatedAt
  deleted_at                 DateTime?

  profile    Profile?
  user_roles UserRole[]
  sessions   Session[]
  student    Student?
  driver     Driver?
}

model Role {
  id          String   @id @default(uuid())
  name        String   @unique
  description String?
  permissions Json?
  created_at  DateTime @default(now())
  updated_at  DateTime @updatedAt

  user_roles UserRole[]
}

model UserRole {
  id          String   @id @default(uuid())
  user_id     String
  role_id     String
  assigned_at DateTime @default(now())

  user User @relation(fields: [user_id], references: [id], onDelete: Cascade)
  role Role @relation(fields: [role_id], references: [id], onDelete: Cascade)

  @@unique([user_id, role_id])
}

model Profile {
  id            String   @id @default(uuid())
  user_id       String   @unique
  first_name    String
  last_name     String
  phone         String?
  avatar_url    String?
  date_of_birth DateTime?
  address       String?
  city          String?
  postal_code   String?
  country       String   @default("Pakistan")
  created_at    DateTime @default(now())
  updated_at    DateTime @updatedAt

  user User @relation(fields: [user_id], references: [id], onDelete: Cascade)
}
```

---

## 🚀 Execution Prompts

### Prompt 1: Create User Authentication Backend
```
Implement complete user authentication system for the backend API:

1. Database Models (using Prisma):
   - User model with email, password_hash, email_verified, login_attempts, etc.
   - Role model with predefined roles (student, driver, admin)
   - UserRole junction table
   - Profile model linked to User

2. Authentication Endpoints:
   - POST /api/v1/auth/register - Register new user
   - POST /api/v1/auth/login - Login with email/password
   - POST /api/v1/auth/refresh - Refresh access token
   - POST /api/v1/auth/verify-email - Verify email with token
   - POST /api/v1/auth/forgot-password - Request password reset
   - POST /api/v1/auth/reset-password - Reset password with token
   - POST /api/v1/auth/logout - Logout and invalidate tokens

3. Security Features:
   - Hash passwords with bcrypt (12 rounds)
   - Generate JWT tokens (access: 1h, refresh: 7d)
   - Implement account lockout after 5 failed attempts
   - Generate email verification tokens
   - Generate password reset tokens with 1-hour expiry
   - Rate limit auth endpoints (5 requests/minute)

4. Middleware:
   - Authentication middleware (verify JWT)
   - Role-based authorization middleware
   - Request validation middleware (Joi/Zod)

Include proper error handling, validation, and TypeScript types.
```

### Prompt 2: Create User Management Backend
```
Implement user management API endpoints:

1. User Profile Endpoints:
   - GET /api/v1/users/me - Get current user profile
   - PATCH /api/v1/users/me - Update current user profile
   - POST /api/v1/users/me/avatar - Upload avatar image
   - POST /api/v1/users/me/change-password - Change password

2. Admin Endpoints:
   - GET /api/v1/admin/users - List all users (paginated, searchable)
   - GET /api/v1/admin/users/:id - Get user by ID
   - POST /api/v1/admin/users - Create new user
   - PATCH /api/v1/admin/users/:id - Update user
   - DELETE /api/v1/admin/users/:id - Deactivate user

3. Features:
   - File upload for avatars (store in S3 or local filesystem)
   - Password validation (min 8 chars, uppercase, lowercase, number, special char)
   - Email validation
   - Phone number validation
   - Pagination and filtering for user lists
   - Role-based access control (admin only for admin endpoints)

Include proper TypeScript types, error handling, and validation.
```

### Prompt 3: Create Mobile Authentication UI
```
Create authentication flow for React Native mobile app (student-app):

1. Screens:
   - SplashScreen.tsx - Show logo, check if user logged in
   - LoginScreen.tsx - Email/password login form
   - RegisterScreen.tsx - Registration form with role selection
   - ForgotPasswordScreen.tsx - Email input for password reset
   - VerifyEmailScreen.tsx - Show verification message, resend option

2. Features:
   - Form validation with React Hook Form + Yup
   - Show loading indicators during API calls
   - Display error messages clearly
   - Remember me functionality
   - Navigate to home screen after successful login
   - Store JWT tokens securely in AsyncStorage or MMKV
   - Implement auto-login if valid token exists

3. UI Requirements:
   - Use React Native Paper or custom components
   - Follow Material Design guidelines
   - Responsive design for different screen sizes
   - Smooth animations and transitions
   - Keyboard handling (dismiss on scroll)

4. State Management:
   - Create Redux slice for auth state (user, token, loading, error)
   - Create API integration functions
   - Handle token refresh automatically

Include TypeScript types, proper error handling, and accessibility features.
```

### Prompt 4: Create Mobile Profile Management
```
Create profile management screens for React Native app:

1. ProfileScreen.tsx:
   - Display user information (name, email, phone, avatar)
   - Edit profile button
   - Change password button
   - Logout button
   - View pass history (placeholder for now)
   - Settings/preferences option

2. EditProfileScreen.tsx:
   - Form to edit: first name, last name, phone, date of birth, address
   - Avatar upload with image picker
   - Save button with loading state
   - Validation for all fields

3. ChangePasswordScreen.tsx:
   - Current password field
   - New password field
   - Confirm new password field
   - Password strength indicator
   - Save button

4. Features:
   - Image picker for avatar (react-native-image-picker)
   - Crop and resize images before upload
   - Show success/error toasts
   - Update Redux state after successful changes
   - Confirm before logout

Include proper validation, error handling, and TypeScript types.
```

### Prompt 5: Create Admin Dashboard Authentication
```
Create authentication system for React.js admin dashboard:

1. LoginPage.tsx:
   - Email/password login form
   - Remember me checkbox
   - Forgot password link
   - Login button with loading state
   - Error message display

2. Authentication Flow:
   - Store tokens in localStorage or httpOnly cookies
   - Create auth slice in Redux
   - Create API integration with Axios
   - Implement auto-login on page load
   - Redirect to dashboard after successful login
   - Redirect to login if token invalid

3. Protected Routes:
   - Create PrivateRoute component
   - Check authentication before allowing access
   - Redirect to login if not authenticated
   - Show loading spinner while checking auth

4. Token Management:
   - Implement token refresh logic
   - Add auth token to all API requests (Axios interceptor)
   - Handle 401 errors (refresh token or logout)
   - Clear tokens on logout

5. UI/UX:
   - Use Material-UI or Ant Design components
   - Responsive design
   - Form validation with React Hook Form
   - Loading indicators
   - Error messages

Include TypeScript types and proper error handling.
```

### Prompt 6: Create Admin User Management Interface
```
Create user management interface for admin dashboard:

1. UsersListPage.tsx:
   - Data table with columns: Name, Email, Role, Status, Created Date, Actions
   - Search functionality (by name or email)
   - Filter by role (student, driver, admin)
   - Filter by status (active, inactive)
   - Pagination (20 items per page)
   - Create New User button
   - Actions: View, Edit, Deactivate/Activate

2. UserDetailPage.tsx:
   - Display all user information
   - Show role badges
   - Show account status
   - Edit button
   - Deactivate/Activate button
   - Reset password button (admin only)

3. CreateUserPage.tsx:
   - Form to create new user
   - Fields: email, password, first name, last name, role, phone
   - Validation
   - Submit button

4. EditUserPage.tsx:
   - Form to edit user information
   - Pre-filled with existing data
   - Cannot change email
   - Can change role (with confirmation)
   - Save button

5. Features:
   - Use AG Grid or Material-UI DataGrid
   - Export to CSV functionality
   - Confirmation modals for destructive actions
   - Real-time search with debouncing
   - Proper loading and error states

Include TypeScript types, proper state management, and error handling.
```

### Prompt 7: Implement Email Service
```
Create email service for sending transactional emails:

1. Set up SendGrid or AWS SES:
   - Configure API credentials
   - Set up verified sender email
   - Create email templates

2. Email Service Class:
   - sendVerificationEmail(email, token, name)
   - sendPasswordResetEmail(email, token, name)
   - sendWelcomeEmail(email, name)
   - sendPasswordChangedEmail(email, name)

3. Email Templates (HTML):
   - Verification email with clickable link
   - Password reset email with secure link
   - Welcome email after verification
   - Password changed confirmation

4. Features:
   - Queue emails for async sending (Bull queue with Redis)
   - Retry failed emails (3 attempts)
   - Log all email attempts
   - Handle unsubscribe requests
   - Track email open rates (optional)

5. Testing:
   - Use email testing service (Mailtrap) for development
   - Test all email templates
   - Test link expiry
   - Test error handling

Include proper error handling and logging.
```

### Prompt 8: Write Tests for Authentication
```
Write comprehensive tests for authentication system:

1. Unit Tests (Jest):
   - AuthService.register() - success and failure cases
   - AuthService.login() - valid/invalid credentials
   - AuthService.verifyToken() - valid/expired/invalid tokens
   - Password hashing and verification
   - Token generation and validation
   - Email validation
   - Password strength validation

2. Integration Tests (Supertest):
   - POST /auth/register - success, duplicate email, validation errors
   - POST /auth/login - success, wrong password, account locked
   - POST /auth/refresh - valid token, expired token, invalid token
   - POST /auth/verify-email - success, expired token, invalid token
   - POST /auth/forgot-password - success, non-existent email
   - POST /auth/reset-password - success, expired token
   - POST /auth/logout - success

3. Middleware Tests:
   - Authentication middleware - valid/invalid/missing tokens
   - Authorization middleware - correct/incorrect roles
   - Rate limiting - exceeding limits

4. Test Coverage:
   - Aim for >80% code coverage
   - Test all edge cases
   - Test error handling
   - Test security features (account lockout, etc.)

Include proper test setup, teardown, and mock data.
```

---

## ✅ Acceptance Criteria

Phase 2 is complete when:

- [ ] Users can register with email and password
- [ ] Users receive email verification link
- [ ] Users can login with verified email
- [ ] JWT tokens are generated and validated correctly
- [ ] Token refresh works automatically
- [ ] Users can reset forgotten passwords
- [ ] Profile management works (view, edit, change password, avatar upload)
- [ ] Admin can view and manage all users
- [ ] Role-based access control works correctly
- [ ] Account lockout works after failed attempts
- [ ] All authentication endpoints are secure
- [ ] All tests pass with >80% coverage
- [ ] Mobile apps handle authentication flow smoothly
- [ ] Admin dashboard authentication works
- [ ] Documentation is complete

---

## 🧪 Testing Checklist

### Backend Testing
- [ ] User can register successfully
- [ ] Duplicate email registration fails
- [ ] Login with correct credentials succeeds
- [ ] Login with wrong password fails
- [ ] Account locks after 5 failed attempts
- [ ] Email verification works
- [ ] Password reset flow works
- [ ] JWT tokens are valid
- [ ] Token refresh works
- [ ] Protected endpoints require authentication
- [ ] Role-based access control works
- [ ] Rate limiting prevents brute force

### Mobile App Testing
- [ ] Splash screen checks for existing session
- [ ] Login form validates input
- [ ] Login navigates to home on success
- [ ] Register form validates all fields
- [ ] Tokens are stored securely
- [ ] Auto-login works on app restart
- [ ] Profile screen displays user data
- [ ] Edit profile saves changes
- [ ] Avatar upload works
- [ ] Change password works
- [ ] Logout clears tokens and navigates to login

### Admin Dashboard Testing
- [ ] Login page validates input
- [ ] Login redirects to dashboard on success
- [ ] Protected routes redirect to login if not authenticated
- [ ] Users list loads and displays correctly
- [ ] Search and filter work
- [ ] Pagination works
- [ ] Create user form works
- [ ] Edit user form works
- [ ] Deactivate user works
- [ ] Logout works

---

## 📊 Success Metrics

- **Registration Success Rate:** >95%
- **Login Success Rate:** >98%
- **Token Refresh Success Rate:** >99%
- **Email Delivery Rate:** >95%
- **API Response Time:** <200ms (95th percentile)
- **Test Coverage:** >80%
- **Security Audit:** No critical vulnerabilities

---

## 🔄 Next Phase

After completing Phase 2, proceed to:
➡️ **Phase 3: Bus & Route Management**

---

## 📝 Notes

- Use strong password requirements (min 8 chars, mix of upper/lower/numbers/special)
- Store tokens securely (never in plain text)
- Log all authentication attempts for security auditing
- Rate limit aggressive to prevent brute force attacks
- Test email delivery thoroughly before production
- Consider implementing 2FA in future phases
- Document all API endpoints in Swagger/OpenAPI

---

**Phase Owner:** Backend Lead + Mobile Lead  
**Created:** December 26, 2025  
**Last Updated:** December 26, 2025

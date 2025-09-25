# Issue #002: User Login Authentication

## Description

Implement a comprehensive user authentication system for the caautoline automotive marketplace. The system should handle traditional email/password authentication with JWT tokens. The frontend login forms are already implemented and require backend API endpoints to process authentication requests.

Based on the frontend analysis, the authentication system needs to handle:
- Traditional email/password login with JWT tokens
- Remember me functionality with refresh tokens
- Session management and token refresh
- Account status validation (active, suspended, pending verification)
- Login attempt tracking and rate limiting
- User profile retrieval after successful authentication

## Acceptance Criteria

- [ ] Create user login API endpoint that accepts email/username and password
- [ ] Implement JWT token generation with access and refresh tokens
- [ ] Validate user credentials against hashed passwords
- [ ] Check user account status before allowing login
- [ ] Implement "remember me" functionality with extended token expiration
- [ ] Track login attempts and implement rate limiting
- [ ] Update last_login timestamp on successful authentication
- [ ] Return user profile data with authentication tokens
- [ ] Implement token refresh endpoint
- [ ] Log all authentication attempts for security monitoring
- [ ] Validate all input data using class-validator decorators
- [ ] Create comprehensive unit and integration tests

## Technical Requirements

### Database Schema Updates
Refer to the backend structure documentation for the complete database schema. The following tables are required:

- `user_refresh_tokens` table - For JWT refresh token management
- `login_attempts` table - For tracking login attempts and rate limiting
- Update `users` table to include login tracking fields (failed_login_attempts, locked_until)

### NestJS Implementation Structure

The implementation should follow NestJS best practices with proper module organization:
- Authentication module with login, token refresh, and logout services
- JWT strategy and guards for token validation
- Login tracking service for security monitoring
- User management module
- Common utilities for decorators and interceptors

## API Endpoints

### 1. Traditional Login
```
POST /api/auth/login
```

**Request Body:**
```json
{
  "email": "john.doe@example.com",
  "password": "SecurePass123!",
  "remember_me": false
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": 123,
      "email": "john.doe@example.com",
      "first_name": "John",
      "last_name": "Doe",
      "role": "buyer",
      "status": "active",
      "email_verified": true,
      "profile_picture": "https://example.com/avatar.jpg"
    },
    "tokens": {
      "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "expires_in": 3600
    }
  }
}
```

**Error Response (401):**
```json
{
  "success": false,
  "message": "Invalid credentials",
  "error_code": "INVALID_CREDENTIALS"
}
```

**Error Response (423):**
```json
{
  "success": false,
  "message": "Account locked due to too many failed attempts",
  "error_code": "ACCOUNT_LOCKED",
  "data": {
    "locked_until": "2024-01-15T10:30:00Z"
  }
}
```

### 2. Token Refresh
```
POST /api/auth/refresh
```

**Request Body:**
```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Token refreshed successfully",
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 3600
  }
}
```

### 3. Logout
```
POST /api/auth/logout
```

**Headers:**
```
Authorization: Bearer <access_token>
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

### 4. Get Current User
```
GET /api/auth/me
```

**Headers:**
```
Authorization: Bearer <access_token>
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": 123,
      "email": "john.doe@example.com",
      "first_name": "John",
      "last_name": "Doe",
      "role": "buyer",
      "status": "active",
      "email_verified": true,
      "profile_picture": "https://example.com/avatar.jpg",
      "created_at": "2024-01-01T00:00:00Z",
      "last_login": "2024-01-15T10:00:00Z"
    }
  }
}
```

## Dependencies

### Required Packages
```json
{
  "@nestjs/passport": "^10.0.0",
  "@nestjs/jwt": "^10.0.0",
  "@nestjs/typeorm": "^10.0.0",
  "passport": "^0.6.0",
  "passport-jwt": "^4.0.0",
  "bcrypt": "^5.1.0",
  "class-validator": "^0.14.0",
  "class-transformer": "^0.5.1",
  "@nestjs/throttler": "^5.0.0",
  "uuid": "^9.0.0",
  "nodemailer": "^6.9.0"
}
```

### Environment Variables
```env
# JWT Configuration
JWT_SECRET=your_super_secure_jwt_secret_key
JWT_EXPIRES_IN=1h
JWT_REFRESH_SECRET=your_super_secure_refresh_secret_key
JWT_REFRESH_EXPIRES_IN=7d


# Security
MAX_LOGIN_ATTEMPTS=5
LOCKOUT_TIME=900000
RATE_LIMIT_TTL=3600
RATE_LIMIT_LIMIT=10

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_app_password
```

## Testing Requirements

### Unit Tests
- [ ] Test login with valid credentials
- [ ] Test login with invalid credentials
- [ ] Test account lockout after failed attempts
- [ ] Test JWT token generation and validation
- [ ] Test refresh token functionality
- [ ] Test rate limiting functionality
- [ ] Test remember me functionality
- [ ] Test user status validation

### Integration Tests
- [ ] Test complete login flow
- [ ] Test token refresh workflow
- [ ] Test logout functionality
- [ ] Test protected route access
- [ ] Test rate limiting across multiple requests
- [ ] Test database transaction handling

### Security Tests
- [ ] Test brute force attack prevention
- [ ] Test token tampering detection
- [ ] Test expired token handling
- [ ] Test revoked token handling
- [ ] Test SQL injection prevention
- [ ] Test XSS prevention in error messages

## Documentation

### API Documentation
- [ ] Update OpenAPI/Swagger documentation
- [ ] Document all authentication flows
- [ ] Add example requests and responses
- [ ] Document error codes and messages
- [ ] Create authentication flow diagrams

### Code Documentation
- [ ] Add JSDoc comments to all public methods
- [ ] Document JWT token structure
- [ ] Document security considerations
- [ ] Create README for authentication setup
- [ ] Document environment variable requirements

## Security Considerations

- [ ] Implement account lockout after 5 failed login attempts
- [ ] Use secure JWT secrets with sufficient entropy
- [ ] Implement token rotation for refresh tokens
- [ ] Validate all input data and sanitize outputs
- [ ] Log all authentication attempts with IP and user agent
- [ ] Implement rate limiting (max 10 requests per minute per IP)
- [ ] Use HTTPS in production for all authentication endpoints
- [ ] Implement CSRF protection for forms
- [ ] Secure password reset tokens with expiration
- [ ] Implement proper session management

## Performance Considerations

- [ ] Use database indexes for email and token lookups
- [ ] Implement caching for user profile data
- [ ] Use connection pooling for database connections
- [ ] Implement async email sending for password resets
- [ ] Cache frequently accessed user permissions
- [ ] Use bulk operations for login attempt logging
- [ ] Implement token blacklisting for logout

## Error Handling

### Standard Error Responses
```json
{
  "success": false,
  "message": "Error description",
  "error_code": "ERROR_CODE",
  "timestamp": "2024-01-15T10:00:00Z",
  "path": "/api/auth/login"
}
```

### Error Codes
- `INVALID_CREDENTIALS`: Wrong email or password
- `ACCOUNT_LOCKED`: Too many failed login attempts
- `ACCOUNT_INACTIVE`: Account is not active
- `EMAIL_NOT_VERIFIED`: Email address not verified
- `ACCOUNT_SUSPENDED`: Account has been suspended
- `TOKEN_EXPIRED`: JWT token has expired
- `TOKEN_INVALID`: JWT token is invalid
- `REFRESH_TOKEN_INVALID`: Refresh token is invalid or expired
- `RATE_LIMIT_EXCEEDED`: Too many requests from IP
- `VALIDATION_FAILED`: Request data validation failed

## Related Issues

- Issue #001: User Registration System
- Issue #003: Password Reset System
- Issue #004: User Profile Management
- Issue #006: Session Management System
- Issue #007: Social Media OAuth Integration

## Notes

- The frontend login forms expect these specific API endpoints and response formats
- "Remember me" functionality should extend token expiration to 30 days
- All database field names must use snake_case convention
- Implement proper logging for security monitoring and debugging

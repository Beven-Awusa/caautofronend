# Issue #001: User Registration System

## Description

Implement a comprehensive user registration system for the caautoline automotive marketplace. The system should handle traditional email/password signup with email verification. The frontend forms are already implemented and require backend API endpoints to process registration requests.

Based on the frontend analysis, the registration system needs to handle:
- Traditional email/password registration with validation
- Email verification system
- User profile creation with required fields
- Password strength validation
- Duplicate email prevention
- Account activation workflow

## Acceptance Criteria

- [ ] Create user registration API endpoint that accepts email, password, first_name, last_name
- [ ] Implement password strength validation (minimum 8 characters, complexity requirements)
- [ ] Validate email format and prevent duplicate email registrations
- [ ] Hash passwords using bcrypt with appropriate salt rounds
- [ ] Generate email verification tokens and send verification emails (handled by separate email service)
- [ ] Create user profile records in database with all required fields
- [ ] Return appropriate success/error responses with proper HTTP status codes
- [ ] Implement rate limiting to prevent spam registrations
- [ ] Log all registration attempts for security monitoring
- [ ] Validate all input data using class-validator decorators
- [ ] Create comprehensive unit and integration tests

## Technical Requirements

### Database Schema
Refer to the backend structure documentation for the complete database schema. The following tables are required:

- `users` table - Primary user data storage
- `user_social_links` table - For future social media integration

### NestJS Implementation Structure

The implementation should follow NestJS best practices with proper module organization:
- Authentication module with registration and email verification services
- User management module
- Common utilities for validation and error handling

## API Endpoints

### 1. Traditional Registration
```
POST /api/auth/register
```

**Request Body:**
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@example.com",
  "password": "SecurePass123!",
  "confirm_password": "SecurePass123!"
}
```

**Success Response (201):**
```json
{
  "success": true,
  "message": "Registration successful. Please check your email for verification.",
  "data": {
    "user_id": 123,
    "email": "john.doe@example.com",
    "status": "pending_verification"
  }
}
```

**Error Response (400):**
```json
{
  "success": false,
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "Email already exists"
    },
    {
      "field": "password",
      "message": "Password must be at least 8 characters long"
    }
  ]
}
```

### 2. Email Verification
```
POST /api/auth/verify-email
```

**Request Body:**
```json
{
  "token": "verification_token_here"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Email verified successfully",
  "data": {
    "user_id": 123,
    "email_verified": true,
    "status": "active"
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
  "nodemailer": "^6.9.0",
  "@nestjs/throttler": "^5.0.0",
  "uuid": "^9.0.0"
}
```

### Environment Variables
```env
# Database
DATABASE_HOST=localhost
DATABASE_PORT=3306
DATABASE_USERNAME=root
DATABASE_PASSWORD=password
DATABASE_NAME=caautoline

# JWT
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRES_IN=1h
JWT_REFRESH_SECRET=your_refresh_secret_key
JWT_REFRESH_EXPIRES_IN=7d


# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_app_password
```

## Testing Requirements

### Unit Tests
- [ ] Test user registration with valid data
- [ ] Test email validation and duplicate prevention
- [ ] Test password hashing and validation
- [ ] Test email verification token generation
- [ ] Test error handling for invalid inputs
- [ ] Test rate limiting functionality

### Integration Tests
- [ ] Test complete registration flow
- [ ] Test email verification workflow
- [ ] Test database transactions and rollbacks
- [ ] Test API endpoint responses and status codes

### Test Data
Create test fixtures for:
- Valid user registration data
- Invalid user registration data
- Email verification tokens

## Documentation

### API Documentation
- [ ] Update OpenAPI/Swagger documentation
- [ ] Document all request/response schemas
- [ ] Add example requests and responses
- [ ] Document error codes and messages

### Code Documentation
- [ ] Add JSDoc comments to all public methods
- [ ] Document complex business logic
- [ ] Add inline comments for non-obvious code
- [ ] Create README for auth module setup

## Security Considerations

- [ ] Implement rate limiting (max 5 registration attempts per IP per hour)
- [ ] Use secure password hashing with bcrypt (minimum 12 salt rounds)
- [ ] Validate and sanitize all input data
- [ ] Implement CSRF protection for forms
- [ ] Use HTTPS in production
- [ ] Log all registration attempts for monitoring
- [ ] Implement account lockout after failed attempts
- [ ] Validate email verification tokens with expiration

## Performance Considerations

- [ ] Use database indexes for email and verification token lookups
- [ ] Implement caching for frequently accessed user data
- [ ] Use connection pooling for database connections
- [ ] Implement async email sending to avoid blocking requests

## Related Issues

- Issue #002: User Login Authentication
- Issue #003: Password Reset System
- Issue #004: User Profile Management
- Issue #005: Email Notification System
- Issue #006: Social Media OAuth Integration

## Notes

- The frontend forms are already implemented and expect these specific API endpoints
- Email verification should include a link back to the frontend verification page
- All database field names must use snake_case convention as specified in the backend structure documentation

# Issue #003: Password Reset System

## Description

Implement a comprehensive password reset system for the caautoline automotive marketplace. The system should handle password reset requests, token generation, email notifications, and secure password updates. This functionality is triggered by the "Lost your password?" link in the login forms.

The frontend login forms already have password reset links that expect backend API endpoints to handle the complete password reset workflow including token validation and secure password updates.

## Acceptance Criteria

- [ ] Create password reset request API endpoint that accepts email
- [ ] Generate secure password reset tokens with expiration
- [ ] Send password reset emails with secure tokens
- [ ] Create password reset confirmation API endpoint
- [ ] Validate password reset tokens and expiration
- [ ] Implement secure password update functionality
- [ ] Handle token invalidation after use
- [ ] Implement rate limiting for password reset requests
- [ ] Log all password reset attempts for security monitoring
- [ ] Return appropriate success/error responses with proper HTTP status codes
- [ ] Validate all input data using class-validator decorators
- [ ] Create comprehensive unit and integration tests

## Technical Requirements

### Database Schema
Refer to the backend structure documentation for the complete database schema. The following tables are required:

- `password_reset_tokens` table - For storing password reset tokens with expiration
- Update `users` table to include password reset tracking fields

### NestJS Implementation Structure

The implementation should follow NestJS best practices with proper module organization:
- Authentication module with password reset services
- Email service for sending password reset notifications
- Token validation and security services
- Rate limiting guards for password reset endpoints

## API Endpoints

### 1. Password Reset Request
```
POST /api/auth/forgot-password
```

**Request Body:**
```json
{
  "email": "john.doe@example.com"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Password reset email sent if account exists"
}
```

**Error Response (400):**
```json
{
  "success": false,
  "message": "Invalid email format",
  "error_code": "INVALID_EMAIL"
}
```

### 2. Password Reset Confirmation
```
POST /api/auth/reset-password
```

**Request Body:**
```json
{
  "token": "password_reset_token_here",
  "new_password": "NewSecurePass123!",
  "confirm_password": "NewSecurePass123!"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Password reset successfully"
}
```

**Error Response (400):**
```json
{
  "success": false,
  "message": "Invalid or expired token",
  "error_code": "INVALID_TOKEN"
}
```

**Error Response (400):**
```json
{
  "success": false,
  "message": "Passwords do not match",
  "error_code": "PASSWORD_MISMATCH"
}
```


## Dependencies

### Required Packages
```json
{
  "@nestjs/jwt": "^10.0.0",
  "@nestjs/typeorm": "^10.0.0",
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
# JWT Configuration
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRES_IN=1h

# Password Reset
PASSWORD_RESET_TOKEN_EXPIRES_IN=1h
PASSWORD_RESET_RATE_LIMIT_TTL=3600
PASSWORD_RESET_RATE_LIMIT_LIMIT=3

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_app_password

# Frontend URLs
FRONTEND_URL=http://localhost:3000
PASSWORD_RESET_REDIRECT_URL=/reset-password
```

## Testing Requirements

### Unit Tests
- [ ] Test password reset request with valid email
- [ ] Test password reset request with invalid email
- [ ] Test password reset token generation
- [ ] Test password update functionality
- [ ] Test token expiration handling
- [ ] Test rate limiting functionality
- [ ] Test email sending functionality

### Integration Tests
- [ ] Test complete password reset flow
- [ ] Test password reset with expired token
- [ ] Test password reset with invalid token
- [ ] Test multiple password reset requests
- [ ] Test database transaction handling
- [ ] Test email delivery and content

### Security Tests
- [ ] Test rate limiting effectiveness
- [ ] Test token tampering prevention
- [ ] Test expired token handling
- [ ] Test invalid token handling
- [ ] Test password strength validation
- [ ] Test token reuse prevention

## Documentation

### API Documentation
- [ ] Update OpenAPI/Swagger documentation
- [ ] Document password reset flow
- [ ] Add example requests and responses
- [ ] Document error codes and messages

### Code Documentation
- [ ] Add JSDoc comments to all public methods
- [ ] Document password reset business logic
- [ ] Document token generation and validation
- [ ] Create README for password reset setup

## Security Considerations

- [ ] Implement rate limiting (max 3 password reset requests per IP per hour)
- [ ] Use secure token generation with sufficient entropy
- [ ] Set appropriate token expiration (1 hour recommended)
- [ ] Invalidate tokens after successful password reset
- [ ] Validate and sanitize all input data
- [ ] Log all password reset attempts for monitoring
- [ ] Use HTTPS for all password reset endpoints
- [ ] Implement proper password strength validation
- [ ] Prevent token reuse after successful reset
- [ ] Secure email content to prevent information disclosure

## Performance Considerations

- [ ] Use database indexes for token lookups
- [ ] Implement async email sending to avoid blocking requests
- [ ] Use connection pooling for database operations
- [ ] Cache frequently accessed configuration
- [ ] Implement efficient token cleanup for expired tokens

## Error Handling

### Standard Error Responses
```json
{
  "success": false,
  "message": "Error description",
  "error_code": "ERROR_CODE",
  "timestamp": "2024-01-15T10:00:00Z",
  "path": "/api/auth/forgot-password"
}
```

### Error Codes
- `INVALID_EMAIL`: Invalid email format
- `EMAIL_NOT_FOUND`: Email address not found
- `INVALID_TOKEN`: Invalid or expired reset token
- `TOKEN_EXPIRED`: Password reset token has expired
- `PASSWORD_MISMATCH`: New password and confirmation do not match
- `PASSWORD_TOO_WEAK`: Password does not meet strength requirements
- `RATE_LIMIT_EXCEEDED`: Too many password reset requests
- `TOKEN_ALREADY_USED`: Password reset token has already been used
- `VALIDATION_FAILED`: Request data validation failed

## Email Templates

### Password Reset Email Template
The password reset email should include:
- Clear subject line indicating password reset request
- User's name for personalization
- Secure reset link with token
- Expiration time information
- Security warnings about not sharing the link
- Contact information for support

### Email Content Requirements
- Professional and clear messaging
- Security warnings and best practices
- Mobile-friendly design
- Clear call-to-action button
- Fallback text for email clients that don't support HTML

## Related Issues

- Issue #001: User Registration System
- Issue #002: User Login Authentication
- Issue #005: Email Notification System

## Notes

- The frontend login forms have "Lost your password?" links that expect these API endpoints
- Password reset emails should include a link to the frontend reset page with the token
- All database field names must use snake_case convention as specified in the backend structure documentation
- The system should provide the same response for valid and invalid emails to prevent email enumeration
- Implement proper token cleanup to remove expired tokens from the database
- Consider implementing account lockout after multiple failed password reset attempts

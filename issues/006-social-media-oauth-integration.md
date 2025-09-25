# Issue #006: Social Media OAuth Integration

## Issue Details
- **Type**: Backend Development
- **Priority**: Medium
- **Complexity**: Complex
- **Estimated Time**: 3-4 days
- **Assignee**: Backend Developer
- **Labels**: backend, nestjs, api, database, authentication, oauth, social-media

## Description

Implement social media OAuth integration for the caautoline automotive marketplace. The system should handle user authentication and registration through Facebook and Google OAuth providers. This will allow users to sign up and log in using their social media accounts, providing a seamless authentication experience.

The frontend already has social media login buttons for Facebook and Google that expect OAuth redirect URLs and token handling. The backend needs to provide OAuth endpoints that can handle the complete OAuth flow including user profile creation, linking existing accounts, and JWT token generation.

## Acceptance Criteria

- [ ] Implement Facebook OAuth strategy with Passport.js
- [ ] Implement Google OAuth strategy with Passport.js
- [ ] Create OAuth callback endpoints for Facebook and Google
- [ ] Handle user profile creation from social media data
- [ ] Link social media accounts to existing email-based accounts
- [ ] Generate JWT tokens for social media authenticated users
- [ ] Store social media profile data and access tokens
- [ ] Handle OAuth error scenarios and edge cases
- [ ] Implement account linking/unlinking functionality
- [ ] Return consistent response format with traditional authentication
- [ ] Validate and sanitize social media user data
- [ ] Implement rate limiting for OAuth endpoints
- [ ] Log OAuth authentication attempts for security monitoring
- [ ] Create comprehensive unit and integration tests

## Technical Requirements

### Database Schema
Refer to the backend structure documentation for the complete database schema. The following tables are required:

- `user_social_links` table - For OAuth integration and social account linking
- `social_login_attempts` table - For tracking OAuth login attempts and security monitoring

### NestJS Implementation Structure

The implementation should follow NestJS best practices with proper module organization:
- Authentication module with OAuth strategies (Facebook, Google)
- Social authentication service for user creation and linking
- OAuth profile service for handling social media data
- Account linking service for managing multiple social providers
- Guards for OAuth rate limiting and authentication

## API Endpoints

### 1. Facebook OAuth Authentication
```
GET /api/auth/facebook
GET /api/auth/facebook/callback
```

**Flow:**
1. User clicks Facebook login button
2. Frontend redirects to `/api/auth/facebook`
3. User authorizes on Facebook
4. Facebook redirects to `/api/auth/facebook/callback`
5. Backend processes OAuth response and creates/links account
6. Returns JWT tokens to frontend

### 2. Google OAuth Authentication
```
GET /api/auth/google
GET /api/auth/google/callback
```

**Flow:**
1. User clicks Google login button
2. Frontend redirects to `/api/auth/google`
3. User authorizes on Google
4. Google redirects to `/api/auth/google/callback`
5. Backend processes OAuth response and creates/links account
6. Returns JWT tokens to frontend

### 3. Social Authentication Response
**Success Response (200/201):**
```json
{
  "success": true,
  "message": "Authentication successful",
  "data": {
    "user": {
      "id": 123,
      "email": "user@example.com",
      "first_name": "John",
      "last_name": "Doe",
      "role": "buyer",
      "status": "active",
      "email_verified": true,
      "profile_picture": "https://profile-picture-url.com",
      "social_providers": ["facebook", "google"]
    },
    "tokens": {
      "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "expires_in": 3600
    },
    "is_new_user": true,
    "provider": "facebook"
  }
}
```

### 4. Link Social Account
```
POST /api/auth/link-social
```

**Headers:**
```
Authorization: Bearer <access_token>
```

**Request Body:**
```json
{
  "provider": "facebook",
  "access_token": "facebook_access_token"
}
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Social account linked successfully",
  "data": {
    "linked_providers": ["facebook", "google"]
  }
}
```

### 5. Unlink Social Account
```
DELETE /api/auth/unlink-social/:provider
```

**Headers:**
```
Authorization: Bearer <access_token>
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Social account unlinked successfully",
  "data": {
    "linked_providers": ["google"]
  }
}
```

### 6. Get Linked Accounts
```
GET /api/auth/linked-accounts
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
    "linked_providers": ["facebook", "google"],
    "accounts": [
      {
        "provider": "facebook",
        "email": "user@example.com",
        "profile_picture": "https://facebook-profile-pic.com",
        "linked_at": "2024-01-15T10:00:00Z"
      },
      {
        "provider": "google",
        "email": "user@gmail.com",
        "profile_picture": "https://google-profile-pic.com",
        "linked_at": "2024-01-10T10:00:00Z"
      }
    ]
  }
}
```

## Dependencies

### Required Packages
```json
{
  "@nestjs/passport": "^10.0.0",
  "@nestjs/jwt": "^10.0.0",
  "passport": "^0.6.0",
  "passport-facebook": "^3.0.0",
  "passport-google-oauth20": "^2.0.0",
  "passport-jwt": "^4.0.0",
  "class-validator": "^0.14.0",
  "class-transformer": "^0.5.1",
  "@nestjs/throttler": "^5.0.0",
  "axios": "^1.6.0",
  "uuid": "^9.0.0"
}
```

### Environment Variables
```env
# OAuth Configuration
FACEBOOK_APP_ID=your_facebook_app_id
FACEBOOK_APP_SECRET=your_facebook_app_secret
FACEBOOK_CALLBACK_URL=http://localhost:3000/api/auth/facebook/callback
FACEBOOK_SCOPE=email,public_profile

GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_CALLBACK_URL=http://localhost:3000/api/auth/google/callback
GOOGLE_SCOPE=email,profile

# Frontend URLs
FRONTEND_URL=http://localhost:3000
FRONTEND_SUCCESS_REDIRECT=/dashboard
FRONTEND_ERROR_REDIRECT=/login?error=oauth_failed

# Security
OAUTH_STATE_SECRET=your_oauth_state_secret
OAUTH_RATE_LIMIT_TTL=3600
OAUTH_RATE_LIMIT_LIMIT=5
```

## OAuth Flow Implementation

### Facebook OAuth Strategy
The Facebook OAuth strategy should:
- Handle Facebook OAuth flow with proper scopes (email, public_profile)
- Validate Facebook profile data and extract required fields
- Handle cases where email is not provided by Facebook
- Integrate with social authentication service for user validation

### Google OAuth Strategy
The Google OAuth strategy should:
- Handle Google OAuth flow with proper scopes (email, profile)
- Validate Google profile data and extract required fields
- Handle cases where email is not provided by Google
- Integrate with social authentication service for user validation

## Testing Requirements

### Unit Tests
- [ ] Test Facebook OAuth strategy validation
- [ ] Test Google OAuth strategy validation
- [ ] Test social user creation and linking
- [ ] Test account linking/unlinking functionality
- [ ] Test OAuth error handling
- [ ] Test profile data validation and sanitization
- [ ] Test JWT token generation for social users
- [ ] Test rate limiting for OAuth endpoints

### Integration Tests
- [ ] Test complete Facebook OAuth flow
- [ ] Test complete Google OAuth flow
- [ ] Test account linking with existing email
- [ ] Test multiple provider linking
- [ ] Test OAuth callback error scenarios
- [ ] Test database transactions for social auth
- [ ] Test frontend redirect handling

### Security Tests
- [ ] Test OAuth state parameter validation
- [ ] Test access token validation
- [ ] Test profile data sanitization
- [ ] Test CSRF protection for OAuth flows
- [ ] Test rate limiting effectiveness
- [ ] Test unauthorized access attempts

## Documentation

### API Documentation
- [ ] Update OpenAPI/Swagger documentation
- [ ] Document OAuth flow diagrams
- [ ] Add example OAuth requests and responses
- [ ] Document error codes and messages
- [ ] Create OAuth setup guide for developers

### Code Documentation
- [ ] Add JSDoc comments to OAuth strategies
- [ ] Document social user interface
- [ ] Document account linking logic
- [ ] Create README for OAuth setup
- [ ] Document environment variable requirements

## Security Considerations

- [ ] Implement OAuth state parameter validation to prevent CSRF attacks
- [ ] Validate and sanitize all social media profile data
- [ ] Secure storage of access tokens and refresh tokens
- [ ] Implement rate limiting for OAuth endpoints (max 5 attempts per IP per hour)
- [ ] Log all OAuth attempts for security monitoring
- [ ] Validate OAuth provider responses and handle edge cases
- [ ] Implement proper error handling without exposing sensitive information
- [ ] Use HTTPS for all OAuth redirect URLs in production
- [ ] Implement token expiration and refresh handling
- [ ] Validate email ownership for account linking

## Performance Considerations

- [ ] Cache OAuth provider configurations
- [ ] Use database indexes for platform user ID lookups
- [ ] Implement async token refresh for expired tokens
- [ ] Use connection pooling for database operations
- [ ] Cache frequently accessed social profile data
- [ ] Implement bulk operations for social login tracking

## Error Handling

### Standard Error Responses
```json
{
  "success": false,
  "message": "OAuth authentication failed",
  "error_code": "OAUTH_FAILED",
  "timestamp": "2024-01-15T10:00:00Z",
  "path": "/api/auth/facebook/callback"
}
```

### Error Codes
- `OAUTH_FAILED`: OAuth authentication failed
- `OAUTH_EMAIL_REQUIRED`: Email not provided by OAuth provider
- `OAUTH_ACCOUNT_EXISTS`: Account already exists with different provider
- `OAUTH_LINKING_FAILED`: Failed to link social account
- `OAUTH_UNLINKING_FAILED`: Failed to unlink social account
- `OAUTH_RATE_LIMIT_EXCEEDED`: Too many OAuth attempts
- `OAUTH_INVALID_STATE`: Invalid OAuth state parameter
- `OAUTH_TOKEN_EXPIRED`: OAuth access token expired

## Frontend Integration

### Required Frontend Changes
The frontend social media buttons should redirect to:
- Facebook: `GET /api/auth/facebook`
- Google: `GET /api/auth/google`

### Success/Error Handling
- **Success**: Redirect to frontend dashboard with JWT tokens
- **Error**: Redirect to login page with error message
- **Account Linking**: Show confirmation dialog before linking accounts

## Related Issues

- Issue #001: User Registration System
- Issue #002: User Login Authentication
- Issue #003: Password Reset System
- Issue #004: User Profile Management

## Notes

- The frontend social media buttons are already implemented and expect these OAuth endpoints
- OAuth callbacks should redirect to the frontend with appropriate success/error states
- Account linking should be seamless and not require additional user input
- Social media profile pictures should be stored and served through the application
- The system should handle users who have accounts with multiple social providers
- All database field names must use snake_case convention as specified in the backend structure documentation
- Implement proper logging for OAuth flows to help with debugging and security monitoring

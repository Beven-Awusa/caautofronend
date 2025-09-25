# Issues - Backend Development Tasks

This folder contains detailed issues for implementing the NestJS backend functionality for the caautoline automotive marketplace platform.

## Issue Structure

Each issue follows a standardized format including:
- **Title**: Clear, descriptive title
- **Type**: Backend Development
- **Priority**: High/Medium/Low
- **Complexity**: Simple/Medium/Complex
- **Estimated Time**: Development time estimate
- **Description**: Detailed problem statement
- **Acceptance Criteria**: Clear definition of done
- **Technical Requirements**: Specific implementation details
- **API Endpoints**: Required endpoints with request/response schemas
- **Database Schema**: Required database tables and relationships
- **Dependencies**: Prerequisites and related issues
- **Testing Requirements**: Testing scenarios and validation
- **Documentation**: Required documentation updates

## Current Issues

### Authentication & User Management
- [Issue #001: User Registration System](./001-user-registration-system.md)
- [Issue #002: User Login Authentication](./002-user-login-authentication.md)
- [Issue #003: Password Reset System](./003-password-reset-system.md)
- [Issue #006: Social Media OAuth Integration](./006-social-media-oauth-integration.md)

### Listings Management
- [Issue #008: Car Listings CRUD, Filter and Search System](./008-car-listings-crud-filter-search.md)
- [Issue #009: Accessories CRUD, Filter and Search System](./009-accessories-crud-filter-search.md)
- [Issue #010: Car Makes, Models, and Features CRUD System](./010-car-makes-models-features-crud.md)

## Issue Template

When creating new issues, use the following template:

```markdown
# Issue #[NUMBER]: [TITLE]

## Issue Details
- **Type**: Backend Development
- **Priority**: [High/Medium/Low]
- **Complexity**: [Simple/Medium/Complex]
- **Estimated Time**: [X hours/days]
- **Assignee**: [Developer Name]
- **Labels**: [backend, nestjs, api, database]

## Description
[Detailed description of the issue]

## Acceptance Criteria
- [ ] [Specific criteria 1]
- [ ] [Specific criteria 2]
- [ ] [Specific criteria 3]

## Technical Requirements
[Technical implementation details]

## API Endpoints
[Required API endpoints]

## Database Schema
[Required database changes]

## Dependencies
[Prerequisites and related issues]

## Testing Requirements
[Testing scenarios and validation]

## Documentation
[Required documentation updates]
```

## Backend Architecture Overview

The backend follows these principles:
- **Framework**: NestJS with TypeScript
- **Database**: MySQL with TypeORM
- **Authentication**: JWT with refresh tokens
- **Validation**: class-validator and class-transformer
- **Security**: bcrypt for password hashing, helmet for security headers
- **API Design**: RESTful APIs with consistent response formats
- **Error Handling**: Global exception filters
- **Logging**: Structured logging with Winston
- **Testing**: Unit and integration tests with Jest

## Database Naming Convention

All database fields follow snake_case naming convention as specified in the backend structure documentation.

# Car Listings CRUD, Filter and Search System

## Branch Name
`feature/car-listings-crud`

## Description

Implement a comprehensive Car Listings CRUD, Filter, and Search system for the caautoline automotive marketplace. This system should handle car listings management including creation, retrieval, updating, and deletion of listings, along with advanced filtering and search capabilities.

The system needs to support:
- Full CRUD operations for car listings
- Advanced filtering by multiple criteria (make, model, year, price, condition, etc.)
- Full-text search across listing titles and descriptions
- Sorting by various fields (price, year, date, popularity)
- Listing status management
- Media management for images and videos
- Analytics tracking for listing views

Based on the frontend analysis, multiple pages require these backend API endpoints including home pages, listing pages, dashboard pages, and single listing pages.

## Acceptance Criteria

- [ ] Create API endpoints for full CRUD operations on car listings
- [ ] Implement advanced filtering by make, model, year, price, condition, fuel type, transmission, etc.
- [ ] Implement full-text search across listing titles and descriptions
- [ ] Implement sorting by price, year, date, popularity, and other relevant fields
- [ ] Implement listing status management (active, pending, sold, draft)
- [ ] Handle media uploads for listing images and videos
- [ ] Implement listing analytics tracking (views count, favorites count)
- [ ] Implement pagination for listing results
- [ ] Return appropriate success/error responses with proper HTTP status codes
- [ ] Validate all input data using class-validator decorators
- [ ] Create comprehensive unit and integration tests

## Technical Requirements

### Database Schema
Refer to the backend structure documentation for the complete database schema. The following tables are required:

- `car_listings` table - Primary car listings storage
- `car_makes` table - Car manufacturer data
- `car_models` table - Car model data
- `car_features` table - Car feature data
- `car_listing_features` table - Junction table for listing features
- `users` table - Application users

### NestJS Implementation Structure

The implementation should follow NestJS best practices with proper module organization:
- Listings module with services for car listings
- Search module with search and filter services
- Media module for handling image/video uploads
- Analytics module for tracking views and favorites
- Common utilities for validation and error handling

## API Endpoints

### 1. Get All Listings with Filters
```
GET /api/listings
```

**Query Parameters:**
- `page` (integer) - Page number for pagination (default: 1)
- `limit` (integer) - Number of items per page (default: 10, max: 100)
- `sort` (string) - Sort field (price, year, created_at, views_count)
- `order` (string) - Sort order (asc, desc)
- `make_id` (integer) - Filter by car make
- `model_id` (integer) - Filter by car model
- `year_min` (integer) - Minimum year
- `year_max` (integer) - Maximum year
- `price_min` (number) - Minimum price
- `price_max` (number) - Maximum price
- `condition` (string) - Filter by condition (new, used, certified)
- `fuel_type` (string) - Filter by fuel type
- `transmission` (string) - Filter by transmission type
- `featured` (boolean) - Filter featured listings only
- `status` (string) - Filter by status (active, pending, sold, draft)
- `search` (string) - Full-text search query

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "listings": [
      {
        "id": 123,
        "user_id": 456,
        "title": "2023 Volvo XC90 - Premium",
        "description": "Beautiful 2023 Volvo XC90 in excellent condition",
        "make": {
          "id": 1,
          "name": "Volvo"
        },
        "model": {
          "id": 15,
          "name": "XC90"
        },
        "year": 2023,
        "condition": "used",
        "price": 45000,
        "mileage": 12000,
        "fuel_type": "gasoline",
        "transmission": "automatic",
        "drive_type": "awd",
        "primary_image": "https://example.com/images/listing1.jpg",
        "gallery_images": [
          "https://example.com/images/listing1-1.jpg",
          "https://example.com/images/listing1-2.jpg"
        ],
        "status": "active",
        "featured": true,
        "views_count": 125,
        "favorites_count": 24,
        "location": "New York, NY",
        "created_at": "2024-01-15T10:00:00Z",
        "updated_at": "2024-01-20T14:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 1250,
      "total_pages": 125
    }
  }
}
```

### 2. Get Single Listing
```
GET /api/listings/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": 123,
    "user_id": 456,
    "title": "2023 Volvo XC90 - Premium",
    "description": "Beautiful 2023 Volvo XC90 in excellent condition",
    "make": {
      "id": 1,
      "name": "Volvo"
    },
    "model": {
      "id": 15,
      "name": "XC90"
    },
    "year": 2023,
    "condition": "used",
    "price": 45000,
    "mileage": 12000,
    "fuel_type": "gasoline",
    "transmission": "automatic",
    "drive_type": "awd",
    "engine_size": 2.0,
    "cylinders": 4,
    "doors": 4,
    "seats": 7,
    "color": "Black",
    "interior_color": "Leather Brown",
    "vin": "1234567890ABCDEFG",
    "primary_image": "https://example.com/images/listing1.jpg",
    "gallery_images": [
      "https://example.com/images/listing1-1.jpg",
      "https://example.com/images/listing1-2.jpg"
    ],
    "videos": [
      "https://youtube.com/watch?v=abc123"
    ],
    "features": [
      {
        "id": 1,
        "name": "Leather Seats",
        "category": "Interior"
      },
      {
        "id": 2,
        "name": "Navigation System",
        "category": "Technology"
      }
    ],
    "status": "active",
    "featured": true,
    "views_count": 125,
    "favorites_count": 24,
    "location": "New York, NY",
    "latitude": 40.7128,
    "longitude": -74.0060,
    "contact_phone": "+1-555-123-4567",
    "contact_email": "seller@example.com",
    "created_at": "2024-01-15T10:00:00Z",
    "updated_at": "2024-01-20T14:30:00Z"
  }
}
```

### 3. Create New Listing
```
POST /api/listings
```

**Headers:**
```
Authorization: Bearer <access_token>
Content-Type: multipart/form-data
```

**Form Data:**
- `title` (string, required)
- `description` (string)
- `make_id` (integer, required)
- `model_id` (integer, required)
- `year` (integer, required)
- `condition` (string, required) - new, used, certified
- `price` (number, required)
- `mileage` (integer)
- `fuel_type` (string, required) - gasoline, diesel, electric, hybrid
- `transmission` (string, required) - manual, automatic, semi-automatic
- `drive_type` (string, required) - fwd, rwd, awd, 4wd
- `engine_size` (number)
- `cylinders` (integer)
- `doors` (integer)
- `seats` (integer)
- `color` (string)
- `interior_color` (string)
- `vin` (string)
- `status` (string) - active, pending, sold, draft (default: pending)
- `featured` (boolean) - default: false
- `location` (string)
- `latitude` (number)
- `longitude` (number)
- `contact_phone` (string)
- `contact_email` (string)
- `primary_image` (file)
- `gallery_images` (files, multiple)
- `videos` (array of URLs)
- `feature_ids` (array of integers) - selected feature IDs

**Success Response (201):**
```json
{
  "success": true,
  "message": "Listing created successfully",
  "data": {
    "id": 123,
    "title": "2023 Volvo XC90 - Premium",
    "status": "pending",
    "created_at": "2024-01-15T10:00:00Z"
  }
}
```

### 4. Update Listing
```
PUT /api/listings/:id
```

**Headers:**
```
Authorization: Bearer <access_token>
Content-Type: multipart/form-data
```

**Form Data:**
(Same fields as create, but only include fields to update)

**Success Response (200):**
```json
{
  "success": true,
  "message": "Listing updated successfully",
  "data": {
    "id": 123,
    "title": "2023 Volvo XC90 - Premium Plus",
    "status": "active",
    "updated_at": "2024-01-20T14:30:00Z"
  }
}
```

### 5. Delete Listing
```
DELETE /api/listings/:id
```

**Headers:**
```
Authorization: Bearer <access_token>
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Listing deleted successfully"
}
```

### 6. Upload Listing Media
```
POST /api/listings/:id/upload
```

**Headers:**
```
Authorization: Bearer <access_token>
Content-Type: multipart/form-data
```

**Form Data:**
- `images` (files, multiple) - Images to upload
- `videos` (files, multiple) - Videos to upload
- `set_primary` (boolean) - Whether to set the first image as primary

**Success Response (200):**
```json
{
  "success": true,
  "message": "Media uploaded successfully",
  "data": {
    "primary_image": "https://example.com/images/listing1-new.jpg",
    "gallery_images": [
      "https://example.com/images/listing1-new.jpg",
      "https://example.com/images/listing1-new2.jpg"
    ]
  }
}
```

### 7. Advanced Search
```
GET /api/listings/search
```

**Query Parameters:**
- `query` (string, required) - Search query
- `page` (integer) - Page number for pagination (default: 1)
- `limit` (integer) - Number of items per page (default: 10, max: 100)

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "id": 123,
        "title": "2023 Volvo XC90 - Premium",
        "description": "Beautiful 2023 Volvo XC90 in excellent condition",
        "price": 45000,
        "primary_image": "https://example.com/images/listing1.jpg",
        "type": "car"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 42,
      "total_pages": 5
    },
    "query": "Volvo XC90"
  }
}
```



## Testing Requirements

### Unit Tests
- [ ] Test listing creation with valid data
- [ ] Test listing update functionality
- [ ] Test listing deletion functionality
- [ ] Test filtering by various criteria
- [ ] Test search functionality
- [ ] Test sorting functionality
- [ ] Test pagination
- [ ] Test media upload and processing
- [ ] Test validation for all input fields
- [ ] Test error handling for invalid inputs

### Integration Tests
- [ ] Test complete listing CRUD flow
- [ ] Test filtering with multiple criteria
- [ ] Test search with various query types
- [ ] Test database transactions and rollbacks
- [ ] Test API endpoint responses and status codes
- [ ] Test media upload and storage
- [ ] Test authorization for protected endpoints

### Test Data
Create test fixtures for:
- Valid car listing data
- Invalid listing data
- Filter test scenarios
- Search test scenarios
- Media upload test files

## Documentation

### API Documentation
- [ ] Update OpenAPI/Swagger documentation
- [ ] Document all request/response schemas
- [ ] Add example requests and responses
- [ ] Document error codes and messages
- [ ] Document query parameters for filtering and search

### Code Documentation
- [ ] Add JSDoc comments to all public methods
- [ ] Document complex business logic for filtering
- [ ] Document search algorithm implementation
- [ ] Add inline comments for non-obvious code
- [ ] Create README for listings module setup

## Security Considerations

- [ ] Validate and sanitize all input data
- [ ] Implement proper authorization checks for CRUD operations
- [ ] Validate file uploads and limit file types
- [ ] Use HTTPS in production
- [ ] Implement proper error handling without exposing sensitive information
- [ ] Validate user ownership for update/delete operations

## Performance Considerations

- [ ] Use database indexes for frequently queried fields
- [ ] Use connection pooling for database connections
- [ ] Implement efficient pagination for large datasets
- [ ] Optimize full-text search queries
- [ ] Use database query optimization for complex filters

## Error Handling

### Standard Error Responses
```json
{
  "success": false,
  "message": "Error description",
  "error_code": "ERROR_CODE",
  "timestamp": "2024-01-15T10:00:00Z",
  "path": "/api/listings"
}
```

### Error Codes
- `LISTING_NOT_FOUND`: Listing with specified ID not found
- `UNAUTHORIZED_ACCESS`: User not authorized to perform operation
- `VALIDATION_FAILED`: Request data validation failed
- `UPLOAD_FAILED`: File upload failed
- `INVALID_FILTER`: Invalid filter parameters provided
- `SEARCH_ERROR`: Search operation failed
- `DATABASE_ERROR`: Database operation failed

## Related Issues

- Issue #001: User Registration System
- Issue #002: User Login Authentication
- Issue #004: User Profile Management
- Issue #005: Media Management System

## Notes

- The frontend pages expect these specific API endpoints and response formats
- All database field names must use snake_case convention as specified in the backend structure documentation
- Implement proper validation for all input data including file uploads
- Consider implementing soft deletes for listings to maintain data integrity
- Implement proper error handling for media processing and storage
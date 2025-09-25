# Issue #009: Accessories CRUD, Filter and Search System

## Branch Name
`feature/accessories-crud`

## Issue Details
- **Type**: Backend Development
- **Priority**: High
- **Complexity**: Complex
- **Estimated Time**: 3-4 days
- **Assignee**: Backend Developer
- **Labels**: backend, nestjs, api, database, accessories, search, filter

## Description

Implement a comprehensive Accessories CRUD, Filter, and Search system for the caautoline automotive marketplace. This system should handle accessory listings management including creation, retrieval, updating, and deletion of accessories, along with advanced filtering and search capabilities.

The system needs to support:
- Full CRUD operations for accessory listings
- Advanced filtering by multiple criteria (category, brand, price, condition, etc.)
- Full-text search across accessory titles and descriptions
- Sorting by various fields (price, date, popularity)
- Accessory status management
- Media management for images and videos
- Analytics tracking for accessory views
- Compatibility information management
- Specification management

Based on the frontend analysis, multiple pages require these backend API endpoints including accessories pages, shop pages, and single accessory pages.

## Acceptance Criteria

- [ ] Create API endpoints for full CRUD operations on accessory listings
- [ ] Implement advanced filtering by category, brand, price, condition, etc.
- [ ] Implement full-text search across accessory titles and descriptions
- [ ] Implement sorting by price, date, popularity, and other relevant fields
- [ ] Implement accessory status management (active, pending, sold, draft)
- [ ] Handle media uploads for accessory images and videos
- [ ] Implement accessory analytics tracking (views count, favorites count)
- [ ] Create API endpoints for accessory categories data with full CRUD operations
- [ ] Create API endpoints for accessory brands data with full CRUD operations
- [ ] Implement pagination for accessory results
- [ ] Return appropriate success/error responses with proper HTTP status codes
- [ ] Validate all input data using class-validator decorators
- [ ] Create comprehensive unit and integration tests

## Technical Requirements

### Database Schema
Refer to the backend structure documentation for the complete database schema. The following tables are required:

- `accessories` table - Primary accessories listings storage
- `accessories_categories` table - Accessory category data
- `accessories_brands` table - Accessory brand data
- `accessories_specifications` table - Accessory specifications
- `accessories_compatibility` table - Accessory compatibility data
- `user_views` table - For tracking accessory views
- `user_favorites` table - For tracking favorites

### NestJS Implementation Structure

The implementation should follow NestJS best practices with proper module organization:
- Accessories module with services for accessory listings
- Search module with search and filter services
- Media module for handling image/video uploads
- Analytics module for tracking views and favorites
- Common utilities for validation and error handling

## API Endpoints

### 1. Get All Accessories with Filters
```
GET /api/accessories
```

**Query Parameters:**
- `page` (integer) - Page number for pagination (default: 1)
- `limit` (integer) - Number of items per page (default: 10, max: 100)
- `sort` (string) - Sort field (price, created_at, views_count)
- `order` (string) - Sort order (asc, desc)
- `category_id` (integer) - Filter by category
- `brand_id` (integer) - Filter by brand
- `condition` (string) - Filter by condition (new, used, refurbished)
- `price_min` (number) - Minimum price
- `price_max` (number) - Maximum price
- `featured` (boolean) - Filter featured accessories only
- `status` (string) - Filter by status (active, pending, sold, draft)
- `search` (string) - Full-text search query

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "accessories": [
      {
        "id": 123,
        "user_id": 456,
        "title": "Michelin Pilot Sport 4",
        "description": "High-performance summer tire",
        "category": {
          "id": 1,
          "name": "Tires & Wheels"
        },
        "brand": {
          "id": 1,
          "name": "Michelin"
        },
        "price": 150.00,
        "condition": "new",
        "primary_image": "https://example.com/images/accessory1.jpg",
        "status": "active",
        "featured": true,
        "views_count": 89,
        "favorites_count": 12,
        "created_at": "2024-01-15T10:00:00Z",
        "updated_at": "2024-01-20T14:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 450,
      "total_pages": 45
    }
  }
}
```

### 2. Get Single Accessory
```
GET /api/accessories/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": {
    "id": 123,
    "user_id": 456,
    "title": "Michelin Pilot Sport 4",
    "description": "High-performance summer tire",
    "category": {
      "id": 1,
      "name": "Tires & Wheels"
    },
    "brand": {
      "id": 1,
      "name": "Michelin"
    },
    "sku": "MP4-22545R17",
    "price": 150.00,
    "sale_price": 135.00,
    "condition": "new",
    "stock_quantity": 24,
    "manage_stock": true,
    "stock_status": "instock",
    "weight": 9.5,
    "dimensions": "225/45R17",
    "compatibility_notes": "Fits BMW 3 Series 2015-2023",
    "warranty_info": "2 years manufacturer warranty",
    "primary_image": "https://example.com/images/accessory1.jpg",
    "gallery_images": [
      "https://example.com/images/accessory1-1.jpg",
      "https://example.com/images/accessory1-2.jpg"
    ],
    "videos": [
      "https://youtube.com/watch?v=def456"
    ],
    "specifications": [
      {
        "name": "Tire Size",
        "value": "225/45R17"
      },
      {
        "name": "Load Index",
        "value": "91"
      }
    ],
    "status": "active",
    "featured": true,
    "views_count": 89,
    "favorites_count": 12,
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

### 3. Create New Accessory
```
POST /api/accessories
```

**Headers:**
```
Authorization: Bearer <access_token>
Content-Type: multipart/form-data
```

**Form Data:**
- `title` (string, required)
- `description` (string)
- `category_id` (integer, required)
- `brand_id` (integer)
- `sku` (string)
- `price` (number, required)
- `sale_price` (number)
- `condition` (string, required) - new, used, refurbished
- `stock_quantity` (integer)
- `manage_stock` (boolean) - default: true
- `stock_status` (string) - instock, outofstock, onbackorder (default: instock)
- `weight` (number)
- `dimensions` (string)
- `compatibility_notes` (string)
- `warranty_info` (string)
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

**Success Response (201):**
```json
{
  "success": true,
  "message": "Accessory created successfully",
  "data": {
    "id": 123,
    "title": "Michelin Pilot Sport 4",
    "status": "pending",
    "created_at": "2024-01-15T10:00:00Z"
  }
}
```

### 4. Update Accessory
```
PUT /api/accessories/:id
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
  "message": "Accessory updated successfully",
  "data": {
    "id": 123,
    "title": "Michelin Pilot Sport 4 - Special Edition",
    "status": "active",
    "updated_at": "2024-01-20T14:30:00Z"
  }
}
```

### 5. Delete Accessory
```
DELETE /api/accessories/:id
```

**Headers:**
```
Authorization: Bearer <access_token>
```

**Success Response (200):**
```json
{
  "success": true,
  "message": "Accessory deleted successfully"
}
```

### 6. Upload Accessory Media
```
POST /api/accessories/:id/upload
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
    "primary_image": "https://example.com/images/accessory1-new.jpg",
    "gallery_images": [
      "https://example.com/images/accessory1-new.jpg",
      "https://example.com/images/accessory1-new2.jpg"
    ]
  }
}
```


### 8. Get Accessory Brands
```
GET /api/accessories/brands
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "name": "Michelin",
      "logo": "https://example.com/logos/michelin.png"
    },
    {
      "id": 2,
      "name": "Bosch",
      "logo": "https://example.com/logos/bosch.png"
    }
  ]
}
```

### 9. Get Accessory Compatibility Info
```
GET /api/accessories/compatibility/:id
```

**Success Response (200):**
```json
{
  "success": true,
  "data": [
    {
      "make": {
        "id": 1,
        "name": "BMW"
      },
      "model": {
        "id": 15,
        "name": "3 Series"
      },
      "year_start": 2015,
      "year_end": 2023,
      "notes": "Perfect fit for all trim levels"
    }
  ]
}
```

## Dependencies

### Required Packages
```json
{
  "@nestjs/typeorm": "^10.0.0",
  "typeorm": "^0.3.0",
  "class-validator": "^0.14.0",
  "class-transformer": "^0.5.1",
  "multer": "^1.4.5-lts.1",
  "@nestjs/platform-express": "^10.0.0",
  "sharp": "^0.32.0",
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

# File Upload
UPLOAD_PATH=./uploads
MAX_FILE_SIZE=10485760
MAX_FILES=10

# Pagination
DEFAULT_PAGE_SIZE=10
MAX_PAGE_SIZE=100
```

## Testing Requirements

### Unit Tests
- [ ] Test accessory creation with valid data
- [ ] Test accessory update functionality
- [ ] Test accessory deletion functionality
- [ ] Test filtering by various criteria
- [ ] Test search functionality
- [ ] Test sorting functionality
- [ ] Test pagination
- [ ] Test media upload and processing
- [ ] Test validation for all input fields
- [ ] Test error handling for invalid inputs
- [ ] Test accessory category creation with valid data
- [ ] Test accessory brand creation with valid data

### Integration Tests
- [ ] Test complete accessory CRUD flow
- [ ] Test filtering with multiple criteria
- [ ] Test search with various query types
- [ ] Test database transactions and rollbacks
- [ ] Test API endpoint responses and status codes
- [ ] Test media upload and storage
- [ ] Test authorization for protected endpoints
- [ ] Test complete accessory category CRUD flow
- [ ] Test complete accessory brand CRUD flow

### Test Data
Create test fixtures for:
- Valid accessory listing data
- Invalid accessory data
- Filter test scenarios
- Search test scenarios
- Media upload test files
- Valid accessory category data
- Valid accessory brand data
- Valid accessory compatibility data

## Documentation

### API Documentation
- [ ] Update OpenAPI/Swagger documentation
- [ ] Document all request/response schemas
- [ ] Add example requests and responses
- [ ] Document error codes and messages
- [ ] Document query parameters for filtering and search
- [ ] Document all accessories API endpoints
- [ ] Document accessories category and brand endpoints

### Code Documentation
- [ ] Add JSDoc comments to all public methods
- [ ] Document complex business logic for filtering
- [ ] Document search algorithm implementation
- [ ] Add inline comments for non-obvious code
- [ ] Create README for accessories module setup

## Security Considerations

- [ ] Validate and sanitize all input data
- [ ] Implement proper authorization checks for CRUD operations
- [ ] Validate file uploads and limit file types
- [ ] Use HTTPS in production
- [ ] Implement proper error handling without exposing sensitive information
- [ ] Validate user ownership for update/delete operations

## Performance Considerations

- [ ] Use database indexes for frequently queried fields
- [ ] Implement caching for filter data (categories, brands)
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
  "path": "/api/accessories"
}
```

### Error Codes
- `ACCESSORY_NOT_FOUND`: Accessory with specified ID not found
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
- Consider implementing soft deletes for accessories to maintain data integrity
- Implement proper error handling for media processing and storage
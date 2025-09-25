# Offer Management System

## Branch Name
`feature/offer-management`

## Description

Implement a complete Offer Management System for the caautoline automotive marketplace. This feature will enable buyers to make offers on vehicle listings and sellers to manage these offers effectively. The system will handle the entire lifecycle of an offer from creation to resolution.

The system needs to support:
- Making offers on car listings with custom amounts
- Managing offers (accept, reject, withdraw)
- Viewing offers by listing and by user
- Notifications for offer updates
- Validity period management
- Offer analytics and reporting

This functionality will be integrated into listing detail pages and the user dashboard.

## Acceptance Criteria

- [ ] Create offer management API endpoints
- [ ] Implement offer status management (pending, accepted, rejected, expired, withdrawn)
- [ ] Add offer amount validation with configurable rules
- [ ] Set up offer validity period management
- [ ] Integrate with notification system for status changes
- [ ] Implement authorization checks for buyers and sellers
- [ ] Add rate limiting for offer creation
- [ ] Create unit and integration tests
- [ ] Add proper error handling
- [ ] Document all API endpoints

## Technical Requirements

### Database Schema
Refer to the backend structure documentation for the complete database schema. The following tables are required:

- `offers` table - Primary offer storage
- `car_listings` table - Car listings data
- `users` table - Application users
- `notifications` table - Notification storage

### NestJS Implementation Structure

The implementation should follow NestJS best practices with proper module organization:

```typescript
// offers.module.ts
@Module({
  imports: [
    TypeOrmModule.forFeature([Offer, CarListing, User, Notification]),
    NotificationsModule,
  ],
  controllers: [OffersController],
  providers: [OffersService, OffersRepository],
})
export class OffersModule {}

// offers.entity.ts
@Entity('offers')
export class Offer {
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    listing_id BIGINT NOT NULL,
    buyer_id BIGINT NOT NULL,
    seller_id BIGINT NOT NULL,
    amount DECIMAL(12,2) NOT NULL,
    message TEXT,
    status ENUM('pending', 'accepted', 'rejected', 'expired', 'withdrawn') DEFAULT 'pending',
    valid_until TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE CASCADE,
    FOREIGN KEY (buyer_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (seller_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_listing_id (listing_id),
    INDEX idx_buyer_id (buyer_id),
    INDEX idx_seller_id (seller_id),
    INDEX idx_status (status),
    INDEX idx_valid_until (valid_until)
);
```

### API Endpoints

#### Submit Offer
- **Endpoint**: `POST /api/offers`
- **Auth**: Required
- **Request Body**:
```json
{
  "listingId": "string",
  "amount": number,
  "message": "string",
  "validUntil": "date" // optional
}
```

#### Get Offers for Listing
- **Endpoint**: `GET /api/offers/listing/:id`
- **Auth**: Required (seller only)
- **Response**:
```json
{
  "offers": [
    {
      "id": "string",
      "amount": number,
      "status": "string",
      "buyerName": "string",
      "message": "string",
      "createdAt": "date"
    }
  ]
}
```

#### Get User's Offers
- **Endpoint**: `GET /api/offers/user/:id`
- **Auth**: Required
- **Query Params**: type (made|received), status, sort

#### Update Offer Status
- **Endpoint**: `PUT /api/offers/:id`
- **Auth**: Required (seller only)
- **Request Body**:
```json
{
  "status": "accepted|rejected",
  "message": "string"
}
```

#### Withdraw Offer
- **Endpoint**: `DELETE /api/offers/:id`
- **Auth**: Required (buyer only)
- **Conditions**: Only pending offers

### API Endpoints

#### Offer Management Endpoints

1. Submit Offer
```typescript
@Post('/offers')
@UseGuards(AuthGuard)
async createOffer(@Body() dto: CreateOfferDto): Promise<OfferResponse> {
  return this.offersService.createOffer(dto);
}
```

2. Get Offers for Listing
```typescript
@Get('/offers/listing/:id')
@UseGuards(AuthGuard)
async getListingOffers(
  @Param('id') listingId: string,
  @Query() query: GetOffersQueryDto
): Promise<OffersListResponse> {
  return this.offersService.getListingOffers(listingId, query);
}
```

3. Get User's Offers
```typescript
@Get('/offers/user/:id')
@UseGuards(AuthGuard)
async getUserOffers(
  @Param('id') userId: string,
  @Query() query: GetOffersQueryDto
): Promise<OffersListResponse> {
  return this.offersService.getUserOffers(userId, query);
}
```

4. Update Offer Status
```typescript
@Put('/offers/:id')
@UseGuards(AuthGuard)
async updateOfferStatus(
  @Param('id') offerId: string,
  @Body() dto: UpdateOfferStatusDto
): Promise<OfferResponse> {
  return this.offersService.updateOfferStatus(offerId, dto);
}
```

### DTOs and Interfaces

```typescript
// create-offer.dto.ts
export class CreateOfferDto {
  @IsNumber()
  @Min(0)
  amount: number;

  @IsString()
  @IsNotEmpty()
  listingId: string;

  @IsOptional()
  @IsString()
  @MaxLength(1000)
  message?: string;

  @IsOptional()
  @IsDate()
  validUntil?: Date;
}

// update-offer-status.dto.ts
export class UpdateOfferStatusDto {
  @IsEnum(OfferStatus)
  status: OfferStatus;

  @IsOptional()
  @IsString()
  @MaxLength(1000)
  message?: string;
}

// offer-response.interface.ts
export interface OfferResponse {
  id: string;
  listingId: string;
  buyerId: string;
  sellerId: string;
  amount: number;
  status: OfferStatus;
  message?: string;
  validUntil?: Date;
  createdAt: Date;
  updatedAt: Date;
}
```

### Service Layer Implementation
- Price input with validation
- Optional message field
- Validity period selector
- Terms and conditions
- Submit button with loading state

#### 2. Offers Management Dashboard
- Separate tabs for offers made/received
- Status filters (pending, accepted, rejected, expired)
- Sort options (date, amount)
- Action buttons based on status
- Pagination

#### 3. Offer Card Component
- Display offer details
- Status badge
- Timer for validity
- Action buttons based on role/status
- Message thread

### Notifications

1. **Email Notifications**
   - New offer received
   - Offer status change
   - Offer expiring soon
   - Offer withdrawn

2. **In-App Notifications**
   - Real-time offer updates
   - Status changes
   - New messages

### Validation Rules

1. **Offer Amount**
   - Must be numeric and positive
   - Optional min/max percentage of listing price
   - Maximum decimal places: 2

2. **Validity Period**
   - Minimum: 24 hours
   - Maximum: 7 days
   - Default: 48 hours

3. **Message**
   - Maximum length: 1000 characters
   - No HTML allowed
   - Optional

### Security Considerations

1. **Authorization**
   - Only authenticated users can make offers
   - Sellers can only manage their own listings' offers
   - Buyers can only manage their own offers

2. **Rate Limiting**
   - Maximum 5 active offers per user per listing
   - Maximum 50 offers per day per user

3. **Data Validation**
   - Input sanitization
   - CSRF protection
   - Request body size limits

## Implementation Phases

### Phase 1: Core Functionality
1. Database schema implementation
2. Basic API endpoints
3. Make offer functionality
4. Offer management for sellers
5. Basic email notifications

### Phase 2: Enhanced Features
1. In-app notifications
2. Counter-offer functionality
3. Message thread system
4. Advanced filtering and sorting
5. Analytics dashboard

### Phase 3: Optimization
1. Performance improvements
2. Real-time updates
3. Mobile responsiveness
4. Advanced analytics
5. Reporting features

## Testing Requirements

1. **Unit Tests**
   - Offer creation validation
   - Status updates
   - Authorization checks

2. **Integration Tests**
   - API endpoints
   - Notification system
   - Database transactions

3. **End-to-End Tests**
   - Complete offer workflow
   - User interactions
   - Edge cases

## Documentation Requirements

1. **API Documentation**
   - Endpoint specifications
   - Request/response examples
   - Authentication requirements

2. **User Documentation**
   - How to make offers
   - Managing offers
   - Notifications setup

3. **Technical Documentation**
   - System architecture
   - Database schema
   - Integration points

## Success Metrics

1. **User Engagement**
   - Number of offers made
   - Offer acceptance rate
   - Average response time

2. **System Performance**
   - API response times
   - Database query performance
   - Notification delivery time

3. **Business Impact**
   - Increase in successful transactions
   - User satisfaction ratings
   - Reduction in manual price negotiations
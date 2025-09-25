# caautoline Backend Structure & Database Design

## Complete Functionality Analysis

Here are all the functionalities that need backend support:

## 1. User Management & Authentication

### Features:
- User registration and login
- Password reset functionality
- User profile management
- Social media integration
- Account verification
- User roles (buyer, seller, admin)

### Data Requirements:
- User credentials and personal information
- Profile pictures and social links
- Account status and verification
- Login history and security

## 2. Car Listings Management

### Features:
- Add new car listings
- Edit existing listings
- Delete listings
- Featured listings
- Listing status management (active, pending, sold)
- Multiple listing views (grid, list, map)
- Advanced filtering and search
- Listing analytics and views

### Data Requirements:
- Complete car specifications
- Multiple images and videos
- Location and contact information
- Pricing and availability
- Listing metadata and statistics

## 3. Search & Filtering System

### Features:
- Advanced search with multiple criteria
- Filter by make, model, year, price, location
- Sort by price, date, popularity
- Saved searches
- Search history
- Auto-complete suggestions

### Data Requirements:
- Search queries and filters
- User search history
- Popular searches and trends
- Auto-complete data

## 4. User Dashboard & Analytics

### Features:
- Personal dashboard with statistics
- Listing performance metrics
- Message management
- Favorites tracking
- Activity history
- Revenue tracking (for sellers)

### Data Requirements:
- User statistics and metrics
- Dashboard widgets data
- Activity logs
- Performance analytics

## 5. Favorites & Wishlist

### Features:
- Save favorite cars
- Create multiple wishlists
- Share wishlists
- Price alerts for favorites
- Recently viewed items

### Data Requirements:
- User favorites relationships
- Wishlist management
- View history
- Price change tracking

## 6. Car Comparison System

### Features:
- Compare multiple cars side by side
- Feature comparison tables
- Save comparison results
- Share comparisons

### Data Requirements:
- Comparison sessions
- Comparison criteria
- Shared comparison links

## 7. Messaging & Communication

### Features:
- Direct messaging between users
- Contact seller forms
- WhatsApp integration
- Message history
- Notification system
- Email notifications

### Data Requirements:
- Message threads and conversations
- Message content and metadata
- Notification preferences
- Contact form submissions

## 8. Blog & Content Management

### Features:
- Blog post creation and management
- Categories and tags
- Comments system
- Author management
- SEO optimization
- Content scheduling

### Data Requirements:
- Blog posts and content
- Categories and tags
- Comments and interactions
- Author information
- SEO metadata

## 9. E-commerce & Shop

### Features:
- Product catalog management
- Shopping cart functionality
- Checkout process
- Order management
- Inventory tracking
- Payment processing
- Accessories marketplace
- Compatibility checking

### Data Requirements:
- Product information
- Cart and order data
- Payment transactions
- Inventory levels
- Shipping information
- Accessory specifications
- Compatibility data

## 10. Reviews & Ratings

### Features:
- Car reviews and ratings
- User reviews
- Review moderation
- Rating aggregation
- Review helpfulness voting

### Data Requirements:
- Review content and ratings
- Review metadata
- User voting data
- Aggregated statistics

## 11. Loan Calculator & Financial Tools

### Features:
- Loan calculation
- Interest rate management
- Payment scheduling
- Financial product recommendations
- Credit score integration

### Data Requirements:
- Loan calculations
- Financial product data
- User financial information
- Payment schedules

## 12. Pricing & Subscription Management

### Features:
- Listing pricing tiers
- Subscription management
- Payment processing
- Billing history
- Feature access control

### Data Requirements:
- Pricing plans
- Subscription data
- Payment history
- Feature permissions

## 13. Location & Mapping

### Features:
- Geographic search
- Map-based listings
- Location-based recommendations
- Distance calculations
- Dealer location management

### Data Requirements:
- Geographic coordinates
- Location data
- Distance calculations
- Map markers and regions

## 14. Media Management

### Features:
- Image and video uploads
- Media optimization
- Gallery management
- CDN integration
- Media metadata

### Data Requirements:
- Media files and metadata
- Upload history
- Optimization settings
- CDN references

## 15. Notification System

### Features:
- Email notifications
- Push notifications
- SMS notifications
- In-app notifications
- Notification preferences

### Data Requirements:
- Notification templates
- User preferences
- Delivery status
- Notification history

## 16. Accessories Marketplace

### Features:
- Accessory catalog management
- Category and brand organization
- Specification management
- Compatibility system
- Inventory tracking
- Condition management (new, used, refurbished)
- Warranty information
- Stock management

### Data Requirements:
- Accessory information and specifications
- Category and brand data
- Compatibility relationships
- Inventory levels
- Condition and warranty details
- Technical specifications

---

## Database Schema Design

### Core Tables

#### 1. Users Table
```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    profile_picture VARCHAR(500),
    location VARCHAR(255),
    bio TEXT,
    role ENUM('buyer', 'seller', 'admin') DEFAULT 'buyer',
    status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
    email_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    last_login TIMESTAMP NULL,
    INDEX idx_email (email),
    INDEX idx_role (role),
    INDEX idx_status (status)
);
```

#### 2. User Social Links Table
```sql
CREATE TABLE user_social_links (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    platform VARCHAR(50) NOT NULL,
    url VARCHAR(500) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id)
);
```

#### 3. Car Makes Table
```sql
CREATE TABLE car_makes (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL UNIQUE,
    logo VARCHAR(500),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_name (name),
    INDEX idx_active (is_active)
);
```

#### 4. Car Models Table
```sql
CREATE TABLE car_models (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    make_id BIGINT NOT NULL,
    name VARCHAR(100) NOT NULL,
    year_start INT,
    year_end INT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (make_id) REFERENCES car_makes(id) ON DELETE CASCADE,
    INDEX idx_make_id (make_id),
    INDEX idx_name (name),
    INDEX idx_active (is_active)
);
```

#### 5. Car Listings Table
```sql
CREATE TABLE car_listings (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    make_id BIGINT NOT NULL,
    model_id BIGINT NOT NULL,
    year INT NOT NULL,
    condition ENUM('new', 'used', 'certified') NOT NULL,
    price DECIMAL(12,2) NOT NULL,
    mileage INT,
    fuel_type ENUM('gasoline', 'diesel', 'electric', 'hybrid') NOT NULL,
    transmission ENUM('manual', 'automatic', 'semi-automatic') NOT NULL,
    drive_type ENUM('fwd', 'rwd', 'awd', '4wd') NOT NULL,
    engine_size DECIMAL(3,1),
    cylinders INT,
    doors INT,
    seats INT,
    color VARCHAR(50),
    interior_color VARCHAR(50),
    vin VARCHAR(17) UNIQUE,
    primary_image VARCHAR(500),
    gallery_images JSON,
    videos JSON,
    listing_type ENUM('car', 'accessory') DEFAULT 'car',
    status ENUM('active', 'pending', 'sold', 'draft') DEFAULT 'pending',
    featured BOOLEAN DEFAULT FALSE,
    views_count INT DEFAULT 0,
    favorites_count INT DEFAULT 0,
    location VARCHAR(255),
    latitude DECIMAL(10,8),
    longitude DECIMAL(11,8),
    contact_phone VARCHAR(20),
    contact_email VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (make_id) REFERENCES car_makes(id),
    FOREIGN KEY (model_id) REFERENCES car_models(id),
    INDEX idx_user_id (user_id),
    INDEX idx_make_model (make_id, model_id),
    INDEX idx_price (price),
    INDEX idx_year (year),
    INDEX idx_status (status),
    INDEX idx_featured (featured),
    INDEX idx_listing_type (listing_type),
    INDEX idx_location (latitude, longitude),
    FULLTEXT idx_search (title, description)
);
```

#### 6. Car Features Table
```sql
CREATE TABLE car_features (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL UNIQUE,
    category VARCHAR(50) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_category (category),
    INDEX idx_active (is_active)
);
```

#### 7. Car Listing Features Table
```sql
CREATE TABLE car_listing_features (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    listing_id BIGINT NOT NULL,
    feature_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE CASCADE,
    FOREIGN KEY (feature_id) REFERENCES car_features(id) ON DELETE CASCADE,
    UNIQUE KEY unique_listing_feature (listing_id, feature_id),
    INDEX idx_listing_id (listing_id),
    INDEX idx_feature_id (feature_id)
);
```



#### 9. User Favorites Table
```sql
CREATE TABLE user_favorites (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    listing_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE CASCADE,
    UNIQUE KEY unique_user_favorite (user_id, listing_id),
    INDEX idx_user_id (user_id),
    INDEX idx_listing_id (listing_id)
);
```

#### 10. User Views Table
```sql
CREATE TABLE user_views (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT,
    listing_id BIGINT NOT NULL,
    ip_address VARCHAR(45),
    user_agent TEXT,
    viewed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_listing_id (listing_id),
    INDEX idx_viewed_at (viewed_at)
);
```

#### 11. Messages Table
```sql
CREATE TABLE messages (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    sender_id BIGINT NOT NULL,
    recipient_id BIGINT NOT NULL,
    listing_id BIGINT,
    subject VARCHAR(255),
    message TEXT NOT NULL,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (sender_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (recipient_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE SET NULL,
    INDEX idx_sender_id (sender_id),
    INDEX idx_recipient_id (recipient_id),
    INDEX idx_listing_id (listing_id),
    INDEX idx_is_read (is_read),
    INDEX idx_created_at (created_at)
);
```

#### 12. Message Threads Table
```sql
CREATE TABLE message_threads (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user1_id BIGINT NOT NULL,
    user2_id BIGINT NOT NULL,
    listing_id BIGINT,
    last_message_id BIGINT,
    last_message_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user1_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (user2_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE SET NULL,
    FOREIGN KEY (last_message_id) REFERENCES messages(id) ON DELETE SET NULL,
    UNIQUE KEY unique_thread (user1_id, user2_id, listing_id),
    INDEX idx_user1_id (user1_id),
    INDEX idx_user2_id (user2_id),
    INDEX idx_last_message_at (last_message_at)
);
```

#### 13. Blog Categories Table
```sql
CREATE TABLE blog_categories (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL UNIQUE,
    slug VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_slug (slug),
    INDEX idx_active (is_active)
);
```

#### 14. Blog Posts Table
```sql
CREATE TABLE blog_posts (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    author_id BIGINT NOT NULL,
    category_id BIGINT,
    title VARCHAR(255) NOT NULL,
    slug VARCHAR(255) NOT NULL UNIQUE,
    excerpt TEXT,
    content LONGTEXT NOT NULL,
    featured_image VARCHAR(500),
    status ENUM('draft', 'published', 'archived') DEFAULT 'draft',
    published_at TIMESTAMP NULL,
    views_count INT DEFAULT 0,
    comments_count INT DEFAULT 0,
    meta_title VARCHAR(255),
    meta_description TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES blog_categories(id) ON DELETE SET NULL,
    INDEX idx_author_id (author_id),
    INDEX idx_category_id (category_id),
    INDEX idx_slug (slug),
    INDEX idx_status (status),
    INDEX idx_published_at (published_at),
    FULLTEXT idx_search (title, content)
);
```

#### 15. Blog Tags Table
```sql
CREATE TABLE blog_tags (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL UNIQUE,
    slug VARCHAR(50) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_slug (slug)
);
```

#### 16. Blog Post Tags Table
```sql
CREATE TABLE blog_post_tags (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    post_id BIGINT NOT NULL,
    tag_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES blog_posts(id) ON DELETE CASCADE,
    FOREIGN KEY (tag_id) REFERENCES blog_tags(id) ON DELETE CASCADE,
    UNIQUE KEY unique_post_tag (post_id, tag_id),
    INDEX idx_post_id (post_id),
    INDEX idx_tag_id (tag_id)
);
```

#### 17. Blog Comments Table
```sql
CREATE TABLE blog_comments (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    post_id BIGINT NOT NULL,
    user_id BIGINT,
    parent_id BIGINT,
    name VARCHAR(100),
    email VARCHAR(255),
    content TEXT NOT NULL,
    is_approved BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES blog_posts(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    FOREIGN KEY (parent_id) REFERENCES blog_comments(id) ON DELETE CASCADE,
    INDEX idx_post_id (post_id),
    INDEX idx_user_id (user_id),
    INDEX idx_parent_id (parent_id),
    INDEX idx_approved (is_approved)
);
```

#### 18. Products Table (E-commerce)
```sql
CREATE TABLE products (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(255) NOT NULL UNIQUE,
    description TEXT,
    short_description TEXT,
    price DECIMAL(10,2) NOT NULL,
    sale_price DECIMAL(10,2),
    sku VARCHAR(100) UNIQUE,
    stock_quantity INT DEFAULT 0,
    manage_stock BOOLEAN DEFAULT TRUE,
    stock_status ENUM('instock', 'outofstock', 'onbackorder') DEFAULT 'instock',
    weight DECIMAL(8,2),
    dimensions VARCHAR(100),
    featured_image VARCHAR(500),
    gallery_images JSON,
    status ENUM('active', 'inactive', 'draft') DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_slug (slug),
    INDEX idx_sku (sku),
    INDEX idx_status (status),
    INDEX idx_stock_status (stock_status),
    FULLTEXT idx_search (name, description)
);
```

#### 19. Shopping Cart Table
```sql
CREATE TABLE shopping_cart (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT,
    session_id VARCHAR(255),
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL DEFAULT 1,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_session_id (session_id),
    INDEX idx_product_id (product_id)
);
```

#### 20. Orders Table
```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT,
    order_number VARCHAR(50) NOT NULL UNIQUE,
    status ENUM('pending', 'processing', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',
    subtotal DECIMAL(10,2) NOT NULL,
    tax_amount DECIMAL(10,2) DEFAULT 0,
    shipping_amount DECIMAL(10,2) DEFAULT 0,
    total_amount DECIMAL(10,2) NOT NULL,
    payment_status ENUM('pending', 'paid', 'failed', 'refunded') DEFAULT 'pending',
    payment_method VARCHAR(50),
    shipping_address JSON,
    billing_address JSON,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_user_id (user_id),
    INDEX idx_order_number (order_number),
    INDEX idx_status (status),
    INDEX idx_payment_status (payment_status)
);
```

#### 21. Order Items Table
```sql
CREATE TABLE order_items (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    order_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    total DECIMAL(10,2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE,
    INDEX idx_order_id (order_id),
    INDEX idx_product_id (product_id)
);
```

#### 22. Reviews Table
```sql
CREATE TABLE reviews (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    listing_id BIGINT,
    product_id BIGINT,
    rating INT NOT NULL CHECK (rating >= 1 AND rating <= 5),
    title VARCHAR(255),
    content TEXT,
    is_approved BOOLEAN DEFAULT FALSE,
    helpful_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_listing_id (listing_id),
    INDEX idx_product_id (product_id),
    INDEX idx_rating (rating),
    INDEX idx_approved (is_approved)
);
```

#### 23. Search History Table
```sql
CREATE TABLE search_history (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT,
    search_query VARCHAR(500) NOT NULL,
    filters JSON,
    results_count INT,
    ip_address VARCHAR(45),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_user_id (user_id),
    INDEX idx_created_at (created_at)
);
```

#### 24. Notifications Table
```sql
CREATE TABLE notifications (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    type VARCHAR(50) NOT NULL,
    title VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    data JSON,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_type (type),
    INDEX idx_is_read (is_read),
    INDEX idx_created_at (created_at)
);
```

#### 25. Pricing Plans Table
```sql
CREATE TABLE pricing_plans (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    billing_period ENUM('monthly', 'yearly') NOT NULL,
    features JSON,
    max_listings INT,
    is_featured BOOLEAN DEFAULT FALSE,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_active (is_active),
    INDEX idx_featured (is_featured)
);
```

#### 26. User Subscriptions Table
```sql
CREATE TABLE user_subscriptions (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    plan_id BIGINT NOT NULL,
    status ENUM('active', 'cancelled', 'expired') DEFAULT 'active',
    start_date TIMESTAMP NOT NULL,
    end_date TIMESTAMP NOT NULL,
    auto_renew BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (plan_id) REFERENCES pricing_plans(id) ON DELETE CASCADE,
    INDEX idx_user_id (user_id),
    INDEX idx_plan_id (plan_id),
    INDEX idx_status (status),
    INDEX idx_end_date (end_date)
);
```

#### 27. Contact Forms Table
```sql
CREATE TABLE contact_forms (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    subject VARCHAR(255),
    message TEXT NOT NULL,
    listing_id BIGINT,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (listing_id) REFERENCES car_listings(id) ON DELETE SET NULL,
    INDEX idx_email (email),
    INDEX idx_listing_id (listing_id),
    INDEX idx_is_read (is_read),
    INDEX idx_created_at (created_at)
);
```

#### 28. Loan Calculations Table
```sql
CREATE TABLE loan_calculations (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT,
    vehicle_price DECIMAL(12,2) NOT NULL,
    down_payment DECIMAL(12,2) NOT NULL,
    interest_rate DECIMAL(5,2) NOT NULL,
    loan_term_months INT NOT NULL,
    monthly_payment DECIMAL(10,2) NOT NULL,
    total_interest DECIMAL(12,2) NOT NULL,
    total_amount DECIMAL(12,2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    INDEX idx_user_id (user_id),
    INDEX idx_created_at (created_at)
);
```

#### 29. System Settings Table
```sql
CREATE TABLE system_settings (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    setting_key VARCHAR(100) NOT NULL UNIQUE,
    setting_value TEXT,
    setting_type ENUM('string', 'number', 'boolean', 'json') DEFAULT 'string',
    description TEXT,
    is_public BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_key (setting_key),
    INDEX idx_public (is_public)
);
```

#### 30. Accessories Categories Table
```sql
CREATE TABLE accessories_categories (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL UNIQUE,
    slug VARCHAR(100) NOT NULL UNIQUE,
    description TEXT,
    parent_id BIGINT NULL,
    icon VARCHAR(100),
    is_active BOOLEAN DEFAULT TRUE,
    sort_order INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (parent_id) REFERENCES accessories_categories(id) ON DELETE SET NULL,
    INDEX idx_parent_id (parent_id),
    INDEX idx_slug (slug),
    INDEX idx_active (is_active)
);
```

#### 31. Accessories Brands Table
```sql
CREATE TABLE accessories_brands (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL UNIQUE,
    logo VARCHAR(500),
    website VARCHAR(255),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_name (name),
    INDEX idx_active (is_active)
);
```

#### 32. Accessories Table
```sql
CREATE TABLE accessories (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    category_id BIGINT NOT NULL,
    brand_id BIGINT,
    sku VARCHAR(100) UNIQUE,
    price DECIMAL(10,2) NOT NULL,
    sale_price DECIMAL(10,2),
    condition ENUM('new', 'used', 'refurbished') NOT NULL,
    stock_quantity INT DEFAULT 0,
    manage_stock BOOLEAN DEFAULT TRUE,
    stock_status ENUM('instock', 'outofstock', 'onbackorder') DEFAULT 'instock',
    weight DECIMAL(8,2),
    dimensions VARCHAR(100),
    compatibility_notes TEXT,
    warranty_info TEXT,
    primary_image VARCHAR(500),
    gallery_images JSON,
    videos JSON,
    status ENUM('active', 'pending', 'sold', 'draft') DEFAULT 'pending',
    featured BOOLEAN DEFAULT FALSE,
    views_count INT DEFAULT 0,
    favorites_count INT DEFAULT 0,
    location VARCHAR(255),
    latitude DECIMAL(10,8),
    longitude DECIMAL(11,8),
    contact_phone VARCHAR(20),
    contact_email VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES accessories_categories(id),
    FOREIGN KEY (brand_id) REFERENCES accessories_brands(id),
    INDEX idx_user_id (user_id),
    INDEX idx_category_id (category_id),
    INDEX idx_brand_id (brand_id),
    INDEX idx_price (price),
    INDEX idx_status (status),
    INDEX idx_featured (featured),
    INDEX idx_location (latitude, longitude),
    FULLTEXT idx_search (title, description)
);
```

#### 33. Accessories Compatibility Table
```sql
CREATE TABLE accessories_compatibility (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    accessory_id BIGINT NOT NULL,
    make_id BIGINT,
    model_id BIGINT,
    year_start INT,
    year_end INT,
    notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (accessory_id) REFERENCES accessories(id) ON DELETE CASCADE,
    FOREIGN KEY (make_id) REFERENCES car_makes(id) ON DELETE CASCADE,
    FOREIGN KEY (model_id) REFERENCES car_models(id) ON DELETE CASCADE,
    INDEX idx_accessory_id (accessory_id),
    INDEX idx_make_model (make_id, model_id)
);
```

#### 34. Accessories Specifications Table
```sql
CREATE TABLE accessories_specifications (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    accessory_id BIGINT NOT NULL,
    spec_name VARCHAR(100) NOT NULL,
    spec_value VARCHAR(255) NOT NULL,
    spec_unit VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (accessory_id) REFERENCES accessories(id) ON DELETE CASCADE,
    INDEX idx_accessory_id (accessory_id),
    INDEX idx_spec_name (spec_name)
);
```

---

## API Endpoints Structure

### Authentication Endpoints
- `POST /api/auth/register` - User registration
  - **Pages**: `/signup`, `/login` (modal), all pages (header)
- `POST /api/auth/login` - User login
  - **Pages**: `/login`, `/signup` (modal), all pages (header)
- `POST /api/auth/logout` - User logout
  - **Pages**: All pages (header), `/dashboard` (sidebar)
- `POST /api/auth/forgot-password` - Password reset request
  - **Pages**: `/login` (forgot password link)
- `POST /api/auth/reset-password` - Password reset
  - **Pages**: Password reset page
- `GET /api/auth/me` - Get current user
  - **Pages**: All authenticated pages, `/dashboard`, `/profile`
- `PUT /api/auth/profile` - Update user profile
  - **Pages**: `/profile`, `/dashboard`

### Car Listings Endpoints
- `GET /api/listings` - Get all listings with filters
  - **Pages**: `/home-1`, `/home-2`, `/home-3`, `/home-4`, `/home-5`, `/home-6`, `/home-7`, `/listing-v1`, `/listing-v2`, `/listing-v3`, `/listing-v4`, `/listing-v5`, `/listing-v6`, `/listing-map-v1`, `/listing-map-v2`, `/listing-map-v3`
- `GET /api/listings/:id` - Get single listing
  - **Pages**: `/listing-single-v1`, `/listing-single-v2`, `/listing-single-v3`, `/listing-single-v4`, `/listing-single-v5`, `/listing-single-v6`
- `POST /api/listings` - Create new listing
  - **Pages**: `/add-listings`
- `PUT /api/listings/:id` - Update listing
  - **Pages**: `/add-listings` (edit mode), `/my-listing`
- `DELETE /api/listings/:id` - Delete listing
  - **Pages**: `/my-listing`
- `POST /api/listings/:id/upload` - Upload listing images and videos
  - **Pages**: `/add-listings`
- `GET /api/listings/search` - Advanced search
  - **Pages**: All home pages (hero filter), all listing pages (sidebar filters)
- `GET /api/listings/featured` - Get featured listings
  - **Pages**: `/home-1`, `/home-2`, `/home-3`, `/home-4`, `/home-5`, `/home-6`, `/home-7`, `/listing-v4` (sidebar)

### User Management Endpoints
- `GET /api/users/:id` - Get user profile
  - **Pages**: `/user-profile`, `/profile`
- `PUT /api/users/:id` - Update user profile
  - **Pages**: `/profile`
- `GET /api/users/:id/listings` - Get user's listings
  - **Pages**: `/my-listing`, `/user-profile`
- `GET /api/users/:id/favorites` - Get user's favorites
  - **Pages**: `/favourites`
- `POST /api/users/:id/favorites` - Add to favorites
  - **Pages**: All listing pages (heart icon), `/listing-single-*` pages
- `DELETE /api/users/:id/favorites/:listingId` - Remove from favorites
  - **Pages**: `/favourites`

### Messaging Endpoints
- `GET /api/messages` - Get user's messages
  - **Pages**: `/messages`
- `GET /api/messages/threads` - Get message threads
  - **Pages**: `/messages`
- `POST /api/messages` - Send message
  - **Pages**: `/messages`, `/listing-single-*` (contact seller), `/user-profile` (contact seller)
- `PUT /api/messages/:id/read` - Mark message as read
  - **Pages**: `/messages`
- `GET /api/messages/threads/:id` - Get thread messages
  - **Pages**: `/messages`

### Contact Seller API

#### Endpoints

- `POST /api/contact-seller` - Send a message to the seller
  - **Request Body**:
    ```json
    {
      "listingId": "string",
      "senderId": "string",
      "receiverId": "string",
      "message": "string",
      "subject": "string"
    }
    ```
  - **Response**:
    ```json
    {
      "status": "success",
      "messageId": "string",
      "timestamp": "date"
    }
    ```
  - **Pages**: `/listing-single-*` (contact seller button), `/user-profile` (contact seller button)

- `GET /api/contact-seller/conversation/:listingId` - Retrieve the conversation for a specific listing
  - **Response**:
    ```json
    {
      "status": "success",
      "messages": [
        {
          "messageId": "string",
          "senderId": "string",
          "receiverId": "string",
          "content": "string",
          "timestamp": "date",
          "read": true
        }
      ]
    }
    ```
  - **Pages**: `/messages`, `/dashboard` (messages section)

- `PUT /api/contact-seller/:messageId/read` - Mark a message as read
  - **Response**:
    ```json
    {
      "status": "success",
      "message": "Message marked as read"
    }
    ```
  - **Pages**: `/messages`, `/dashboard` (messages section)

#### 35. Offers Table
```sql
CREATE TABLE offers (
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

### Offers Management Endpoints
- `POST /api/offers` - Submit new offer
  - **Pages**: `/listing-single-*` (make offer button)
- `GET /api/offers/listing/:id` - Get all offers for a listing
  - **Pages**: `/listing-single-*` (seller view), `/dashboard` (seller offers section)
- `GET /api/offers/user/:id` - Get all offers by user (both as buyer and seller)
  - **Pages**: `/dashboard` (my offers section)
- `GET /api/offers/buyer/:id` - Get all offers made by user
  - **Pages**: `/dashboard` (my offers made section)
- `GET /api/offers/seller/:id` - Get all offers received by user
  - **Pages**: `/dashboard` (offers received section)
- `PUT /api/offers/:id` - Update offer status (accept/reject)
  - **Pages**: `/dashboard` (seller offers section)
- `DELETE /api/offers/:id` - Withdraw offer (only if pending)
  - **Pages**: `/dashboard` (my offers section)

### Blog Endpoints
- `GET /api/blog/posts` - Get blog posts
  - **Pages**: `/blog-grid`, `/blog-list`, `/home-1` (blog section), `/home-2` (blog section), `/home-3` (blog section), `/home-4` (blog section), `/home-5` (blog section), `/home-6` (blog section), `/home-7` (blog section)
- `GET /api/blog/posts/:id` - Get single post
  - **Pages**: `/blog-single`
- `POST /api/blog/posts` - Create blog post (admin)
  - **Pages**: Admin panel (not in current template)
- `PUT /api/blog/posts/:id` - Update blog post (admin)
  - **Pages**: Admin panel (not in current template)
- `DELETE /api/blog/posts/:id` - Delete blog post (admin)
  - **Pages**: Admin panel (not in current template)
- `GET /api/blog/categories` - Get categories
  - **Pages**: `/blog-grid`, `/blog-list`, `/blog-single` (sidebar)
- `POST /api/blog/posts/:id/comments` - Add comment
  - **Pages**: `/blog-single`

### E-commerce Endpoints
- `GET /api/products` - Get products
  - **Pages**: `/shop`
- `GET /api/products/:id` - Get single product
  - **Pages**: `/shop-single`
- `GET /api/cart` - Get user's cart
  - **Pages**: `/cart`
- `POST /api/cart` - Add to cart
  - **Pages**: `/shop`, `/shop-single`
- `PUT /api/cart/:id` - Update cart item
  - **Pages**: `/cart`
- `DELETE /api/cart/:id` - Remove from cart
  - **Pages**: `/cart`
- `POST /api/orders` - Create order
  - **Pages**: `/checkout`
- `GET /api/orders` - Get user's orders
  - **Pages**: `/dashboard` (order history)
- `GET /api/orders/:id` - Get single order
  - **Pages**: `/complete-order`

### Analytics Endpoints
- `GET /api/analytics/dashboard` - Dashboard statistics
  - **Pages**: `/dashboard`
- `GET /api/analytics/listings/:id` - Listing analytics
  - **Pages**: `/my-listing`, `/dashboard`
- `GET /api/analytics/search-trends` - Search trends
  - **Pages**: `/dashboard` (admin analytics)
- `GET /api/analytics/popular-searches` - Popular searches
  - **Pages**: `/dashboard` (admin analytics), search suggestions

### Additional Endpoints

#### Comparison Endpoints
- `GET /api/compare` - Get comparison data
  - **Pages**: `/compare`
- `POST /api/compare/add` - Add car to comparison
  - **Pages**: All listing pages (compare button)
- `DELETE /api/compare/remove` - Remove car from comparison
  - **Pages**: `/compare`

#### Contact & Lead Generation Endpoints
- `POST /api/contact` - Submit contact form
  - **Pages**: `/contact`, `/listing-single-*` (contact seller), `/user-profile` (contact seller)
- `POST /api/loan-calculator` - Calculate loan
  - **Pages**: `/loan-calculator`

#### Reviews & Ratings Endpoints
- `GET /api/reviews/listing/:id` - Get listing reviews
  - **Pages**: `/listing-single-*`, `/user-profile`
- `POST /api/reviews` - Submit review
  - **Pages**: `/listing-single-*`, `/user-profile`
- `GET /api/reviews/user/:id` - Get user reviews
  - **Pages**: `/user-profile`

#### Pricing & Subscription Endpoints
- `GET /api/pricing/plans` - Get pricing plans
  - **Pages**: `/pricing`
- `POST /api/subscriptions` - Create subscription
  - **Pages**: `/pricing`
- `GET /api/subscriptions/user/:id` - Get user subscriptions
  - **Pages**: `/dashboard`

#### Search & Filter Endpoints
- `GET /api/search/suggestions` - Get search suggestions
  - **Pages**: All pages (search autocomplete)
- `GET /api/filters/makes` - Get car makes
  - **Pages**: All listing pages (filter dropdowns)
- `GET /api/filters/models/:makeId` - Get car models
  - **Pages**: All listing pages (filter dropdowns)
- `GET /api/filters/features` - Get car features
  - **Pages**: `/add-listings`, `/listing-single-*`

#### Media & File Upload Endpoints
- `POST /api/upload/image` - Upload image
  - **Pages**: `/add-listings`, `/profile` (profile picture)
- `POST /api/upload/video` - Upload video
  - **Pages**: `/add-listings`
- `DELETE /api/media/:id` - Delete media
  - **Pages**: `/add-listings`, `/profile`

#### Notification Endpoints
- `GET /api/notifications` - Get user notifications
  - **Pages**: All pages (notification dropdown)
- `PUT /api/notifications/:id/read` - Mark notification as read
  - **Pages**: All pages (notification dropdown)
- `POST /api/notifications/preferences` - Update notification preferences
  - **Pages**: `/profile`

#### Recently Viewed Endpoints
- `GET /api/recently-viewed` - Get recently viewed items
  - **Pages**: `/listing-v4` (sidebar), `/dashboard`
- `POST /api/recently-viewed` - Add to recently viewed
  - **Pages**: All listing pages (automatic tracking)

#### Popular & Featured Endpoints
- `GET /api/popular/listings` - Get popular listings
  - **Pages**: `/home-1` (popular listings section), `/home-2`, `/home-3`, `/home-4`, `/home-5`, `/home-6`, `/home-7`
- `GET /api/partners` - Get partner/dealer logos
  - **Pages**: All home pages (partners section)
- `GET /api/testimonials` - Get testimonials
  - **Pages**: All home pages (testimonials section)

#### Accessories Management Endpoints
- `GET /api/accessories` - Get all accessories with filters
  - **Pages**: `/accessories`, `/home-*` (featured accessories), `/shop` (accessories section)
- `GET /api/accessories/:id` - Get single accessory
  - **Pages**: `/accessories/:id`, `/shop-single` (for accessories)
- `POST /api/accessories` - Create new accessory
  - **Pages**: `/add-accessory`, `/add-listings` (accessory mode)
- `PUT /api/accessories/:id` - Update accessory
  - **Pages**: `/add-accessory` (edit mode), `/my-accessories`
- `DELETE /api/accessories/:id` - Delete accessory
  - **Pages**: `/my-accessories`
- `POST /api/accessories/:id/upload` - Upload accessory images
  - **Pages**: `/add-accessory`
- `GET /api/accessories/categories` - Get accessory categories
  - **Pages**: `/accessories` (filter dropdown), `/add-accessory` (category selection)
- `GET /api/accessories/brands` - Get accessory brands
  - **Pages**: `/accessories` (filter dropdown), `/add-accessory` (brand selection)
- `GET /api/accessories/compatibility/:id` - Get compatibility info
  - **Pages**: `/accessories/:id`, compatibility checker tool
- `GET /api/accessories/featured` - Get featured accessories
  - **Pages**: `/home-*` (featured accessories section)

#### Combined Search & Listing Endpoints
- `GET /api/search/combined` - Search both cars and accessories
  - **Pages**: All home pages (hero search), all listing pages (search functionality)
- `GET /api/listings/featured` - Get featured cars and accessories
  - **Pages**: `/home-*` (featured section), `/listing-v4` (sidebar)
- `GET /api/users/:id/listings` - Get user's cars and accessories
  - **Pages**: `/my-listing`, `/user-profile`
- `GET /api/users/:id/favorites` - Get user's favorite cars and accessories
  - **Pages**: `/favourites`
- `POST /api/users/:id/favorites` - Add car or accessory to favorites
  - **Pages**: All listing pages (heart icon), `/listing-single-*`, `/accessories/:id`
- `DELETE /api/users/:id/favorites/:itemId` - Remove from favorites
  - **Pages**: `/favourites`

---

## Backend Technology Recommendations

### Database
- **Primary Database**: MySQL 8.0 

### Backend Framework
- **Node.js**: Express.js 

### Authentication
- **JWT**: For stateless authentication
- **OAuth 2.0**: For social login integration
- **Rate Limiting**: To prevent abuse

### Additional Services
- **Email Service**: SendGrid, Mailgun, 
- **SMS Service**: Twilio 
- **Payment Processing**: Stripe, PayPal,
- **Image Processing**: Multer (Node.js) 
- **Background Jobs**: Bull (Node.js) 

This comprehensive backend structure provides all the necessary tables and relationships to support the full functionality of the **caautoline**  website. The schema is designed to be scalable, maintainable, and follows database normalization principles.

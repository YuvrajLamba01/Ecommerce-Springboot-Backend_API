# Entity Relationship Documentation

## Updated Entity Structure

All entities have been updated to match the specified requirements:

### 1. USER Entity
- **Collection**: `users`
- **Fields**:
  - `id` (String, Primary Key)
  - `username` (String)
  - `email` (String)
  - `role` (String)

### 2. PRODUCT Entity
- **Collection**: `products`
- **Fields**:
  - `id` (String, Primary Key)
  - `name` (String)
  - `description` (String)
  - `price` (Double)
  - `stock` (Integer)

### 3. CART_ITEM Entity
- **Collection**: `cart_items`
- **Fields**:
  - `id` (String, Primary Key)
  - `userId` (String, Foreign Key → USER)
  - `productId` (String, Foreign Key → PRODUCT)
  - `quantity` (Integer)

### 4. ORDER Entity
- **Collection**: `orders`
- **Fields**:
  - `id` (String, Primary Key)
  - `userId` (String, Foreign Key → USER)
  - `totalAmount` (Double)
  - `status` (String: CREATED, PAID, FAILED, CANCELLED)
  - `createdAt` (Instant)

### 5. ORDER_ITEM Entity
- **Collection**: `order_items`
- **Fields**:
  - `id` (String, Primary Key)
  - `orderId` (String, Foreign Key → ORDER)
  - `productId` (String, Foreign Key → PRODUCT)
  - `quantity` (Integer)
  - `price` (Double) - Price at time of order

### 6. PAYMENT Entity
- **Collection**: `payments`
- **Fields**:
  - `id` (String, Primary Key)
  - `orderId` (String, Foreign Key → ORDER)
  - `amount` (Double)
  - `status` (String: PENDING, SUCCESS, FAILED)
  - `paymentId` (String) - External payment ID
  - `createdAt` (Instant)

## Relationships

```
USER (1) ----< (N) CART_ITEM
USER (1) ----< (N) ORDER

PRODUCT (1) ----< (N) CART_ITEM
PRODUCT (1) ----< (N) ORDER_ITEM

ORDER (1) ----< (N) ORDER_ITEM
ORDER (1) ---- (1) PAYMENT
```

## Key Changes Made

### Data Types
- Changed `BigDecimal` → `Double` for all price/amount fields
- Changed `LocalDateTime` → `Instant` for timestamp fields
- Renamed `stockQuantity` → `stock` in Product
- Renamed `transactionId` → `paymentId` in Payment

### Removed Fields
- **User**: Removed `password`, `firstName`, `lastName`, `phoneNumber`, `address`
- **Product**: Removed `category`, `imageUrl`
- **CartItem**: Removed `productName`, `price`, `subtotal`
- **Order**: Removed `items` (embedded list), `shippingAddress`, `paymentId`, `updatedAt`
- **Payment**: Removed `userId`, `paymentMethod`, `completedAt`

### Status Values
- **Order**: `CREATED`, `PAID`, `FAILED`, `CANCELLED`
- **Payment**: `PENDING`, `SUCCESS`, `FAILED`

### New Features
- OrderItem is now a separate collection (not embedded in Order)
- OrderItem includes `id` and `orderId` for proper referencing
- Added `OrderItemRepository` for managing order items separately
- Added `UserRepository` for user management

## Updated APIs

### Simplified DTOs
- `CreateOrderRequest`: Only requires `userId`
- `PaymentRequest`: Only requires `orderId` and `amount`
- `PaymentWebhookRequest`: Uses `orderId` and `paymentId`

### Service Layer Updates
- CartService now calculates totals by fetching product prices dynamically
- OrderService creates separate OrderItem documents
- PaymentService uses simplified payment flow

## Build Status
✅ Project compiles successfully
✅ All 28 source files compiled
✅ No compilation errors

## Next Steps
1. Start MongoDB
2. Run the application
3. Test APIs with Postman
4. Verify entity relationships in MongoDB collections

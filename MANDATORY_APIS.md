# Mandatory APIs Implementation Guide

## Complete API Specifications & Testing

All mandatory APIs have been implemented and are ready for testing with Postman.

---

## 1. Product APIs

### Create Product
**Endpoint**: `POST /api/products`

**Request Body**:
```json
{
  "name": "Laptop",
  "description": "Gaming Laptop",
  "price": 50000.0,
  "stock": 10
}
```

**Response** (201 Created):
```json
{
  "id": "654abc123def456",
  "name": "Laptop",
  "description": "Gaming Laptop",
  "price": 50000.0,
  "stock": 10
}
```

### Get All Products
**Endpoint**: `GET /api/products`

**Response** (200 OK):
```json
[
  {
    "id": "654abc123def456",
    "name": "Laptop",
    "description": "Gaming Laptop",
    "price": 50000.0,
    "stock": 10
  },
  {
    "id": "654abc123def789",
    "name": "Mouse",
    "description": "Wireless Mouse",
    "price": 5000.0,
    "stock": 50
  }
]
```

---

## 2. Cart APIs

### Add Item to Cart
**Endpoint**: `POST /api/cart/add`

**Request Body**:
```json
{
  "userId": "user123",
  "productId": "654abc123def456",
  "quantity": 2
}
```

**Response** (201 Created):
```json
{
  "id": "cart_123",
  "userId": "user123",
  "productId": "654abc123def456",
  "quantity": 2
}
```

### Get User's Cart
**Endpoint**: `GET /api/cart/{userId}`

**Example**: `GET /api/cart/user123`

**Response** (200 OK):
```json
[
  {
    "id": "cart_123",
    "productId": "654abc123def456",
    "quantity": 2,
    "product": {
      "id": "654abc123def456",
      "name": "Laptop",
      "price": 50000.0
    }
  },
  {
    "id": "cart_456",
    "productId": "654abc123def789",
    "quantity": 5,
    "product": {
      "id": "654abc123def789",
      "name": "Mouse",
      "price": 5000.0
    }
  }
]
```

### Clear User's Cart
**Endpoint**: `DELETE /api/cart/{userId}/clear`

**Example**: `DELETE /api/cart/user123/clear`

**Response** (200 OK):
```json
{
  "message": "Cart cleared successfully"
}
```

### Get Cart Total
**Endpoint**: `GET /api/cart/{userId}/total`

**Example**: `GET /api/cart/user123/total`

**Response** (200 OK):
```
110000.0
```

### Remove Item from Cart
**Endpoint**: `DELETE /api/cart/{cartItemId}`

**Example**: `DELETE /api/cart/cart_123`

**Response**: 204 No Content

---

## 3. Order APIs

### Create Order from Cart
**Endpoint**: `POST /api/orders`

**Request Body**:
```json
{
  "userId": "user123"
}
```

**Response** (201 Created):
```json
{
  "id": "order_abc123",
  "userId": "user123",
  "totalAmount": 110000.0,
  "status": "CREATED",
  "items": [
    {
      "productId": "654abc123def456",
      "quantity": 2,
      "price": 50000.0
    },
    {
      "productId": "654abc123def789",
      "quantity": 5,
      "price": 5000.0
    }
  ],
  "payment": null
}
```

### Get Order Details
**Endpoint**: `GET /api/orders/{orderId}`

**Example**: `GET /api/orders/order_abc123`

**Response** (200 OK):
```json
{
  "id": "order_abc123",
  "userId": "user123",
  "totalAmount": 110000.0,
  "status": "PAID",
  "items": [
    {
      "productId": "654abc123def456",
      "quantity": 2,
      "price": 50000.0
    },
    {
      "productId": "654abc123def789",
      "quantity": 5,
      "price": 5000.0
    }
  ],
  "payment": {
    "id": "pay_xyz789",
    "status": "SUCCESS",
    "amount": 110000.0
  }
}
```

### Get User's Orders
**Endpoint**: `GET /api/orders/user/{userId}`

**Example**: `GET /api/orders/user/user123`

**Response** (200 OK):
```json
[
  {
    "id": "order_abc123",
    "userId": "user123",
    "totalAmount": 110000.0,
    "status": "CREATED",
    "createdAt": "2025-01-19T15:30:00Z"
  },
  {
    "id": "order_def456",
    "userId": "user123",
    "totalAmount": 55000.0,
    "status": "PAID",
    "createdAt": "2025-01-18T10:15:00Z"
  }
]
```

---

## 4. Payment APIs

### Create Payment for Order
**Endpoint**: `POST /api/payments/create`

**Request Body**:
```json
{
  "orderId": "order_abc123",
  "amount": 110000.0
}
```

**Response** (201 Created):
```json
{
  "paymentId": "pay_xyz789",
  "orderId": "order_abc123",
  "amount": 110000.0,
  "status": "PENDING"
}
```

**Note**: The mock payment service will automatically:
1. Wait for 3 seconds
2. Call the webhook endpoint with status `SUCCESS`
3. Order status will automatically change to `PAID`

### Get Payment by ID
**Endpoint**: `GET /api/payments/{paymentId}`

**Example**: `GET /api/payments/pay_xyz789`

**Response** (200 OK):
```json
{
  "id": "pay_xyz789",
  "orderId": "order_abc123",
  "amount": 110000.0,
  "status": "SUCCESS",
  "paymentId": "pay_xyz789",
  "createdAt": "2025-01-19T15:30:00Z"
}
```

### Get Payment by Order ID
**Endpoint**: `GET /api/payments/order/{orderId}`

**Example**: `GET /api/payments/order/order_abc123`

**Response** (200 OK):
```json
{
  "id": "pay_xyz789",
  "orderId": "order_abc123",
  "amount": 110000.0,
  "status": "SUCCESS",
  "paymentId": "pay_xyz789",
  "createdAt": "2025-01-19T15:30:00Z"
}
```

### Payment Webhook (Auto-called by Mock Service)
**Endpoint**: `POST /api/webhooks/payment`

**Request Body** (sent by mock service):
```json
{
  "paymentId": "pay_xyz789",
  "orderId": "order_abc123",
  "status": "SUCCESS",
  "message": "Payment processed successfully"
}
```

**Response** (200 OK):
```json
{
  "message": "Webhook processed successfully"
}
```

---

## Complete End-to-End Flow

### Step 1: Create Products
```bash
POST /api/products
{
  "name": "Laptop",
  "description": "Gaming Laptop",
  "price": 50000.0,
  "stock": 10
}
```
Save the returned `id` as `{productId}`

### Step 2: Add Items to Cart
```bash
POST /api/cart/add
{
  "userId": "user123",
  "productId": "{productId}",
  "quantity": 2
}
```

### Step 3: View Cart
```bash
GET /api/cart/user123
```

### Step 4: Create Order
```bash
POST /api/orders
{
  "userId": "user123"
}
```
Save the returned `id` as `{orderId}`

### Step 5: Create Payment
```bash
POST /api/payments/create
{
  "orderId": "{orderId}",
  "amount": 100000.0
}
```
Save the returned `paymentId`

### Step 6: Wait for Webhook (3 seconds)
The mock payment service automatically:
- Processes the payment
- Calls the webhook endpoint
- Updates order status to PAID

### Step 7: Verify Order Status
```bash
GET /api/orders/{orderId}
```
You should see:
- `status`: "PAID"
- `payment`: with status "SUCCESS"

---

## Testing with Postman

### 1. Import Collection
Create a new Postman collection with all the above endpoints.

### 2. Environment Variables
Set up variables in Postman:
- `baseUrl`: `http://localhost:8080/api`
- `productId`: (updated from create product response)
- `orderId`: (updated from create order response)
- `paymentId`: (updated from create payment response)

### 3. Test Flow
1. Run requests in order from the "Complete End-to-End Flow" section above
2. Extract IDs from responses and use them in subsequent requests
3. Use Tests tab in Postman to automatically extract and save IDs

---

## Key Features Implemented

✅ **Product Management**: CRUD operations for products  
✅ **Shopping Cart**: Add/remove items, view cart, calculate totals  
✅ **Order Management**: Create orders, track status, view items  
✅ **Payment Processing**: Mock payment service with auto-webhook  
✅ **Async Webhook**: Webhook called automatically after 3 seconds  
✅ **Order-Payment Link**: Order status automatically updates on payment  

---

## Status Values

### Order Status
- `CREATED` - Order created
- `PAID` - Payment successful
- `FAILED` - Payment failed
- `CANCELLED` - Order cancelled

### Payment Status
- `PENDING` - Awaiting payment processing
- `SUCCESS` - Payment successful
- `FAILED` - Payment failed

---

## Error Handling

### Common Errors

**404 Not Found**
```json
{
  "error": "Order not found",
  "timestamp": "2025-01-19T15:30:00Z"
}
```

**400 Bad Request**
```json
{
  "error": "Invalid request body",
  "details": "amount cannot be null"
}
```

**500 Internal Server Error**
```json
{
  "error": "Internal server error",
  "message": "Database connection failed"
}
```

---

## Build & Run

```bash
# Build
.\mvnw.cmd clean install

# Run
.\mvnw.cmd spring-boot:run
```

Application starts on `http://localhost:8080`

---

## Database

MongoDB collections created automatically:
- `products`
- `cart_items`
- `orders`
- `order_items`
- `payments`
- `users`

---

## Notes

1. All IDs are auto-generated by MongoDB
2. Cart is cleared after order creation
3. Stock is decremented when order is created
4. Payment webhook is automatically called after 3 seconds
5. Order status updates automatically on payment success

# Bonus Challenges - Postman Testing Guide

## Challenge 1: Order History - GET /api/orders/user/{userId}

### cURL Example
```bash
curl -X GET http://localhost:8080/api/orders/user/user123
```

### Postman Setup
- **Method**: GET
- **URL**: `http://localhost:8080/api/orders/user/user123`
- **Headers**: None required
- **Body**: None

### Expected Response
```json
[
  {
    "id": "507f1f77bcf86cd799439011",
    "userId": "user123",
    "totalAmount": 100000.0,
    "status": "PAID",
    "createdAt": "2026-01-19T10:30:00Z"
  }
]
```

### Test Scenarios
1. User with multiple orders → Returns list
2. User with no orders → Returns empty array []
3. Non-existent user → Returns empty array []

---

## Challenge 2: Order Cancellation - POST /api/orders/{orderId}/cancel

### cURL Example
```bash
curl -X POST http://localhost:8080/api/orders/507f1f77bcf86cd799439011/cancel
```

### Postman Setup
- **Method**: POST
- **URL**: `http://localhost:8080/api/orders/{{orderId}}/cancel`
- **Headers**: None required
- **Body**: None
- **Path Variable**: orderId = "507f1f77bcf86cd799439011"

### Expected Response (Success)
```json
{
  "id": "507f1f77bcf86cd799439011",
  "userId": "user123",
  "totalAmount": 100000.0,
  "status": "CANCELLED",
  "createdAt": "2026-01-19T10:30:00Z"
}
```

### Expected Response (Failure - Paid Order)
```
400 Bad Request
Response: ""
```

### Test Scenarios

#### Scenario 1: Cancel CREATED order (Success)
1. Create order with status CREATED
2. Send POST /api/orders/{orderId}/cancel
3. Verify response status is CANCELLED
4. Check that product stock increased

#### Scenario 2: Cancel FAILED order (Success)
1. Create order with status FAILED
2. Send POST /api/orders/{orderId}/cancel
3. Verify response status is CANCELLED

#### Scenario 3: Cancel PAID order (Failure)
1. Create order with status PAID
2. Send POST /api/orders/{orderId}/cancel
3. Expect 400 Bad Request
4. Verify order status unchanged

#### Scenario 4: Cancel non-existent order
1. Use invalid orderId
2. Expect 400 Bad Request
3. Verify error handling

### Stock Restoration Verification
```bash
# Before cancel - check stock
curl -X GET http://localhost:8080/api/products/{productId}
# Note: stock = 10

# Cancel order with 2 items
curl -X POST http://localhost:8080/api/orders/{orderId}/cancel

# After cancel - check stock again
curl -X GET http://localhost:8080/api/products/{productId}
# Expected: stock = 12 (increased by 2)
```

---

## Challenge 3: Product Search - GET /api/products/search

### cURL Examples

#### Search with `q` parameter (Recommended)
```bash
curl -X GET "http://localhost:8080/api/products/search?q=laptop"
curl -X GET "http://localhost:8080/api/products/search?q=phone"
curl -X GET "http://localhost:8080/api/products/search?q=samsung"
```

#### Search with `name` parameter (Legacy)
```bash
curl -X GET "http://localhost:8080/api/products/search?name=laptop"
```

#### Case-insensitive search
```bash
curl -X GET "http://localhost:8080/api/products/search?q=LAPTOP"
curl -X GET "http://localhost:8080/api/products/search?q=LaPtOp"
```

### Postman Setup

#### Method 1: Using q parameter
- **Method**: GET
- **URL**: `http://localhost:8080/api/products/search`
- **Query Params**: 
  - Key: `q`
  - Value: `laptop`
- **Headers**: None required
- **Body**: None

#### Method 2: Using name parameter
- **Method**: GET
- **URL**: `http://localhost:8080/api/products/search`
- **Query Params**:
  - Key: `name`
  - Value: `laptop`
- **Headers**: None required
- **Body**: None

### Expected Response
```json
[
  {
    "id": "507f1f77bcf86cd799439001",
    "name": "Dell Laptop",
    "description": "High-performance laptop",
    "price": 50000.0,
    "stock": 10
  },
  {
    "id": "507f1f77bcf86cd799439002",
    "name": "HP Laptop",
    "description": "Budget-friendly laptop",
    "price": 35000.0,
    "stock": 15
  },
  {
    "id": "507f1f77bcf86cd799439003",
    "name": "Lenovo Laptop",
    "description": "Business laptop",
    "price": 45000.0,
    "stock": 8
  }
]
```

### Test Scenarios

#### Scenario 1: Search with exact match
```
Query: "Dell Laptop"
Expected: Returns product with exact match
```

#### Scenario 2: Partial search
```
Query: "Dell"
Expected: Returns all products containing "Dell"
```

#### Scenario 3: Case-insensitive search
```
Query: "laptop" → Results: Dell Laptop, HP Laptop, Lenovo Laptop
Query: "LAPTOP" → Same results (case-insensitive)
Query: "LaPtOp" → Same results (case-insensitive)
```

#### Scenario 4: Brand search
```
Query: "Samsung"
Expected: All Samsung products (phones, tablets, TVs, etc.)
```

#### Scenario 5: Type search
```
Query: "phone"
Expected: All phone products
Query: "tablet"
Expected: All tablet products
```

#### Scenario 6: No results
```
Query: "xyz123"
Expected: Empty array []
```

#### Scenario 7: Empty search
```
Query: "" (empty string)
Expected: 400 Bad Request
```

#### Scenario 8: Parameter precedence
```
URL: /api/products/search?q=laptop&name=phone
Expected: Results for "laptop" (q parameter takes precedence)
```

---

## Full End-to-End Test Flow

### Test Workflow with All 3 Challenges

#### Step 1: Search for Products
```bash
GET /api/products/search?q=laptop
# Get product ID for next steps
```

#### Step 2: Create Order
```bash
POST /api/orders
Body: {"userId": "user123"}
# Note the orderId
```

#### Step 3: Verify Order Created
```bash
GET /api/orders/{orderId}
# Check status: CREATED
```

#### Step 4: Get User's Order History
```bash
GET /api/orders/user/user123
# Should include the order just created
```

#### Step 5: Check Product Stock Before Cancel
```bash
GET /api/products/{productId}
# Note the stock value (e.g., 10)
```

#### Step 6: Cancel the Order
```bash
POST /api/orders/{orderId}/cancel
# Check status is now CANCELLED
```

#### Step 7: Verify Stock Restored
```bash
GET /api/products/{productId}
# Stock should have increased
```

#### Step 8: Confirm Order History Updated
```bash
GET /api/orders/user/user123
# Order should show CANCELLED status
```

---

## Bonus Challenges Quick Reference

| Challenge | Endpoint | Method | Query Param | Notes |
|-----------|----------|--------|-------------|-------|
| Order History | /api/orders/user/{userId} | GET | - | Returns list |
| Order Cancel | /api/orders/{orderId}/cancel | POST | - | Restores stock |
| Product Search | /api/products/search | GET | q or name | Case-insensitive |

---

## Error Scenarios to Test

### Challenge 1: Order History
```
Invalid userId → Returns empty array []
Non-existent user → Returns empty array []
Valid user, no orders → Returns empty array []
```

### Challenge 2: Order Cancellation
```
Non-existent orderId → 400 Bad Request
Paid order → 400 Bad Request
Already cancelled order → 400 Bad Request (or allow re-cancellation)
Valid CREATED order → 200 OK
Valid FAILED order → 200 OK
```

### Challenge 3: Product Search
```
Empty query → 400 Bad Request
Null query → 400 Bad Request
Non-existent product → 200 OK with empty array []
Valid query → 200 OK with matching products
Multiple matches → 200 OK with all matches
Case variations → 200 OK with same results
```

---

## Notes for Testers

1. **Data Setup**: Ensure MongoDB is running with sample data
2. **Timestamps**: Instant format: "2026-01-19T16:03:34.000Z"
3. **Status Values**: CREATED, PAID, FAILED, CANCELLED
4. **Stock Management**: Always verify stock changes after cancellation
5. **User IDs**: Can be any string (user123, uid_001, etc.)
6. **Product Quantities**: Must not exceed available stock

---

## Performance Considerations

- Order History: O(n) where n = number of orders for user
- Order Cancellation: O(m) where m = number of items in order
- Product Search: O(n log n) MongoDB index-based search

All endpoints should respond in < 200ms with proper indexing.

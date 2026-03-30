# E-Commerce Backend System

A minimal e-commerce backend system built with **Spring Boot 4.0.1** and **Java 17** featuring product management, shopping cart, order processing, and payment integration.

## ✨ Features

✅ **Product Management** - List, create, update, delete products  
✅ **Shopping Cart** - Add items, view cart, calculate totals  
✅ **Order Management** - Place orders, track status  
✅ **Payment Processing** - Mock payment service integration  
✅ **Webhooks** - Payment status updates  
✅ **Auto Reload** - DevTools for faster development  

## 🛠️ Technology Stack

- **Java**: 17
- **Spring Boot**: 4.0.1
- **Database**: MongoDB
- **Build Tool**: Maven
- **Testing**: Postman

## 📋 Prerequisites

- Java 17 or higher
- MongoDB (running on localhost:27017)
- Maven (or use included Maven Wrapper)

## 🚀 Getting Started

### 1. Start MongoDB

Make sure MongoDB is running on your local machine:

```bash
# Windows
mongod

# Linux/Mac
sudo systemctl start mongodb
```

### 2. Clone and Build

```bash
cd ecommerce
.\mvnw.cmd clean install
```

### 3. Run the Application

```bash
.\mvnw.cmd spring-boot:run
```

The application will start on **http://localhost:8080**

## 📚 API Documentation

See [API_DOCUMENTATION.md](API_DOCUMENTATION.md) for complete API reference.

### Quick API Overview

| Feature | Method | Endpoint |
|---------|--------|----------|
| List Products | GET | `/api/products` |
| Add to Cart | POST | `/api/cart` |
| Create Order | POST | `/api/orders` |
| Process Payment | POST | `/api/payments` |
| Payment Webhook | POST | `/api/webhooks/payment` |

## 🧪 Testing with Postman

### Sample Test Flow:

1. **Create a Product**
```json
POST http://localhost:8080/api/products
{
  "name": "Laptop",
  "description": "Gaming Laptop",
  "price": 1299.99,
  "stockQuantity": 10,
  "category": "Electronics",
  "imageUrl": "https://example.com/laptop.jpg"
}
```

2. **Add to Cart**
```json
POST http://localhost:8080/api/cart
{
  "userId": "user123",
  "productId": "{productId from step 1}",
  "quantity": 1
}
```

3. **Create Order**
```json
POST http://localhost:8080/api/orders
{
  "userId": "user123",
  "shippingAddress": "123 Main St, City, State 12345"
}
```

4. **Process Payment**
```json
POST http://localhost:8080/api/payments
{
  "orderId": "{orderId from step 3}",
  "userId": "user123",
  "amount": 1299.99,
  "paymentMethod": "CREDIT_CARD"
}
```

5. **Verify Order Status**
```
GET http://localhost:8080/api/orders/{orderId}
```

## 📁 Project Structure

```
com.example.ecommerce
├── controller/          # REST API Controllers
├── service/            # Business Logic
├── repository/         # MongoDB Repositories
├── model/              # Domain Models
├── dto/                # Data Transfer Objects
├── client/             # External Service Clients
├── webhook/            # Webhook Handlers
└── config/             # Configuration Classes
```

## 🔧 Configuration

Edit `application.properties` to customize:

```properties
server.port=8080
spring.data.mongodb.host=localhost
spring.data.mongodb.port=27017
spring.data.mongodb.database=ecommerce_db
```

## 🗄️ Database Collections

- `products` - Product catalog
- `cart_items` - Shopping cart items
- `orders` - Customer orders
- `payments` - Payment transactions

## 📝 Order Workflow

1. User adds products to cart
2. User creates order (cart → order)
3. Payment is initiated
4. Payment webhook updates order status
5. Order status changes to CONFIRMED

## 💳 Payment Methods

- CREDIT_CARD
- DEBIT_CARD
- PAYPAL
- UPI

## 📊 Order Statuses

- PENDING - Order created, awaiting payment
- CONFIRMED - Payment successful
- SHIPPED - Order dispatched
- DELIVERED - Order completed
- CANCELLED - Order cancelled

## 🐛 Troubleshooting

### MongoDB Connection Issues
```
Error: Connection refused
Solution: Ensure MongoDB is running on port 27017
```

### Port Already in Use
```
Error: Port 8080 is already in use
Solution: Change server.port in application.properties
```

## 🔄 Auto Reload

DevTools is enabled for automatic restart on code changes. Just save your files and the application will reload automatically!

## 📦 Build & Deploy

### Build JAR
```bash
.\mvnw.cmd clean package
```

### Run JAR
```bash
java -jar target/ecommerce-0.0.1-SNAPSHOT.jar
```



## 📄 License

This is a student project for educational purposes.

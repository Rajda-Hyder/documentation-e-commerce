# MARKETPLACE TECNICAL FOUNDATION -[MEUBEL HOUSE FURNITURE]

## 1. Introduction
- **Project Name:** E-Commerce Furniture Marketplace - [MEUBEL HOUSE FURNITURE]
- **Purpose:** This document outlines the technical requirements for the e-commerce platform, focusing on frontend, backend, and third-party integrations.

## 2. Technical Requirements

### 2.1 Frontend Requirements
- User-friendly interface for browsing products.
- Responsive design for mobile and desktop.
- Key pages:
  - Home
  - Product Listing
  - Product Details
  - Cart
  - Checkout
  - Order Confirmation

### 2.2 Backend Requirements
- Use **Sanity CMS** to manage:
  - Product data
  - Customer data
  - Order details
- Design schemas in **Sanity CMS** for:
  - Products {name,type,title,field{id,name,description,price,stock,category,images}}
  - Orders {name,type,title,field{orderID,customerID,orderDate,productList,TotalAmount,payment,deliveryStatus}}
  - Customer{name,type,title,field{id,name,email,phone,address,orderHistory,accountCreationDate}}
  - Delivery Zone{name,type,title,field{id,regionName,Deliveryfee,deliveryTime,SupportedProduct}}
  - Payment Method{name,type,title,field{id,methodName,TransactionFee,Currency}}
  - Admins{name,type,title,field{adminID,name,email,Role}}
  - Reviews And Rating{name,type,title,field{reviewID,customerID,productID,rating,comment}}

### 2.3 Third-Party API Integration
- **Shipment Tracking API:**
  - Fetch real-time shipment details.
- **Payment Gateway Integration:**
  - Securely process transactions.

#### 2.4 Communication
  - The frontend will interact with the sanity through http requests (GET, POST, PUT, DELETE).

## 3. System Workflow
1. User visits the marketplace frontend to browse products.
2. Frontend makes requests to Sanity CMS APIs to fetch product details.
3. User places an order:
   - Order details sent to Sanity CMS.
   - Shipment tracking via third-party APIs.
   - Payment processed securely through payment gateway.
4. Data displayed dynamically to the user in real-time.

## 4. API Endpoints
### Example Endpoints:
- **GET /products:** Fetch all available products.
- **POST /orders:** Create a new order.
-  **put /shipment:** update order shipment status.
- **GET /shipment:** Fetch order shipment status.

## 5. Conclusion
- This document serves as a foundation for building the platform's technical architecture.


# FLOW CHART 

Customer
   |
   v
Browse Products
   |
   v
Add to Cart
   |
   v
Place Order
   |
   v
Select Payment Method  <---->  Provide Shipping Details
   |
   v
Order Placed
   |
   v
Backend (Sanity CMS)
   |            |            |            |
   v            v            v            v
Products      Orders      Customers    Delivery Zones
   |
   v
Order Shipment
   |
   v
Shipment Tracking
   |
   v
Customer Feedback


# Visual Data Chart
here is the link  (https://app.eraser.io/workspace/XAkwLZRUUQ11VcutJkax?origin=share).

The MEUBEL HOUSE FURNITURE e-commerce platform combines a responsive frontend with a robust backend managed by Sanity CMS, handling product, customer, and order data. It integrates third-party APIs for secure payment processing and real-time shipment tracking, ensuring a seamless user experience from browsing to order fulfillment. The system architecture is designed for efficiency, scalability, and real-time data synchronization, forming the foundation of a modern online furniture marketplace.



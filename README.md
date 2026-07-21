# 🛒 ShopCloud — AWS Serverless E-Commerce Platform

![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![AWS](https://img.shields.io/badge/AWS-12%2B%20Services-orange?logo=amazon-aws)
![Status](https://img.shields.io/badge/status-live-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)
![Serverless](https://img.shields.io/badge/serverless-Lambda%20%7C%20DynamoDB%20%7C%20SQS-yellow)
![Cognito](https://img.shields.io/badge/auth-AWS%20Cognito-orange)

> A fully serverless, cloud-native e-commerce platform built on AWS — featuring real-time order processing, AI-powered notifications, role-based authentication, and a complete seller dashboard.

---

## 🌐 Live Demo

🔗 **[Visit ShopCloud](https://shopcloud-rizaa.netlify.app)**

---

## 📸 Screenshots

| Home Page | Product Detail | Checkout |
|---|---|---|
| Product catalog loaded from DynamoDB | Real product images from S3 | Full order form with payment options |

| Seller Dashboard | My Orders | Order Email |
|---|---|---|
| Add, edit, delete products | Customer order history | Real confirmation via SES |

---

## ☁️ AWS Architecture

```
User (Browser)
      │
      ▼
Netlify (HTTPS Frontend)
      │
      ├── Cognito (Authentication — Buyer/Seller roles)
      │
      ▼
API Gateway (REST API)
      │
      ├── GET  /products    → Lambda → DynamoDB (Products)
      ├── POST /products    → Lambda → DynamoDB (Add product)
      ├── POST /products/update → Lambda → DynamoDB (Edit product)
      ├── POST /products/delete → Lambda → DynamoDB (Delete product)
      ├── GET  /orders      → Lambda → DynamoDB (Orders)
      ├── POST /orders      → Lambda → SQS Queue
      └── POST /orders/status → Lambda → DynamoDB (Update status)
                │
                ▼
           SQS Queue (shopcloud-orders-queue)
                │
                ▼
         process-order Lambda
                │
                ├── Step Functions Workflow
                │     ├── ValidateOrder Lambda
                │     ├── SendConfirmationEmail Lambda → SES (Buyer email)
                │     └── UpdateOrderStatus Lambda → DynamoDB
                │
                └── SNS Topic → Seller email notification
                
S3 Bucket → Product images storage
CloudWatch → Monitoring + Alarms
IAM → Security roles and least-privilege policies
SQS DLQ → Failed order handling
```

---

## 🛠️ AWS Services Used (12 Services)

| # | Service | Purpose |
|---|---|---|
| 1 | **Amazon S3** | Frontend hosting + product image storage |
| 2 | **Amazon Cognito** | User authentication — Buyer and Seller roles |
| 3 | **Amazon DynamoDB** | NoSQL database for products and orders |
| 4 | **AWS Lambda** | 8 serverless backend functions |
| 5 | **Amazon API Gateway** | REST API endpoints with CORS |
| 6 | **Amazon SES** | Order confirmation emails to buyers |
| 7 | **Amazon SQS** | Async order processing queue |
| 8 | **Amazon SQS DLQ** | Dead letter queue for failed orders |
| 9 | **AWS Step Functions** | Order workflow orchestration |
| 10 | **Amazon SNS** | Real-time seller notifications |
| 11 | **Amazon CloudWatch** | Monitoring, logging and alarms |
| 12 | **AWS IAM** | Security roles and least-privilege access |

---

## 🚀 Features

### Customer (Buyer) Features
- 🔐 Secure signup and login with email verification (Cognito)
- 🛍️ Browse product catalog loaded from real DynamoDB database
- 🔍 Search and filter products by category
- 📱 Responsive product detail pages with real S3 images
- 🛒 Add to cart and manage cart items
- 💳 Complete checkout with delivery address and payment method
- 📦 Real-time order processing with async SQS queue
- 📧 Automatic order confirmation email via SES
- 📋 View order history with live status updates
- ❌ Out of stock products clearly marked and disabled

### Seller (Admin) Features
- 🏪 Dedicated seller dashboard with sales statistics
- ➕ Add new products with images, pricing and descriptions
- ✏️ Edit existing products including stock status
- 🗑️ Delete products from catalog
- 📊 View all customer orders with details
- 🔄 Update order status (Confirmed → Shipped → Delivered)
- 🔔 Real-time SNS email notification for every new order

### Technical Features
- ⚡ Fully serverless — no servers to manage
- 🔄 Event-driven order processing with SQS + Step Functions
- 👥 Role-based access control (Buyers vs Sellers via Cognito groups)
- 📊 CloudWatch monitoring with error alarms
- 🛡️ IAM least-privilege security on all Lambda functions
- 💀 Dead letter queue for failed order handling
- 🌐 HTTPS delivery via Netlify CDN

---

## 📁 Project Structure

```
aws-ecommerce-platform/
├── frontend/
│   ├── index.html        # Home page — product catalog
│   ├── product.html      # Product detail page
│   ├── cart.html         # Shopping cart
│   ├── checkout.html     # Order placement
│   ├── login.html        # Cognito authentication
│   ├── orders.html       # Customer order history
│   ├── seller.html       # Seller dashboard
│   └── style.css         # Global styles
└── README.md
```

---

## ⚙️ Lambda Functions

| Function | Trigger | Purpose |
|---|---|---|
| `shopcloud-get-products` | API Gateway GET /products | Fetch all products from DynamoDB |
| `shopcloud-add-product` | API Gateway POST /products | Add new product to DynamoDB |
| `shopcloud-update-product` | API Gateway POST /products/update | Edit product details |
| `shopcloud-delete-product` | API Gateway POST /products/delete | Remove product |
| `shopcloud-place-order` | API Gateway POST /orders | Save order + push to SQS |
| `shopcloud-process-order` | SQS trigger | Start Step Functions workflow |
| `shopcloud-get-orders` | API Gateway GET /orders | Fetch orders by email |
| `shopcloud-update-order-status-api` | API Gateway POST /orders/status | Update order status |

---

## 🔄 Order Processing Flow

```
1. Customer places order on checkout page
        ↓
2. place-order Lambda saves to DynamoDB (PENDING)
        ↓
3. Order message sent to SQS queue
        ↓
4. process-order Lambda triggered by SQS
        ↓
5. Step Functions workflow starts:
   ├── ValidateOrder — validates order data
   ├── SendConfirmationEmail — SES email to buyer
   └── UpdateOrderStatus — DynamoDB status → CONFIRMED
        ↓
6. SNS notification sent to seller email
        ↓
7. Seller updates status (Shipped/Delivered) from dashboard
        ↓
8. Customer sees updated status on My Orders page
```

---

## 🔐 Authentication Flow

```
User visits site
      ↓
Login/Signup via Cognito
      ↓
Email verification (OTP)
      ↓
JWT token issued
      ↓
API Gateway validates token on every request
      ↓
Cognito group check:
  ├── "Buyers" group → redirected to index.html
  └── "Sellers" group → redirected to seller.html
```

---

## 🗄️ DynamoDB Tables

### shopcloud-products
| Attribute | Type | Description |
|---|---|---|
| productId (PK) | String | Unique product ID |
| name | String | Product name |
| brand | String | Brand name |
| category | String | Product category |
| price | Number | Current price |
| originalPrice | Number | Original price |
| discount | String | Discount percentage |
| rating | Number | Product rating (1-5) |
| ratingCount | String | Number of ratings |
| image | String | S3 image URL |
| description | String | Product features |
| inStock | Boolean | Stock availability |

### shopcloud-orders
| Attribute | Type | Description |
|---|---|---|
| orderId (PK) | String | Unique order ID |
| customerName | String | Buyer name |
| customerEmail | String | Buyer email |
| items | List | Ordered items |
| total | Number | Order total |
| address | String | Delivery address |
| status | String | PENDING/CONFIRMED/SHIPPED/DELIVERED |
| createdAt | String | Order timestamp |

---

## 📊 CloudWatch Monitoring

- **ShopCloud-Lambda-Errors** — Alerts when Lambda function errors occur
- **ShopCloud-High-Queue-Depth** — Alerts when SQS queue exceeds 10 messages
- All Lambda functions log to CloudWatch Logs automatically

---

## 🏗️ Setup Guide

### Prerequisites
- AWS Account with Free Tier
- Node.js installed
- Git and GitHub account

### AWS Services to Configure
1. Create Cognito User Pool with Buyers and Sellers groups
2. Create DynamoDB tables (shopcloud-products, shopcloud-orders)
3. Create S3 bucket for product images
4. Deploy Lambda functions with IAM role
5. Configure API Gateway with CORS
6. Set up SQS queues (main + DLQ)
7. Create Step Functions state machine
8. Verify email in SES
9. Create SNS topic and subscribe seller email
10. Set up CloudWatch alarms

### Frontend Deployment
```bash
# Clone the repository
git clone https://github.com/rizariyas8/aws-ecommerce-platform

# Update API_URL in all HTML files with your API Gateway URL
# Update Cognito User Pool ID and Client ID in login.html and all pages

# Deploy to Netlify by dragging frontend folder
# OR upload to S3 and enable static website hosting
```

---

## 💡 Key Technical Decisions

| Decision | Reason |
|---|---|
| Serverless over EC2 | No server management, pay per request, auto-scaling |
| DynamoDB over RDS | Flexible schema for varying product attributes, auto-scaling |
| SQS for orders | Decouples order placement from processing, no order loss on failure |
| Step Functions | Visual workflow, automatic retries, error handling per step |
| Cognito over custom auth | Enterprise-grade security without building from scratch |
| SNS for seller alerts | Fan-out notifications, multiple subscriber support |

---

## 👩‍💻 Developer

**Riza Riyas**
- 🎓 BCA Graduate — Jamia Hamdard University
- 📜 Diploma in Cloud Computing — Technodot Academy
- 💼 Cloud Associate Intern — Febno Technologies
- 🐙 GitHub: [@rizariyas8](https://github.com/rizariyas8)

---

## 📄 License

This project is licensed under the MIT License.

---

*Built with ❤️ on AWS — ShopCloud © 2026*

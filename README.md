# AWS E-Commerce Platform — ShopCloud

A fully serverless e-commerce platform built on AWS.

## Live Demo
[ShopCloud Website](http://shopcloud-riza-demo.s3-website.ap-south-1.amazonaws.com/index.html)

## AWS Services Used (10+)
- **S3** — Frontend static website hosting
- **CloudFront** — CDN for fast global delivery
- **Cognito** — User authentication (signup, login, email verification)
- **DynamoDB** — NoSQL database for products and orders
- **Lambda** — Serverless backend functions (get products, place order, process order)
- **API Gateway** — REST API endpoints
- **SES** — Order confirmation emails
- **SQS** — Order queue for async processing
- **SQS DLQ** — Dead letter queue for failed orders
- **IAM** — Security roles and permissions
- **CloudWatch** — Monitoring and logging

## Architecture
User → S3+CloudFront (frontend) → Cognito (auth) → API Gateway → Lambda → DynamoDB
Order flow: Checkout → Lambda → SQS → Process Lambda → SES email

## Features
- Browse products loaded from real DynamoDB database
- User signup and login with email verification
- Add to cart and checkout
- Real order processing with confirmation email
- Async order processing via SQS queue
- Dead letter queue for failed order handling

## Pages
- index.html — Home page with product catalog
- product.html — Product detail page
- cart.html — Shopping cart
- checkout.html — Order placement with real API
- login.html — Cognito authentication

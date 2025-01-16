# System Architecture Overview

## High-Level Diagram

[Frontend (Next.js)]
|
v
[Sanity CMS] <--------> [Product Data (Mock) API]
| ^ | |
[Third-Party APIs] <----> [Shipment Tracking API (ShipEngine)]
|
v
[Payment Gateway (Stripe)]
|
v
[Authentication (Clerk)]

yaml
Copy
Edit

---

# Component Descriptions

## Frontend (Next.js):
- Provides a responsive and interactive user interface for browsing products, managing orders, and handling user authentication.
- Fetches and displays data from the backend APIs in real-time.

## Sanity CMS:
- Acts as a centralized backend for managing product information, user data, order records, and inventory.
- Exposes APIs for dynamic data communication with the frontend.

## Third-Party APIs:
1. **Shipment Tracking API (ShipEngine):**  
   - Fetches real-time shipping updates and generates tracking details.
2. **Payment Gateway (Stripe):**  
   - Processes secure transactions and confirms payment status.

## Authentication (Clerk):
- Handles user registration, login, and session management.
- Integrates with Sanity CMS to securely store user data.

---

# Key Workflows

## User Registration
### Process:
1. User signs up via the frontend using Clerk.
2. Registration details are securely stored in Sanity CMS.

## Product Browsing
### Process:
1. User navigates through product categories on the frontend.
2. Sanity CMS API fetches product data (e.g., name, price, stock, description, images).
3. Dynamic product listings are displayed on the frontend.

## Order Placement
### Process:
1. User adds products to the cart and proceeds to checkout.
2. Order details (products, quantities, shipping address) are sent to Sanity CMS.
3. Payment is processed via Stripe.
4. A confirmation message is sent to the user's email, and the order is recorded in Sanity CMS.

## Shipment Tracking
### Process:
1. Shipment details are updated in Sanity CMS using ShipEngine.
2. Real-time tracking information is displayed to the user on the frontend.

## Inventory Management
### Process:
1. Product stock levels are managed in Sanity CMS.
2. Real-time stock updates are fetched from Sanity CMS.
3. Out-of-stock products are displayed as unavailable or are added to the wishlist.

---

# Category-Specific Instructions

## General eCommerce
### Focus on:
- Standard product browsing, cart management, wishlist, and order placement.
- Inventory management and real-time stock updates.

### Workflow Example:
- **Endpoint:** `/products`  
- **Method:** `GET`  
- **Purpose:** Fetch all product listings.

#### JSON Response Example:
```json
[
  {
    "name": "Product Name",
    "slug": "product-slug",
    "image": "https://product1.png",
    "additionalImages": ["https://product2.png", "https://product3.png"],
    "description": "Product Description",
    "price": 100,
    "discountPrice": 95,
    "inStock": true,
    "stock": 25,
    "rating": 4.6,
    "reviews": 10
  }
]
 

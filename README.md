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
# API Endpoints
## Endpoint              ## Method                    ## Purpose                                           ## Response Example
/products                GETFetch all               product details                               [ { "name": "Product Name", "slug": "product-slug", "price": 100 } ]
/order                     POST                      Submit new order details                        { "orderId": 123, "status": "success" }
/shipment-tracing        GETFetch                 real-time tracking updates                        { "trackingId": "abc123", "status": "In Transit" }
/delivery-status         GETFetch                express delivery tracking information              { "orderId": 456, "deliveryTime": "30 mins" }
/inventory               GETFetch                   real-time stock levels                            { "productId": 789, "stock": 50 }
/cart                      POST                    Add product to cart                                   { "cartId": 125, "items": [...] }
/wishlist                  POST                     Add product to wishlist                             { "wishlistId": 202, "items": [...] }


# Sanity Schema Example
## Product Schema:

import { CartIcon } from "@sanity/icons";
import { defineField, defineType } from "sanity";

export const productTypes = defineType({
name: "product",
title: "Products",
type: "document",
icon: TrolleyIcon,
fields: [
defineField({
name: "name",
title: "Product Name",
type: "string",
validation: (Rule) => Rule.required(),
}),
defineField({
name: "slug",
title: "Slug",
type: "slug",
options: {
source: "name",
maxLength: 96,
},
validation: (Rule) => Rule.required(),
}),
defineField({
name: "image",
title: "Product Image",
type: "image",
options: {
hotspot: true,
},
validation: (Rule) => Rule.required(),
}),
defineField({
name: "additionalImages",
title: "Additional Images",
type: "array",
of: [{ type: "image", options: { hotspot: true } }],
}),
defineField({
name: "description",
title: "Description",
type: "blockContent",
}),
defineField({
name: "price",
title: "Price",
type: "number",
validation: (Rule) => Rule.required().min(0),
}),
defineField({
name: "discountPrice",
title: "Discount Price",
type: "number",
description: "Discounted price of the product (optional)",
validation: (Rule) =>
Rule.custom((discountPrice, context) => {
const doc = context.document;
if (doc && typeof doc.price === "number") {
if (discountPrice && discountPrice >= doc.price) {
return "Discount price must be less than the original price";
}
}
return true;
}),
}),
defineField({
name: "inStock",
title: "In Stock",
type: "boolean",
description: "Indicates whether the product is currently in stock",
initialValue: true,
validation: (Rule) => Rule.required(),
}),
defineField({
name: "stock",
title: "Stock Quantity",
type: "number",
description: "Number of items available in stock",
validation: (Rule) => Rule.required().min(0),
}),
defineField({
name: "rating",
title: "Rating",
type: "number",
description: "Rating from 0 to 5",
validation: (Rule) => Rule.min(0).max(5).precision(1),
}),
defineField({
name: "reviews",
title: "Reviews Count",
type: "number",
description: "Number of reviews for the product",
validation: (Rule) => Rule.required().min(0),
}),
],
preview: {
select: {
title: "name",
media: "image",
subtitle: "price",
inStock: "inStock",
stock: "stock",
},
prepare({ title, subtitle, media, inStock, stock }) {
return {
title,
subtitle: `$${subtitle} | ${inStock ? `In Stock (${stock})` : "Out of Stock"}`,
media,
};
},
},
});

## Technical Roadmap

## Development Phase

### Authentication:
- Implement user registration and login using Clerk.
- Integrate Clerk with Sanity CMS for user data storage.
### Product Management:
- Create mock API for product data.
- Store product data in Sanity CMS.
- Fetch and display product data on dynamic frontend pages.
- Cart and Wishlist
- Implement add-to-cart functionality with real-time stock checks.
- Allow out-of-stock products to be added to wishlist.
- Display total bill and proceed to checkout button on cart page.

### Payment Integration:
- Integrate Stripe for secure payments.
- Use Stripe test account for development.
- Handle payment success and failure scenarios
- 
### Shipment Tracking:
- Integrate ShipEngine for shipment tracking.
- Generate tracking numbers and display on the frontend.
- Allow users to track their orders in real-time.
### Inventory Management:
- Create API for real-time stock updates in Sanity CMS.
- Update stock levels upon order placement.
- Prevent out-of-stock products from being added to cart.
## Testing Phase:
### End-to-End Testing:
- Test all workflows, including user registration, product browsing, cart management, checkout, and
shipment tracking.
- Validate API responses and ensure data accuracy.
### Security Audits:
- Conduct security audits for sensitive data handling, including user authentication and payment processing.
### Launch Phase:
### Deployment:
- Deploy the platform on a cloud hosting service (e.g., Vercel, Netlify).
- Monitor user feedback and optimize for performance.

### Post-Launch:
- Collect user feedback for continuous improvement.
- Optimize API performance and frontend loading times.
- Scale infrastructure based on traffic and demand
## Conclusion:
 This technical foundation outlines the architecture, workflows, and API endpoints for the Bandage Online
Shopping platform. By following the enhanced workflow and technical roadmap, the platform will provide a
seamless eCommerce experience with robust authentication, inventory management, and shipment tracking
features
 
 
 

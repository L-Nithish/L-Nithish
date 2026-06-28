# Case Study: DexterStore — Decoupled E-Commerce Marketplace

## 1. Executive Summary
**DexterStore** (digital-marketing) is a decoupled, high-performance e-commerce storefront designed for selling web templates, code assets, and UI packages. It features transactional cart persistence, real-time product filtering, and a secure administrative panel.

* **Live Demo:** [https://dexterstore.netlify.app](https://dexterstore.netlify.app)
* **GitHub Repository:** [https://github.com/L-Nithish/DexterStore](https://github.com/L-Nithish/DexterStore)

---

## 2. System Architecture
DexterStore is built on a clean separation of concerns, decoupling the frontend user experience from the transactional backend:

```
┌────────────────────────────────────────────────────────┐
│                   React Storefront                     │
│  - Hosted on Netlify (Stateless & Cacheable)           │
│  - Tailwind CSS Styling                                │
└──────────────────────────┬─────────────────────────────┘
                           │
                      RESTful HTTPS
                           │
┌──────────────────────────▼─────────────────────────────┐
│                 Spring Boot API Engine                 │
│  - Hosted on Render                                    │
│  - Spring Security & JWT Filter                        │
│  - Hibernate ORM (ACID Transactions)                   │
└──────────────────────────┬─────────────────────────────┘
                           │
                         JDBC
                           │
┌──────────────────────────▼─────────────────────────────┐
│                   PostgreSQL Database                  │
│  - Storing user accounts, products, and cart items     │
└────────────────────────────────────────────────────────┘
```

---

## 3. Engineering Challenges & Solutions

### Challenge 1: Cart State Loss & Session Management
E-commerce users frequently abandon carts due to session expiration or page refreshes, resulting in lost transactional data.
* **Solution:** Implemented **Transactional Cart Persistence** on the backend. Instead of relying solely on local storage, cart items are synchronized with the PostgreSQL database in real-time via Hibernate ORM, binding cart states to authenticated user sessions securely.

### Challenge 2: Slow Product Catalog Loading
As the catalog grows, querying the database repeatedly for filters (by category, price, rating) causes performance bottlenecks.
* **Solution:** Structuring the React storefront to be completely stateless and caching catalog results. Implemented database indexing on frequently filtered fields (`price`, `category`) to reduce query times.

---

## 4. Technical Decisions & Key Features

* **Hibernate for ACID Compliance:** Selected Hibernate to manage database transactions, ensuring that inventory counts and user checkouts are processed under strict ACID transactions.
* **JWT-Based Admin Dashboard:** Implemented role-based access control (RBAC) via Spring Security. Administrative endpoints (adding/editing products) require valid JWTs with admin roles, while public catalog endpoints remain open.
* **Decoupled Architecture:** Deploying the React frontend on Netlify and the Spring Boot backend on Render ensures that frontend traffic spikes do not affect database transaction performance.

---

## 5. Performance Metrics
* **Catalog Filter Speed:** `<20ms` for multi-layered catalog filters.
* **Cart Sync Latency:** `<45ms` for adding items and synchronizing with the server.
* **Uptime:** `99.9%` achieved by hosting frontend and backend on separate cloud architectures.

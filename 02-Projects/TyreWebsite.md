# 🚗 TyreWebsite — Project Deep Dive

> An e-commerce platform for tyres with vehicle fitment matching, brand comparison, and dealer network integration.

---

## 🏗️ Project Overview

**TyreWebsite** is a full-stack e-commerce platform that helps vehicle owners find the **right tyre for their vehicle**, compare brands and prices, and place orders for home delivery or fitting at a nearby dealer.

**Project Type:** E-Commerce Web Application
**Role:** Full Stack Developer
**Timeline:** [Add your actual dates]
**Status:** [Active / Completed / MVP]

---

## 🎯 Problem Statement

Buying tyres online is complicated because:
- Vehicle owners don't know their exact tyre specifications
- Hundreds of brands and sizes make comparison difficult
- Fitment verification (will this tyre fit my car?) requires expertise
- No reliable online platform for Indian tyre market
- Complex installation logistics — tyres need to be fitted at a service point

**TyreWebsite solves these by:**
- Vehicle-based tyre search (select vehicle → see compatible tyres)
- Side-by-side tyre comparison
- Integrated dealer network for fitting services
- Transparent pricing with brand/model filters

---

## 🛠️ Tech Stack

| Layer | Technology | Reason |
|-------|-----------|--------|
| Frontend | Next.js + Tailwind CSS | SSR for SEO, fast page loads |
| Backend | Next.js API Routes / Node.js | Unified codebase |
| Database | PostgreSQL | Relational product catalog |
| ORM | Prisma | Type-safe DB queries |
| Cache | Redis | Product catalog caching |
| Search | Elasticsearch / MeiliSearch | Fast product search |
| Payments | Razorpay | Indian payment gateway |
| Storage | AWS S3 + CloudFront | Product images via CDN |
| Auth | NextAuth.js + JWT | Simplified auth |
| Deployment | Vercel | Next.js native platform |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────┐
│           Next.js Frontend          │
│  SSR Pages + API Routes             │
│  Product Listing, Cart, Checkout    │
└────────────────────┬────────────────┘
                     │ Internal API
┌────────────────────▼────────────────┐
│            API Layer                 │
│  /api/products  /api/cart           │
│  /api/orders    /api/dealers        │
└───────┬──────────────┬──────────────┘
        │              │
┌───────▼──┐    ┌──────▼────┐    ┌────────────┐
│PostgreSQL│    │   Redis   │    │Elasticsearch│
│(Prisma)  │    │  Cache    │    │  Search    │
└──────────┘    └───────────┘    └────────────┘
```

---

## 🗄️ Database Design

### Products (Tyres) Table
```sql
CREATE TABLE tyres (
    id              SERIAL PRIMARY KEY,
    brand           VARCHAR(50),       -- MRF, CEAT, Apollo, Bridgestone
    model           VARCHAR(100),      -- ZVTS, Milaze, Amazer
    width           INTEGER,           -- 185
    aspect_ratio    INTEGER,           -- 65
    rim_size        INTEGER,           -- 15 (inches)
    sku             VARCHAR(50) UNIQUE,
    price           DECIMAL(10,2),
    mrp             DECIMAL(10,2),
    discount_pct    INTEGER DEFAULT 0,
    load_index      INTEGER,           -- 88
    speed_rating    CHAR(2),           -- H, V, W
    fuel_efficiency VARCHAR(5),        -- A-G rating
    wet_grip        VARCHAR(5),        -- A-G rating
    noise_level     INTEGER,           -- dB
    stock           INTEGER DEFAULT 0,
    images          TEXT[],
    created_at      TIMESTAMP DEFAULT NOW()
);

-- Size format: 185/65 R15 (width/aspect_ratio R rim_size)
-- This translates to a tyre specification
```

### Vehicle-Tyre Fitment Table
```sql
CREATE TABLE vehicle_fitments (
    id          SERIAL PRIMARY KEY,
    make        VARCHAR(50),    -- Maruti, Honda
    model       VARCHAR(50),    -- Swift, City
    variant     VARCHAR(100),   -- VXI, VDI
    year_from   INTEGER,
    year_to     INTEGER,
    front_size  VARCHAR(20),    -- 185/65/R15
    rear_size   VARCHAR(20)     -- Same or different for sports cars
);
```

### Orders Table
```sql
CREATE TABLE orders (
    id              SERIAL PRIMARY KEY,
    user_id         INTEGER REFERENCES users(id),
    total_amount    DECIMAL(10,2),
    status          VARCHAR(30) DEFAULT 'pending',
    delivery_type   VARCHAR(20),    -- home_delivery | dealer_pickup
    dealer_id       INTEGER,
    address_id      INTEGER,
    razorpay_id     VARCHAR(100),
    placed_at       TIMESTAMP DEFAULT NOW()
);
```

---

## 🔍 Vehicle-Based Tyre Search

The core feature: user selects their vehicle, we show compatible tyres.

```javascript
// API: /api/tyres/by-vehicle
async function getTyresByVehicle(make, model, year, variant) {
    // 1. Find fitment spec for this vehicle
    const fitment = await prisma.vehicleFitment.findFirst({
        where: {
            make,
            model,
            variant,
            year_from: { lte: year },
            year_to: { gte: year }
        }
    });
    
    // 2. Parse size: "185/65/R15" → width=185, ratio=65, rim=15
    const [width, ratio, rim] = parseTyreSize(fitment.front_size);
    
    // 3. Find compatible tyres
    return prisma.tyre.findMany({
        where: { width, aspect_ratio: ratio, rim_size: rim, stock: { gt: 0 } },
        orderBy: { price: 'asc' }
    });
}
```

---

## 🛒 Cart & Checkout Flow

```
Browse → Select Tyre → Add to Cart
Cart → Login (if not) → Select Delivery Type
     → Home Delivery: Enter Address
     → Dealer Fitting: Select nearby dealer + appointment
→ Payment (Razorpay) → Order Confirmation → Email/SMS
```

---

## 📡 API Design

| Method | Endpoint | Description |
|--------|---------|-------------|
| GET | `/api/vehicles/makes` | All vehicle makes |
| GET | `/api/vehicles/:make/models` | Models for a make |
| GET | `/api/tyres/by-vehicle` | Fitment-matched tyres |
| GET | `/api/tyres/search` | Text search + filters |
| GET | `/api/tyres/compare` | Compare 2-3 tyres |
| GET | `/api/dealers/nearby` | Dealers near location |
| POST | `/api/cart/add` | Add item to cart |
| POST | `/api/orders/create` | Place order |
| POST | `/api/orders/verify-payment` | Razorpay payment verification |

---

## ⚠️ Challenges & Solutions

| Challenge | Solution |
|-----------|---------|
| Vehicle fitment database | Manually curated 500+ vehicle records + import from open dataset |
| Product search performance | ElasticSearch with filters for size, brand, price range |
| Large product catalog | Redis cache for popular queries (TTL: 10 minutes) |
| SEO for product pages | Next.js SSR with dynamic metadata + sitemap.xml |
| Razorpay payment verification | Server-side HMAC signature verification |

---

## 🚀 Future Improvements

- [ ] AR tyre preview (see how the tyre looks on your car)
- [ ] Tyre health monitoring integration (OBD2 sensors)
- [ ] Subscription for periodic tyre check reminders
- [ ] Bulk ordering for fleet companies
- [ ] Reseller/dealer B2B portal

---

## ❓ Interview Questions & Answers

**Q: How did you implement the vehicle-to-tyre fitment system?**
> I built a `vehicle_fitments` table that maps each make/model/variant/year combination to front and rear tyre sizes. When a user selects their vehicle, I query this table and use the tyre size spec to filter the `tyres` table. This is a simple relational join but covers thousands of vehicle variants.

**Q: How did you optimize the product listing for performance?**
> I used Next.js with `getStaticProps` for the brand/category pages (cached at build time) and `getServerSideProps` for filtered search results. Popular search queries (e.g., top 10 most searched tyre sizes) are cached in Redis for 10 minutes.

**Q: How did you handle payment integration?**
> I integrated Razorpay with server-side signature verification. When payment is completed client-side, the client sends `razorpay_payment_id`, `razorpay_order_id`, and `razorpay_signature` to the server. The server verifies the HMAC-SHA256 signature to confirm payment authenticity before fulfilling the order.

**Q: What would you do differently if rebuilding this?**
> I'd add an inventory management system from the start. Currently, stock is a simple integer counter which can have race conditions under high load. I'd use database-level locking or optimistic concurrency control to prevent overselling.

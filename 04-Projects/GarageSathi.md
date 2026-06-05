# 🔧 GarageSathi — Project Deep Dive

> A platform connecting vehicle owners with nearby verified garages for service booking, emergency assistance, and transparent pricing.

---

## 🏗️ Project Overview

**GarageSathi** is a two-sided marketplace platform connecting **vehicle owners** with **local garages and mechanics**. It solves the problem of finding trustworthy auto repair services, getting fair pricing, and managing vehicle maintenance history — all in one app.

**Project Type:** Full-Stack Web + Mobile App
**Role:** Full Stack Developer
**Timeline:** [Add your actual dates]
**Status:** [Active / Completed / MVP]

---

## 🎯 Problem Statement

Vehicle owners in India face:
- Difficulty finding trusted local garages (especially in new cities)
- Lack of price transparency — mechanics often overcharge
- No way to track vehicle service history
- No emergency assistance booking for breakdowns
- No reviews/ratings system for garages

**GarageSathi solves these by:**
- Listing verified garages with ratings and reviews
- Showing estimated prices for common services upfront
- Enabling online appointment booking
- Providing emergency SOS for breakdowns with live mechanic tracking
- Maintaining complete vehicle service history

---

## 🛠️ Tech Stack

| Layer | Technology | Reason |
|-------|-----------|--------|
| Frontend | React.js + Tailwind CSS | Fast, responsive UI |
| Mobile | React Native | Cross-platform mobile |
| Backend | Node.js + Express.js | REST API |
| Database | PostgreSQL | Relational data (bookings, users, garages) |
| Cache | Redis | Session cache, OTP storage |
| Real-time | Socket.IO | Live mechanic tracking |
| Maps | Google Maps API | Garage discovery, route |
| Payments | Razorpay | Indian payment gateway |
| Notifications | Firebase FCM | Push notifications |
| Storage | AWS S3 | Invoice and photo storage |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────┐
│                  Client Layer                    │
│         React.js (Web) + React Native (App)     │
└────────────────────┬────────────────────────────┘
                     │ REST API + WebSocket
┌────────────────────▼────────────────────────────┐
│               Backend Layer                      │
│          Node.js + Express.js                    │
│  ┌──────────┐  ┌──────────┐  ┌───────────────┐ │
│  │  Auth    │  │ Booking  │  │  Notification │ │
│  │ Service  │  │ Service  │  │   Service     │ │
│  └──────────┘  └──────────┘  └───────────────┘ │
└───────┬──────────────┬──────────────────────────┘
        │              │
┌───────▼──┐    ┌──────▼────┐    ┌────────────┐
│PostgreSQL│    │   Redis   │    │ Socket.IO  │
│          │    │  (Cache)  │    │ (Real-time)│
└──────────┘    └───────────┘    └────────────┘
```

---

## 🗄️ Database Design (PostgreSQL)

### Users Table
```sql
CREATE TABLE users (
    id          SERIAL PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    phone       VARCHAR(15) UNIQUE NOT NULL,
    email       VARCHAR(100) UNIQUE,
    role        VARCHAR(20) DEFAULT 'owner',  -- owner | garage_admin | mechanic
    password    VARCHAR(255),
    created_at  TIMESTAMP DEFAULT NOW()
);
```

### Vehicles Table
```sql
CREATE TABLE vehicles (
    id          SERIAL PRIMARY KEY,
    user_id     INTEGER REFERENCES users(id),
    make        VARCHAR(50),       -- Maruti, Honda
    model       VARCHAR(50),       -- Swift, City
    year        INTEGER,
    reg_number  VARCHAR(20) UNIQUE,
    fuel_type   VARCHAR(20),       -- petrol | diesel | electric | CNG
    created_at  TIMESTAMP DEFAULT NOW()
);
```

### Garages Table
```sql
CREATE TABLE garages (
    id              SERIAL PRIMARY KEY,
    name            VARCHAR(100) NOT NULL,
    owner_id        INTEGER REFERENCES users(id),
    address         TEXT,
    latitude        DECIMAL(10, 8),
    longitude       DECIMAL(11, 8),
    rating          DECIMAL(2, 1) DEFAULT 0,
    is_verified     BOOLEAN DEFAULT false,
    specializations TEXT[],    -- ['car', 'two-wheeler', 'EV']
    working_hours   JSONB,
    created_at      TIMESTAMP DEFAULT NOW()
);
```

### Bookings Table
```sql
CREATE TABLE bookings (
    id              SERIAL PRIMARY KEY,
    user_id         INTEGER REFERENCES users(id),
    garage_id       INTEGER REFERENCES garages(id),
    vehicle_id      INTEGER REFERENCES vehicles(id),
    service_type    VARCHAR(100),
    scheduled_at    TIMESTAMP,
    status          VARCHAR(20) DEFAULT 'pending',
    estimated_cost  DECIMAL(10, 2),
    final_cost      DECIMAL(10, 2),
    payment_status  VARCHAR(20) DEFAULT 'unpaid',
    razorpay_id     VARCHAR(100),
    notes           TEXT,
    created_at      TIMESTAMP DEFAULT NOW()
);
```

---

## 🔐 Authentication

- **OTP-based login** (no password needed, phone = identity)
- **Redis** stores OTP with 5-minute TTL
- **JWT** access token (1 hour) + refresh token (30 days) in httpOnly cookie
- **Role-based routes**: `owner`, `garage_admin`, `mechanic`, `super_admin`

---

## 📡 API Design

| Method | Endpoint | Description |
|--------|---------|-------------|
| POST | `/api/auth/send-otp` | Send OTP to phone |
| POST | `/api/auth/verify-otp` | Verify OTP, return JWT |
| GET | `/api/garages/nearby` | Garages within radius (lat/lng) |
| GET | `/api/garages/:id` | Garage details + reviews |
| POST | `/api/bookings` | Create a booking |
| PATCH | `/api/bookings/:id/status` | Update booking status |
| GET | `/api/vehicles/:id/history` | Service history |
| POST | `/api/sos` | Emergency assistance request |

### Geospatial Query for Nearby Garages
```sql
SELECT id, name, 
    (6371 * acos(
        cos(radians($1)) * cos(radians(latitude)) *
        cos(radians(longitude) - radians($2)) +
        sin(radians($1)) * sin(radians(latitude))
    )) AS distance
FROM garages
WHERE is_verified = true
HAVING distance < 10   -- within 10 km
ORDER BY distance;
```

---

## ⚡ Real-Time Features (Socket.IO)

```javascript
// Server: emit mechanic location updates
io.on('connection', (socket) => {
    socket.on('mechanic:location', (data) => {
        const { bookingId, lat, lng } = data;
        io.to(`booking:${bookingId}`).emit('mechanic:update', { lat, lng });
    });
});

// Client: subscribe to a booking room
socket.emit('join:booking', { bookingId: '12345' });
socket.on('mechanic:update', (data) => {
    updateMapMarker(data.lat, data.lng);
});
```

---

## ⚠️ Challenges & Solutions

| Challenge | Solution |
|-----------|---------|
| Fake garages / fraud | Manual verification + document upload + admin approval |
| Real-time location accuracy | Socket.IO with 5-second polling interval on mobile |
| Payment failures | Razorpay webhook + idempotency keys for retry safety |
| Dynamic pricing | Base price per service + parts cost (editable by garage admin) |
| Review manipulation | One review per booking; only completed bookings can review |

---

## 🚀 Future Improvements

- [ ] AI-powered diagnosis from symptom description
- [ ] Parts marketplace (order genuine parts through app)
- [ ] Insurance claim assistance integration
- [ ] Mechanic certification and skill verification
- [ ] Subscription plans for fleet owners

---

## ❓ Interview Questions & Answers

**Q: How did you implement the "nearby garages" feature?**
> I stored garage coordinates as `latitude` and `longitude` in PostgreSQL. For nearby search, I used the Haversine formula in SQL to calculate distance between the user's location and each garage, filtering by a configurable radius. For scale, I would add PostGIS extension which provides native spatial indexing.

**Q: How did you handle the real-time mechanic tracking?**
> I used Socket.IO with room-based connections. Each booking gets its own room (e.g., `booking:12345`). The mechanic's mobile app emits location updates every 5 seconds. The customer's app subscribes to the booking room and receives live updates.

**Q: How did you implement the rating system?**
> Ratings are stored per booking (not editable after 24 hours). I calculate the garage's average rating as a running average and update it after each new review using a simple formula: `new_avg = (old_avg * total_reviews + new_rating) / (total_reviews + 1)`.

**Q: What was your biggest technical challenge?**
> Ensuring payment reliability. Payment failures at critical moments (after service) caused disputes. I implemented Razorpay webhooks with idempotency keys so even if the user closes the app, the payment status is eventually reconciled via the webhook.

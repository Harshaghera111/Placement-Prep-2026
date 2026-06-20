# 🌾 GramSathi — Project Deep Dive

> A rural technology platform connecting farmers and village communities with government schemes, local services, and digital resources.

---

## 🏗️ Project Overview

**GramSathi** is a full-stack web and mobile platform designed to bridge the **digital divide in rural India**. It empowers farmers and village communities by providing a unified platform to access government schemes, get farming advice, connect with local service providers, and digitize village records.

**Project Type:** Full-Stack Web Application
**Role:** Full Stack Developer (Solo / Team Lead)
**Timeline:** [Add your actual dates]
**Status:** [Active / Completed / MVP]

---

## 🎯 Problem Statement

Rural India faces significant challenges:
- Farmers are unaware of government schemes they're eligible for
- Language barrier prevents using existing digital platforms (mostly English)
- Lack of a unified platform for village-level services
- Poor digitization of agricultural records and crop data
- Limited access to expert farming advice

**GramSathi solves these by:**
- Providing a vernacular-first interface (Hindi + regional languages)
- Aggregating government scheme eligibility checks in one place
- Connecting farmers with local agronomists and service providers
- Digitizing crop records, land records, and income data

---

## 🛠️ Tech Stack

| Layer | Technology | Reason |
|-------|-----------|--------|
| Frontend | React.js + Tailwind CSS | Component-based, fast UI |
| Backend | Node.js + Express.js | JavaScript full-stack, fast APIs |
| Database | MongoDB | Flexible schema for varied farmer data |
| Authentication | JWT + bcrypt | Secure, stateless auth |
| Storage | AWS S3 / Cloudinary | Document and image uploads |
| Deployment | Vercel (frontend) + Render (backend) | Free tier, CI/CD |
| Language AI | Google Translate API / LibreTranslate | Vernacular support |
| Maps | Google Maps API / Leaflet.js | Village geolocation |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────┐
│              Client Layer                │
│   React.js Web App + Mobile PWA         │
└──────────────────┬──────────────────────┘
                   │ HTTPS / REST API
┌──────────────────▼──────────────────────┐
│             Backend Layer                │
│   Node.js + Express.js API Server       │
│   JWT Auth Middleware                    │
│   Role-based Access Control (RBAC)      │
└──────┬─────────────────────┬────────────┘
       │                     │
┌──────▼──────┐    ┌─────────▼───────────┐
│  MongoDB    │    │   External APIs      │
│  (Atlas)    │    │   - Google Translate │
│             │    │   - Maps API         │
│  Collections│    │   - Scheme DB API   │
│  - Users    │    └─────────────────────┘
│  - Farmers  │
│  - Schemes  │
│  - Records  │
└─────────────┘
```

---

## 🗄️ Database Design

### Users Collection
```json
{
  "_id": "ObjectId",
  "name": "रामलाल शर्मा",
  "phone": "+91-9876543210",
  "role": "farmer | admin | agronomist",
  "village": "Dhampur",
  "district": "Bijnor",
  "state": "Uttar Pradesh",
  "aadhaarHash": "hashed_aadhaar",
  "createdAt": "ISODate",
  "preferredLanguage": "hi"
}
```

### Farmers Collection (Extended Profile)
```json
{
  "_id": "ObjectId",
  "userId": "ref: Users",
  "landArea": 2.5,
  "landUnit": "bigha | acre",
  "crops": ["wheat", "sugarcane"],
  "annualIncome": 120000,
  "bankAccount": "hashed_account",
  "category": "SC | ST | OBC | General",
  "isSmallFarmer": true,
  "documents": ["s3://gramsathi/docs/farmer_id.pdf"]
}
```

### Schemes Collection
```json
{
  "_id": "ObjectId",
  "name": "PM Kisan Samman Nidhi",
  "ministry": "Agriculture",
  "benefits": "₹6000/year",
  "eligibility": {
    "maxLandArea": 2.0,
    "maxIncome": 200000,
    "categories": ["SC", "ST", "OBC"]
  },
  "applicationUrl": "https://pmkisan.gov.in",
  "deadline": "ISODate",
  "isActive": true
}
```

---

## 🔐 Authentication

- **Registration**: Phone number + OTP verification
- **Login**: JWT access token (15 min) + refresh token (7 days)
- **Roles**: `farmer`, `admin`, `agronomist`, `service_provider`
- **RBAC**: Middleware checks role before serving protected routes

```javascript
// Middleware example
const requireRole = (role) => (req, res, next) => {
  if (req.user.role !== role) {
    return res.status(403).json({ message: 'Access denied' });
  }
  next();
};

router.get('/admin/users', authenticateJWT, requireRole('admin'), listUsers);
```

---

## 📡 API Design

| Method | Endpoint | Description | Auth |
|--------|---------|-------------|------|
| POST | `/api/auth/register` | Register new user | Public |
| POST | `/api/auth/login` | Login with phone + OTP | Public |
| GET | `/api/farmers/profile` | Get farmer profile | JWT |
| PUT | `/api/farmers/profile` | Update farmer details | JWT |
| GET | `/api/schemes/eligible` | Get eligible schemes | JWT |
| POST | `/api/schemes/apply` | Submit scheme application | JWT |
| GET | `/api/advisories` | Get farming advisories | JWT |
| POST | `/api/advisory/ask` | Ask an agronomist | JWT |

---

## ⚠️ Challenges & Solutions

| Challenge | Solution |
|-----------|---------|
| Low literacy in rural areas | Voice-based navigation + icons + regional language UI |
| Poor internet connectivity | Progressive Web App (PWA) with offline mode + sync |
| Scheme eligibility logic complexity | Rule engine with eligibility criteria in JSON schema |
| OTP verification in low-signal areas | Fallback to missed call OTP |
| Document digitization | Camera capture → Cloudinary → OCR via Tesseract |

---

## 🚀 Future Improvements

- [ ] AI-powered crop disease detection (image upload → disease diagnosis)
- [ ] Market price integration (mandi rates real-time)
- [ ] Peer-to-peer farmer community (WhatsApp-like messaging)
- [ ] Integration with Digilocker for official document access
- [ ] Voice assistant in Hindi for hands-free operation
- [ ] Analytics dashboard for district administrators

---

## ❓ Interview Questions & Answers

**Q: Why did you build GramSathi?**
> I noticed that despite government schemes worth ₹1000s of crores being available for farmers, most small farmers couldn't access them due to language barriers, lack of awareness, and complex application processes. GramSathi addresses all three issues in one platform.

**Q: How did you handle multiple languages?**
> I used Google Translate API for dynamic translation and stored all content in Hindi by default. For static UI strings, I implemented i18n with a language JSON config that supports Hindi, Bhojpuri, and Marathi.

**Q: How did you ensure data security for sensitive farmer information?**
> Aadhaar numbers and bank accounts are hashed before storage using bcrypt. All API communication uses HTTPS. JWTs are short-lived (15 min) with secure refresh token rotation.

**Q: What was the biggest technical challenge?**
> Building the offline-first PWA was challenging. I used Service Workers and IndexedDB to cache data locally and sync with the server when connectivity is restored. This required careful conflict resolution logic.

**Q: How does the scheme eligibility engine work?**
> Each scheme has eligibility criteria stored as a JSON rule set (land area, income, caste category, etc.). When a farmer requests eligible schemes, the backend runs the farmer's profile against each scheme's rules. It's a simple rule engine pattern.

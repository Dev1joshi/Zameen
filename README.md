# Zameen — Smart Property Intelligence Platform

> A full-stack real estate web application built for the Indian property market, featuring AI-powered price estimation, locality insights, investment scoring, and a complete buyer/seller workflow.

---

## What This Project Is About

Finding the right property in India is notoriously difficult — prices vary wildly across localities, there's almost no transparency in market trends, and buyers rarely have access to data-backed insights before making a decision.

**Zameen** addresses this by combining a curated property listings platform with machine learning-powered price estimation, area quality ratings, and investment scoring — all in one place. It's built to work across 10 major Indian cities and is designed with real-world scale in mind.

> Adapted and developed from a base project scaffold. All ML integration, feature enhancements, and architectural decisions were implemented as part of this build.

---

## Features

### For Buyers
- **Browse & Filter Listings** — Search properties by city, locality, BHK, price range, furnishing status, and property type
- **AI Price Estimator** — Enter property details and get a fair market price estimate powered by a trained Random Forest model, along with a confidence interval (P10–P90)
- **Price History Charts** — Visualize how prices have moved in any locality over time
- **Area Ratings** — Each locality is rated across 9 parameters including safety, schools, connectivity, and walkability
- **Locality Comparison** — Compare up to 3 localities side-by-side on pricing, ratings, and investment potential
- **Save Properties** — Bookmark listings to revisit later (requires login)

### For Sellers
- **Seller Dashboard** — Create, manage, activate, or deactivate your listings from a single view
- **Market Context** — See how your listing compares to similar properties in the area

### Intelligence Layer
- **Investment Scores** — Data-backed ROI scoring for localities based on price trends and demand signals
- **Top Investment Picks** — Curated recommendations for investors across all supported cities
- **Market Stats** — City-level statistics on average prices, price per sqft, and supply

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 18, React Router v6, Recharts, react-hot-toast, react-icons |
| **Backend** | Node.js, Express.js, Mongoose, JWT, bcryptjs, express-validator |
| **Database** | MongoDB (local or Atlas) |
| **ML Service** | Python, Flask, scikit-learn (Random Forest Regressor), pandas, numpy |
| **Styling** | Pure CSS with CSS custom properties — no UI library dependency |

---

## ML Model Details

The price estimator uses a **Random Forest Regressor** trained on 6,000 synthetic records modeled after real Indian property market patterns.

- **Input features**: City, locality, BHK count, carpet area, floor, property age, furnished status, facing direction, property type
- **Output**: Predicted price + confidence interval + top contributing factors
- **Fallback**: If the ML service is unavailable, the backend automatically switches to a rule-based heuristic so the estimator always returns a result

---

## Folder Structure

```
zameen-fullstack/
├── frontend/                  # React 18 client
│   └── src/
│       ├── pages/             # Route-level page components
│       ├── components/        # Shared/reusable UI components
│       ├── context/           # React context (auth state)
│       └── utils/             # API client, helper functions
│
├── backend/                   # Node.js + Express REST API
│   ├── models/                # Mongoose data models
│   ├── routes/                # Modular API route handlers
│   ├── middleware/            # JWT auth middleware
│   └── scripts/               # Database seed script
│
└── ml-api/                    # Python Flask ML microservice
    ├── app.py                 # Model serving + prediction endpoint
    └── zameen_ml_model/       # Serialized model files
```

---

## Getting Started

### Prerequisites

- Node.js v18+
- MongoDB (local instance or Atlas connection string)
- Python 3.9+ *(only needed if you want to run the ML service)*

---

### 1. Clone and Install Dependencies

```bash
# Install backend dependencies
cd backend
npm install
cp .env.example .env
# Open .env and fill in your values (see Environment Variables section)

# Install frontend dependencies
cd ../frontend
npm install
```

---

### 2. Seed the Database

This script generates 1,000+ realistic Indian property listings across 10 cities and populates your MongoDB instance.

```bash
cd backend
node scripts/seedFromCSV.js
```

Three demo accounts are created automatically:

| Role | Email | Password |
|---|---|---|
| Admin | admin@zameen.com | admin123 |
| Seller | seller@zameen.com | seller123 |
| Buyer | buyer@zameen.com | buyer123 |

---

### 3. Start the Backend

```bash
cd backend
npm run dev
# Runs on http://localhost:5000
```

---

### 4. Start the ML Service *(Optional but recommended)*

```bash
cd ml-api
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
python app.py
# Runs on http://localhost:8000
```

> If you skip this step, the backend's heuristic fallback will handle all price estimation requests automatically.

---

### 5. Start the Frontend

```bash
cd frontend
npm install
npm start
# Runs on http://localhost:3000
```

---

## Environment Variables

### `backend/.env`

```env
PORT=5000
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret_key
ML_API_URL=http://localhost:8000
NODE_ENV=development
```

### `frontend/.env` *(optional)*

```env
REACT_APP_API_URL=http://localhost:5000/api
```

> Never commit real credentials to version control. Use `.env.example` files as templates and keep `.env` in `.gitignore`.

---

## API Reference

### Authentication
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/auth/register` | Create a new user account |
| POST | `/api/auth/login` | Login and receive JWT |
| GET | `/api/auth/me` | Get authenticated user profile |
| POST | `/api/auth/save-property/:id` | Save or unsave a property |

### Properties
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/properties` | List properties with filters |
| GET | `/api/properties/:id` | Get single property details |
| POST | `/api/properties` | Create a new listing (seller) |
| PUT | `/api/properties/:id` | Update a listing |
| DELETE | `/api/properties/:id` | Remove a listing |
| GET | `/api/properties/cities` | Fetch all supported cities |
| GET | `/api/properties/localities/:city` | Localities for a given city |
| GET | `/api/properties/price-history/:city/:locality` | Price trend data |
| GET | `/api/properties/compare/localities` | Compare locality metrics |
| GET | `/api/properties/seller/my-listings` | Authenticated seller's listings |

### ML / Price Estimation
| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/ml/estimate` | Predict price for given property details |
| GET | `/api/ml/health` | Check ML service availability |

### Areas
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/areas` | All area ratings |
| GET | `/api/areas/:city/:locality` | Ratings for a specific locality |
| POST | `/api/areas/:city/:locality/rate` | Submit a community rating |

### Investment
| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/investment/insights` | Top localities ranked by investment score |
| GET | `/api/investment/stats` | City-level market statistics |

---

## Supported Cities

Bangalore · Mumbai · Gurgaon · Hyderabad · Pune · Delhi · Chennai · Kolkata · Ahmedabad · Jaipur

Each city includes 15 localities with realistic pricing benchmarks, area quality ratings, and investment scores based on observed Indian market patterns.

---

## Deployment

| Service | Platform |
|---|---|
| Frontend | Vercel or Netlify (`npm run build`) |
| Backend | Railway, Render, or Heroku |
| ML API | Railway or Render (`gunicorn app:app`) |
| Database | MongoDB Atlas (free tier works fine) |

---

## Future Improvements

These are features planned for future iterations:

- **Real listing data pipeline** — Integrate with public property data sources or allow CSV imports for real market data
- **Advanced ML features** — Incorporate neighbourhood-level demand signals and seasonal trends into the price model
- **Map view** — Plot listings on an interactive map with locality boundary overlays
- **Mortgage calculator** — Help buyers estimate EMI and affordability directly on the listing page
- **Notification system** — Alert saved buyers when a property's price drops or a new matching listing is added
- **Admin panel** — Full dashboard for listing moderation, user management, and platform analytics
- **Mobile responsive overhaul** — Dedicated mobile UX improvements for the property browsing and comparison flows

---

## A Note on This Project

This project was developed and extended from an open-source base. The work involved integrating the ML microservice with the Express backend, implementing the fallback pricing logic, building out the investment scoring module, designing the seller workflow, and structuring the project for deployment readiness. It is presented here as a developed full-stack application, not a project built entirely from scratch.

---

*Built with ❤️ for the Indian property market — pricing in ₹ Lakhs and Crores, cities from Mumbai to Jaipur.*
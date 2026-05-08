# Stock-Market-Prediction-Through-Sentimental-Analysis
This Project pulls live data from social media (Twitter/Reddit), financial news, and stock price APIs - runs sentiment NLP + ML prediction models - serves predictions to a real-time dashboard showing whether a stock is likely to go up or down.

-Collect data from Twitter, Reddit, Yahoo Finance, and news APIs
-Clean and store historical XAUUSD price data (March–November 2025)
-Run sentiment analysis on text using two NLP models (VADER for tweets, FinBERT for news)
-Engineer features -- RSI, MACD, Bollinger Bands combined with daily sentiment scores
-Train 4 ML models *[Linear Regression, Multivarient Regression, Random Forest, Extra Tree Regressor]*
-compare their prediction accuracy against real gold prices
-Serve predictions through a live dashboard with real-time buy/sell signal

# Tech Stack
*Java* - Spring Boot for data ingestion pipelines and REST/WebSocket backend API
*Python* - scikit-learn, FinBERT, VADER for ML and NLP
*FastAPI* - serves ML model predictions as a microservice
*React* - live interactive dashboard
*PostgreSQL* - stores price history
*Apache Kafka* - real-time data streaming between services
*Redis* - caches live predictions

# SMPTSA — Complete Project Summary

> **S**ocial **M**edia **P**owered **T**rading **S**entiment **A**nalyzer  
> A real-time gold (XAUUSD) price prediction system that fuses market data with NLP-driven social media sentiment analysis.

---

## 1. Project Overview & Use Case

SMPTSA is a **five-layer, full-stack financial prediction platform** that predicts the next-day closing price of **XAUUSD (Gold / US Dollar)** by combining:

1. **Historical OHLCV price data** from Yahoo Finance
2. **Social sentiment** scraped from Twitter/X, Reddit, and news outlets
3. **Dual NLP engines** (VADER + FinBERT) for sentiment scoring
4. **5 Machine Learning regression models** for price forecasting
5. **A premium React dashboard** for real-time visualization

### Target Users
- Financial analysts and traders monitoring gold markets
- Researchers studying the correlation between social sentiment and commodity prices
- Quantitative finance enthusiasts exploring ML-based prediction systems

### Core Problem Solved
Traditional technical analysis ignores public sentiment. SMPTSA bridges this gap by ingesting thousands of social media posts and news articles daily, scoring them with both rule-based (VADER) and transformer-based (FinBERT) NLP models, and feeding those sentiment signals directly into price prediction models — enabling users to see how market mood influences gold prices in real-time.

---

## 2. Architecture Overview

```
┌──────────────────────────────────────────────────────────────────┐
│                        FRONTEND (React + Vite)                   │
│   Dashboard │ Model Comparison │ Sentiment Timeline │ Auth       │
│                  WebSocket ← Live Predictions                    │
└──────────────────────────┬───────────────────────────────────────┘
                           │ REST + WS
┌──────────────────────────▼───────────────────────────────────────┐
│              BACKEND — API GATEWAY (Spring Boot 3.2)             │
│   AuthController │ PredictionController │ WebSocket Handler      │
│   JWT Auth │ SecurityConfig │ MlServiceClient (WebClient proxy)  │
└────────┬─────────────────┬───────────────────────────────────────┘
         │                 │ HTTP (WebClient)
         │    ┌────────────▼──────────────────────────────────────┐
         │    │         ML SERVICE (Python FastAPI)                │
         │    │  /predict │ /backtest │ /sentiment/history │ /health│
         │    │  5 trained models loaded at startup via joblib     │
         │    └───────────────────────────────────────────────────┘
         │
┌────────▼─────────────────────────────────────────────────────────┐
│           BACKEND — INGESTION SERVICE (Spring Boot 3.2)          │
│   MarketDataScheduler (5-min cron) │ SocialDataScheduler         │
│   KafkaProducerService → Topics: market-data, social-data        │
└──────────┬───────────┬───────────┬───────────────────────────────┘
           │           │           │
    ┌──────▼──┐  ┌─────▼────┐  ┌──▼──────────┐  ┌──────────────┐
    │PostgreSQL│  │  Kafka   │  │    Redis    │  │Elasticsearch │
    │TimescaleDB│ │  3.7     │  │  7-alpine   │  │    8.13      │
    └─────────┘  └──────────┘  └─────────────┘  └──────────────┘
```

The system follows a **strict microservice separation of concerns**:
- **API Gateway** — handles auth, proxies requests, pushes WebSocket updates
- **Ingestion Service** — scheduled data fetching, Kafka event publishing
- **ML Service** — NLP scoring, feature engineering, model training & serving

---

## 3. Complete Tech Stack

### 3.1 Frontend
| Technology | Version | Purpose |
|---|---|---|
| **React** | 18.3 | UI component library |
| **TypeScript** | ~6.0 | Type-safe development |
| **Vite** | 8.0 | Build tool & dev server |
| **Recharts** | 2.15 | Charts — ComposedChart, RadarChart, LineChart |
| **Vanilla CSS** | — | Glassmorphism design system with CSS variables |

### 3.2 Backend — API Gateway (Java)
| Technology | Version | Purpose |
|---|---|---|
| **Spring Boot** | 3.2.5 | Application framework |
| **Java** | 21 | Language runtime |
| **Spring Security** | — | JWT-based stateless authentication |
| **Spring WebSocket** | — | Real-time prediction push (every 30s) |
| **Spring WebFlux (WebClient)** | — | Non-blocking HTTP client to call FastAPI |
| **Spring Kafka** | — | Kafka consumer integration |
| **Spring Data JPA** | — | PostgreSQL ORM |
| **Spring Data Redis** | — | Live prediction caching |
| **JJWT** | 0.12.5 | JWT token generation & validation |
| **Lombok** | — | Boilerplate reduction |
| **Jackson** | — | JSON serialization |
| **PostgreSQL Driver** | — | Database connectivity |
| **Maven** | — | Build system |

### 3.3 Backend — Ingestion Service (Java)
| Technology | Version | Purpose |
|---|---|---|
| **Spring Boot** | 3.2.5 | Application framework |
| **Spring Kafka** | — | Event publishing to Kafka topics |
| **Spring WebFlux** | — | WebClient for Yahoo Finance API calls |
| **Spring Data JPA** | — | OHLCV & social data persistence |
| **PostgreSQL Driver** | — | Database connectivity |

### 3.4 ML Service (Python)
| Technology | Version | Purpose |
|---|---|---|
| **FastAPI** | ≥0.115 | REST API framework |
| **Uvicorn** | ≥0.34 | ASGI server |
| **Pydantic** | ≥2.10 | Request/response validation |
| **scikit-learn** | ≥1.6 | ML models (LR, Ridge, RF, ExtraTrees) |
| **pandas** | ≥2.3 | Data manipulation |
| **numpy** | ≥2.2 | Numerical computing |
| **pandas-ta** | ≥0.3.14 | Technical indicators (RSI, MACD, Bollinger) |
| **yfinance** | ≥0.2.50 | Yahoo Finance OHLCV data |
| **transformers (HuggingFace)** | ≥4.47 | FinBERT model loading |
| **PyTorch** | ≥2.5 | FinBERT inference backend |
| **vaderSentiment** | ≥3.3.2 | VADER sentiment scoring |
| **TfidfVectorizer (sklearn)** | — | Text feature extraction |
| **joblib** | ≥1.4 | Model serialization |
| **matplotlib / seaborn** | — | Evaluation plots |
| **SQLAlchemy** | ≥2.0 | Database ORM |
| **psycopg2-binary** | ≥2.9 | PostgreSQL driver |
| **Alembic** | ≥1.14 | Database migrations |
| **kafka-python-ng** | ≥2.2 | Kafka messaging |
| **PRAW** | ≥7.8 | Reddit API client |
| **newsapi-python** | ≥0.2.7 | NewsAPI client |
| **redis** | ≥5.2 | Prediction caching |
| **elasticsearch** | ≥8.17 | Full-text search |
| **python-dotenv** | ≥1.0 | Environment management |

### 3.5 Infrastructure (Docker Compose)
| Service | Image | Purpose |
|---|---|---|
| **PostgreSQL + TimescaleDB** | `timescale/timescaledb:latest-pg15` | Time-series DB with hypertables |
| **Redis** | `redis:7-alpine` | Live prediction cache |
| **Apache Kafka** | `bitnami/kafka:3.7` | Event streaming (KRaft mode, no Zookeeper) |
| **Elasticsearch** | `elasticsearch:8.13.0` | Tweet/news full-text search |

---

## 4. Database Management

### 4.1 Database Engine
**PostgreSQL 15** with **TimescaleDB extension** — purpose-built for time-series financial data. TimescaleDB converts the `ohlcv_data` table into a **hypertable** partitioned by time, enabling:
- Automatic time-based partitioning
- Optimized range queries on historical price data
- Native time-series aggregation functions

### 4.2 Connection Management
- **Python (ML Service):** SQLAlchemy engine with connection pooling (`pool_size=10`, `max_overflow=20`)
- **Java (API Gateway & Ingestion):** Spring Data JPA with HikariCP connection pool
- **Configuration:** Environment variables via `.env` file

### 4.3 Schema — 5 Tables

#### Table 1: `ohlcv_data` (TimescaleDB Hypertable)
Stores OHLCV (Open, High, Low, Close, Volume) candle data for XAUUSD.

| Column | Type | Description |
|---|---|---|
| `time` | TIMESTAMPTZ | Candle timestamp (partition key) |
| `ticker` | VARCHAR(20) | Default: 'XAUUSD' |
| `open` | DOUBLE PRECISION | Opening price |
| `high` | DOUBLE PRECISION | High price |
| `low` | DOUBLE PRECISION | Low price |
| `close` | DOUBLE PRECISION | Closing price |
| `volume` | DOUBLE PRECISION | Trading volume |
| `source` | VARCHAR(50) | Data source (yahoo_finance) |
| `created_at` | TIMESTAMPTZ | Record creation time |

- **Hypertable:** Partitioned by `time` via `create_hypertable()`
- **Index:** `idx_ohlcv_ticker_time` on `(ticker, time DESC)`

#### Table 2: `raw_social_data`
Raw text from Twitter, Reddit, and news articles.

| Column | Type | Description |
|---|---|---|
| `id` | BIGSERIAL PK | Auto-increment ID |
| `date` | TIMESTAMPTZ | Publication date |
| `source` | VARCHAR(50) | 'twitter', 'reddit', or 'news' |
| `content` | TEXT | Raw text content |
| `author` | VARCHAR(255) | Author/username |
| `url` | TEXT | Source URL |
| `metadata` | JSONB | Flexible extra fields (subreddit, score, etc.) |
| `created_at` | TIMESTAMPTZ | Record creation time |

- **Indexes:** `idx_social_date` (date DESC), `idx_social_source` (source)

#### Table 3: `sentiment_scores`
Aggregated daily sentiment scores per source.

| Column | Type | Description |
|---|---|---|
| `id` | BIGSERIAL PK | Auto-increment ID |
| `date` | DATE | Scoring date |
| `source` | VARCHAR(50) | 'twitter', 'reddit', 'news', or 'all' |
| `vader_score` | DOUBLE PRECISION | VADER compound score [-1, +1] |
| `finbert_score` | DOUBLE PRECISION | FinBERT score [-1, +1] |
| `doc_count` | INTEGER | Number of documents scored |
| **UNIQUE** | | `(date, source)` — enables upsert |

#### Table 4: `predictions`
Model prediction history for backtesting.

| Column | Type | Description |
|---|---|---|
| `id` | BIGSERIAL PK | Auto-increment ID |
| `date` | DATE | Prediction date |
| `ticker` | VARCHAR(20) | Default: 'XAUUSD' |
| `model_name` | VARCHAR(100) | Model identifier |
| `predicted_price` | DOUBLE PRECISION | Predicted closing price |
| `actual_price` | DOUBLE PRECISION | Actual closing price |
| `direction` | VARCHAR(10) | 'UP', 'DOWN', 'NEUTRAL' |
| `confidence` | DOUBLE PRECISION | Confidence percentage |

#### Table 5: `users`
JWT authentication user accounts.

| Column | Type | Description |
|---|---|---|
| `id` | BIGSERIAL PK | Auto-increment ID |
| `username` | VARCHAR(100) UNIQUE | Login username |
| `password_hash` | VARCHAR(255) | BCrypt hashed password |
| `role` | VARCHAR(50) | Default: 'USER' |
| `created_at` | TIMESTAMPTZ | Registration time |

---

## 5. Features — Detailed Breakdown

### 5.1 Data Ingestion Pipeline
- **Market Data:** Scheduled every 5 minutes via `MarketDataScheduler` — fetches XAUUSD from Yahoo Finance v8 chart API, persists to PostgreSQL, and publishes to Kafka `market-data` topic
- **Social Data:** Fetches from Reddit (r/Gold, r/investing, r/economics) via PRAW and news articles via NewsAPI
- **Deduplication:** Both OHLCV and social data use dedup strategies (ON CONFLICT DO NOTHING, content uniqueness checks)
- **Kafka Events:** `MarketDataEvent` and `SocialDataEvent` DTOs published to separate topics

### 5.2 NLP Sentiment Engine (Dual-Model)
- **VADER:** Rule-based lexicon analyzer optimized for short social media text (tweets, Reddit posts). Returns compound score [-1, +1]
- **FinBERT:** Transformer-based model (`ProsusAI/finbert` from HuggingFace) fine-tuned for financial text. Lazy-loaded to save memory. Maps softmax probabilities to [-1, +1] (positive − negative)
- **Scoring Strategy:** VADER is primary for social posts; FinBERT is primary for news. Both are run on all documents when possible
- **Aggregation:** Daily mean scores computed per source + an "all" aggregate. Stored via upsert to `sentiment_scores`

### 5.3 Feature Engineering
- **Technical Indicators:** RSI-14, MACD (12,26,9), Bollinger Bands (20,2) computed via `pandas_ta`
- **Sentiment Features:** `vader_score`, `finbert_score`, `sentiment_combined` (average of both)
- **TF-IDF:** Optional 50-feature TF-IDF vectorization on daily concatenated social text
- **Target Variable:** `next_day_close` = Close shifted by -1 (next-day prediction)
- **16 total price-model features:** open, high, low, close, volume, rsi, macd, macd_signal, macd_hist, bb_lower, bb_mid, bb_upper, bb_bandwidth, vader_score, finbert_score, sentiment_combined

### 5.4 Machine Learning Models (5 Models)

| # | Model | Type | Description |
|---|---|---|---|
| 1 | **Linear Regression** | Baseline | Standard OLS on all 16 features |
| 2 | **Multivariate Ridge Regression** | Mid | Ridge (α=1.0) regularized regression |
| 3 | **Random Forest Regressor** | Mid | 150 trees, max_depth=15 |
| 4 | **Extra Trees Regressor** | Advanced | 150 trees, max_depth=15 |
| 5 | **Sentiment-Only Linear Reg** | Research | Uses only 3 sentiment features — experimental |

**Training Pipeline:**
- **Chronological split** (80/20) — never shuffles time-series data
- **StandardScaler** normalization per model
- **Evaluation metrics:** MAE, RMSE, R², Directional Accuracy
- **Persistence:** Models + scalers saved via `joblib`; evaluation results saved as JSON
- **Visualization:** Actual vs. predicted plots + feature importance charts for tree models

### 5.5 FastAPI Prediction Service
**Endpoints:**
| Method | Path | Description |
|---|---|---|
| `POST` | `/predict` | Predict next-day price from OHLCV + sentiment input |
| `GET` | `/backtest` | Evaluation metrics for all models |
| `GET` | `/models` | List available loaded models |
| `GET` | `/sentiment/history` | Daily sentiment scores over last N days |
| `GET` | `/health` | Service health check |

- Models loaded at startup from joblib files
- Each prediction returns: predicted price, direction (UP/DOWN/NEUTRAL), confidence %
- CORS enabled for all origins

### 5.6 API Gateway (Spring Boot)
- **REST Proxy:** Routes `/api/predict`, `/api/backtest`, `/api/sentiment/history` to FastAPI via WebClient
- **WebSocket:** `LivePredictionHandler` pushes prediction updates to all connected clients every 30 seconds
- **Health Check:** Combined gateway + ML service health status

### 5.7 Authentication System
- **Registration:** `POST /api/auth/register` — validates username (3-100 chars), password (min 6 chars), checks uniqueness, BCrypt hashes password, returns JWT
- **Login:** `POST /api/auth/login` — validates credentials against DB, returns JWT (24h expiry)
- **JWT Filter:** `JwtAuthFilter` intercepts all `/api/**` requests (except auth & WS), validates Bearer token
- **Security Config:** Stateless sessions, CORS for localhost:5173/3000, public endpoints for auth/ws/health

### 5.8 React Dashboard
**Pages:**
1. **Dashboard** — Price summary bar with consensus signal (UP/DOWN/NEUTRAL), prediction cards per model, interactive price+sentiment chart (Recharts ComposedChart with dual Y-axis), model performance teaser
2. **Models** — Metrics comparison table (MAE, RMSE, R², Dir. Accuracy, Features) with best-model badge, radar chart visualization
3. **Sentiment** — Sentiment timeline with VADER/FinBERT toggle, source filters (Twitter/Reddit/News/All), per-source stat cards

**Components:**
| Component | Description |
|---|---|
| `App.tsx` | Main shell — sidebar + header + content routing |
| `Sidebar.tsx` | Navigation: Dashboard, Models, Sentiment |
| `Header.tsx` | Connection status indicator, login/logout buttons |
| `PredictionCards.tsx` | Cards per model showing predicted price, direction arrow, confidence |
| `LiveChart.tsx` | ComposedChart — price area, volume bars, sentiment line, model prediction lines. Time range selector (1W/1M/3M/6M/All) |
| `ModelComparisonPanel.tsx` | Metrics table + RadarChart comparing all models |
| `SentimentTimeline.tsx` | ComposedChart with source toggles and VADER/FinBERT toggle |
| `LoginModal.tsx` | Glassmorphism modal with Sign In / Sign Up tabs |
| `SignalBadge.tsx` | Visual UP/DOWN/NEUTRAL indicator |

**Hooks:**
| Hook | Description |
|---|---|
| `useApi.ts` | REST client with JWT Bearer token injection, login/register/fetch functions |
| `useWebSocket.ts` | Auto-reconnecting WebSocket with exponential backoff (max 10 retries) |

### 5.9 UI/UX Design
- **Glassmorphism design system** with `.glass-card` class
- **Premium frosted-glass aesthetic** with multi-layered shadows
- **Dark theme** with carefully curated color palette
- **Custom typography:** Poppins Bold (headings) + DM Sans (body)
- **Responsive layout** with sidebar navigation
- **Micro-animations:** Fade-in transitions, hover effects
- **Custom favicon and branding**

---

## 6. Infrastructure & DevOps

### Docker Compose Stack
All infrastructure services are containerized with health checks and persistent volumes:

| Service | Container Name | Ports | Volume |
|---|---|---|---|
| PostgreSQL + TimescaleDB | `smptsa-postgres` | 5432 | `pgdata` |
| Redis | `smptsa-redis` | 6379 | `redisdata` |
| Kafka (KRaft, no Zookeeper) | `smptsa-kafka` | 9092, 9094 | `kafkadata` |
| Elasticsearch | `smptsa-elasticsearch` | 9200 | `esdata` |

### Environment Configuration
Managed via `.env` file with variables for:
- Database credentials (DB_HOST, DB_PORT, DB_NAME, DB_USER, DB_PASSWORD)
- Redis connection
- Kafka bootstrap servers
- Elasticsearch host
- Reddit API credentials (PRAW)
- NewsAPI key
- Twitter/Apify API token
- FastAPI host/port
- JWT secret key

---

## 7. Data Flow Summary

```
Yahoo Finance ──→ Ingestion Service ──→ PostgreSQL (ohlcv_data)
                                    └──→ Kafka (market-data topic)

Reddit (PRAW) ──→ ML Service Scripts ──→ PostgreSQL (raw_social_data)
NewsAPI       ──→                    └──→ Kafka (social-data topic)

raw_social_data ──→ VADER + FinBERT ──→ PostgreSQL (sentiment_scores)

ohlcv_data + sentiment_scores ──→ Feature Engineering (RSI, MACD, BB, TF-IDF)
                              ──→ Model Training (5 models)
                              ──→ joblib serialized models

React Dashboard ──→ API Gateway ──→ FastAPI ──→ Loaded Models ──→ Predictions
                ←── WebSocket (30s push) ←─────────────────────────┘
```

---

## 8. Key Design Decisions

| Decision | Rationale |
|---|---|
| **Chronological train/test split** | Never shuffle time-series — prevents data leakage |
| **Dual NLP engines** | VADER excels at social media slang; FinBERT excels at financial jargon |
| **Sentiment-only model** | Research experiment to isolate sentiment's predictive power vs. price+sentiment |
| **TimescaleDB over vanilla PostgreSQL** | Purpose-built for time-series with automatic partitioning |
| **Kafka for event streaming** | Decouples ingestion from processing; enables replay |
| **Redis for caching** | Sub-millisecond prediction lookups for WebSocket push |
| **WebSocket over polling** | Real-time prediction delivery without client-side overhead |
| **Spring Boot as API Gateway** | Enterprise-grade security (Spring Security + JWT) |
| **FastAPI for ML serving** | Python ecosystem for ML; async capable; auto-docs |
| **Stateless JWT auth** | Scalable — no server-side sessions |

---

## 9. External API Integrations

| API | Library | Purpose |
|---|---|---|
| **Yahoo Finance** | `yfinance` (Python) + v8 REST (Java) | XAUUSD OHLCV candle data |
| **Reddit** | PRAW | Posts from r/Gold, r/investing, r/economics |
| **NewsAPI** | newsapi-python | Gold-related news headlines and articles |
| **HuggingFace** | transformers | ProsusAI/finbert model download |
| **Twitter/X** | Apify (planned) | Tweet scraping via API token |

---

## 10. File Structure Summary

```
SMPTSA_AG/
├── docker-compose.yml          # Infrastructure stack
├── .env.example                # Environment template
│
├── backend/
│   ├── api-gateway/            # Spring Boot 3.2 — REST + WS + Auth
│   │   └── src/.../gateway/
│   │       ├── controller/     # AuthController, PredictionController
│   │       ├── model/          # User entity
│   │       ├── repository/     # UserRepository (JPA)
│   │       ├── security/       # JwtUtil, JwtAuthFilter, SecurityConfig
│   │       ├── service/        # MlServiceClient (WebClient proxy)
│   │       └── websocket/      # LivePredictionHandler, WebSocketConfig
│   │
│   └── ingestion-service/      # Spring Boot 3.2 — Data ingestion
│       └── src/.../ingestion/
│           ├── dto/            # MarketDataEvent, SocialDataEvent
│           ├── model/          # OhlcvData, RawSocialData entities
│           ├── repository/     # OhlcvRepository, SocialDataRepository
│           ├── scheduler/      # MarketDataScheduler, SocialDataScheduler
│           └── service/        # KafkaProducerService
│
├── ml_service/                 # Python FastAPI — ML + NLP
│   ├── app/main.py             # FastAPI endpoints (/predict, /backtest, etc.)
│   ├── db/
│   │   ├── database.py         # SQLAlchemy engine + session factory
│   │   └── schema.sql          # Full DB schema (5 tables)
│   ├── ml/
│   │   ├── feature_engineering.py  # OHLCV + technicals + sentiment merge
│   │   └── train_models.py     # 5-model training pipeline
│   ├── nlp/
│   │   └── sentiment_analyzer.py   # VADER + FinBERT scoring engine
│   ├── scripts/
│   │   ├── fetch_ohlcv.py      # Yahoo Finance data fetcher
│   │   └── fetch_social.py     # Reddit + NewsAPI fetcher
│   └── requirements.txt        # 30+ Python dependencies
│
└── frontend/                   # React + TypeScript + Vite
    ├── package.json            # React 18, Recharts, Vite 8
    └── src/
        ├── App.tsx             # Main shell with page routing
        ├── style.css           # Glassmorphism design system
        ├── components/         # 8 React components
        ├── hooks/              # useApi (REST+JWT), useWebSocket
        ├── types/index.ts      # TypeScript interfaces
        └── data/mockData.ts    # Development mock data
```

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

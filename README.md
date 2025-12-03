# Mag7 Market & Sentiment Intelligence Platform
A streamlined data platform that automatically extracts MAG7 stock prices, market sentiment, and S&P 500 benchmarks, loads the data into BigQuery, validates quality using Great Expectations, orchestrates workflows with Dagster, and visualizes insights through a Streamlit dashboard.
The pipeline is fully automated and runs daily at 00:00 SGT.

## Key Features

### 1. Daily MAG7 Stock Extraction
1. Daily MAG7 Stock Extraction
Primary stock source: yfinance
Automatically pulls OHLCV and technical indicators for:
AAPL, AMZN, GOOG, META, MSFT, NVDA, TSLA


### 2. Sentiment Data Ingestion
Includes:
Google News entity sentiment
CNN Fear & Greed Index (scraped)
Yahoo Finance, MarketWatch, CNBC, Reuters headlines (scraped)
Optional NLP sentiment scoring (VADER/TextBlob)

### 3. Data Quality Validation (Great Expectations)
- Ensures data reliability using validations for:
- Schema structure
- Null values
- Numeric ranges
- Sentiment score limits
- Daily freshness checks

### 4. MAG7 vs S&P500 Trends
-Daily computed analytics:
-Rolling averages (20D, 50D, 200D)
-MAG7 vs SP500 ratio
-Divergence signals
-Price–sentiment correlation
-Macro-news sentiment alignment

### 5. Streamlit Dashboard
Interactive UI for:
Multi-ticker stock charts
MAG7 vs SP500 comparison
Sentiment overlays
Divergence detection
Market signals summary

## Data Source
The platform consolidates data from multiple sources:
1. yfinance (Primary Live Stock Source)
MAG7 live OHLCV
Volume
Indicators
2. CSV Files (Optional Historical Backfill)
Custom datasets
Prior years’ OHLCV
Manually curated inputs
3. Web Scraping Sources
Used to capture live sentiment & macro context:
CNN Fear & Greed Index
Yahoo Finance Headlines
MarketWatch Latest News
Reuters World News
CNBC World Markets
Headlines are later transformed into sentiment scores.

## Architecture

              ┌──────────────────────────────────────────┐
              │                 Data Sources             │
              │──────────────────────────────────────────│
              │ • yfinance (MAG7 Live Prices)            │
              │ • CSV Files (Historical Data)            │
              │ • Web Scraping (CNN, Yahoo, MW, CNBC)    │
              └──────────────────────────┬───────────────┘
                                         │
                                         ▼
                          ┌─────────────────────────┐
                          │         Meltano         │
                          │  Extract → Load to BQ   │
                          └──────────────┬──────────┘
                                         │
                                         ▼
                          ┌─────────────────────────┐
                          │        BigQuery         │
                          │   Raw + Model Tables    │
                          └──────────────┬──────────┘
                                         │
                                         ▼
               ┌──────────────────────────────────────────────┐
               │             Great Expectations               │
               │  Data Quality (schema, ranges, nulls, fresh) │
               └──────────────────────────┬───────────────────┘
                                          │
                                          ▼
                          ┌─────────────────────────┐
                          │          Dagster        │
                          │  Daily Run @ 00:00 SGT  │
                          │  Sensors & Orchestration│
                          └──────────────┬──────────┘
                                         │
                                         ▼
                          ┌─────────────────────────┐
                          │        Streamlit        │
                          │     Dashboard UI        │
                          └─────────────────────────┘

## BIGQUERY TABLE STRUCTURE

| Table                       | Description                            |
| --------------------------- | -------------------------------------- |
| **`raw.stock_prices`**      | MAG7 OHLCV (yfinance)                  |
| **`raw.sentiment_news`**    | Scraped news headlines + NLP sentiment |
| **`raw.cnn_index`**         | CNN Fear & Greed Index                 |
| **`raw.csv_imports`**       | Historical CSV backfill                |
| **`model.mag7_vs_sp500`**   | Performance comparison                 |
| **`model.sentiment_trend`** | Daily sentiment movement               |
| **`model.market_signals`**  | Combined market intelligence           |



## Created By

**Created by Eunice for self-exploration and learning.**


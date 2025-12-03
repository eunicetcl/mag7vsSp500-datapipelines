# Mag7 Market & Sentiment Intelligence Platform

A streamlined data platform that automatically extracts MAG7 stock prices, market sentiment, and S&P 500 benchmarks, processes them in BigQuery, validates quality using Great Expectations, orchestrates with Dagster, and visualizes results through a Streamlit dashboard.  
The pipeline runs daily at **00:00 SGT**.

## Key Features

### 1. Daily MAG7 Stock Extraction
Automatically pulls OHLCV and indicators for:  
`AAPL, AMZN, GOOG, META, MSFT, NVDA, TSLA`

### 2. Sentiment Data Ingestion
- Google News entity sentiment  
- CNN Fear & Greed Index  
- Financial headline sentiment  

### 3. Data Quality Validation (Great Expectations)
Checks include:
- Schema validation  
- Null checks  
- Range checks  
- Data freshness  

### 4. MAG7 vs S&P500 Trends
- Rolling averages  
- Divergence signals  
- Price–sentiment correlation  

### 5. Streamlit Dashboard
Interactive views for:
- Price charts  
- Sentiment overlays  
- Benchmark comparison  

## Architecture


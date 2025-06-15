# CryptoPredictor
$120/monthly, Contact `dev.ro0ti` on Discord. Yes a super advanced tool designed to make passive income.

## Table of Contents
- [Data Collection](#data-collection)
- [Feature Engineering](#feature-engineering)
- [Model Training](#model-training)
- [Model Management](#model-management)
- [Risk Management](#risk-management)
- [Retraining System](#retraining-system)
- [Monitoring & Evaluation](#monitoring--evaluation)
- [Technical Implementation](#technical-implementation)
- [Operational Features](#operational-features)

# Data Collection
## Multi-Source Data Integration
- **Yahoo Finance API** - Historical OHLCV data
- **CryptoPanic API** - Cryptocurrency-specific news sentiment
- **Glassnode API** - On-chain metrics (market/price_usd_close)
- **NewsAPI** - General financial news sentiment (optional)

## Data Quality Features
- Automatic handling of missing data
- Duplicate removal during merges
- Type conversion for datetime fields

## Error Handling
- Graceful degradation for API failures
- Empty DataFrame returns for failed requests
- Comprehensive error logging with timestamps

## Feature Engineering

## Technical Indicators
| Category          | Indicators                                  |
|-------------------|--------------------------------------------|
| Trend             | EMA_10, EMA_30, ADX                        |
| Momentum          | RSI, MACD, MACD_Signal, STOCH_K, STOCH_D  |
| Volatility        | ATR, BB_Upper, BB_Lower                    |
| Volume            | OBV, AD                                    |
| Cycle             | HT_DCPERIOD                                |
| Price Transforms  | WCLPRICE                                   |

## Temporal Features
- Day of week (0-6)
- Day of month (1-31)
- Week of year (1-52)
- Month (1-12)

## Lag Features
- 7-day lagged close prices (lag_1 through lag_7)

## Feature Selection
- SelectKBest with f_regression scoring
- Top 15 features automatically selected
- Persistent feature selector for consistent transforms

# Model Training

## XGBoost Implementation
- Randomized hyperparameter search with:
  ```python
  param_grid = {
      'n_estimators': [100, 200, 300],
      'max_depth': [3, 5, 7],
      'learning_rate': [0.01, 0.1, 0.2],
      'subsample': [0.8, 0.9, 1.0]
  }
  ```
- TimeSeriesSplit validation (5 splits)
- Objective: reg:squarederror
- LSTM Implementation
    ```python
    Sequential([
        LSTM(128, return_sequences=True),
        Dropout(0.3),
        LSTM(64),
        Dropout(0.2),
        Dense(32, activation='relu'),
        Dense(1)
    ])
    ```
 - Early stopping (patience=10)
 - Model checkpointing
 - 100 epoch training with 32 batch size

# Model Management
## Persistence
 - Automatic directory creation (models/)
 - XGBoost: Pickle serialization
 - LSTM: HDF5 format
 - Versioned model storage

## MLflow Integration
 - Parameter logging
 - Metric tracking (MAE, loss)
 - Experiment comparison
 - Run lifecycle management

# Risk Management
## Key Metrics
 - **Value at Risk (VaR):** 95th percentile of returns distribution
 - **Conditional VaR:** Mean of returns beyond VaR threshold
 - **Max Drawdown:** Peak-to-trough decline percentage

# Retraining System
## Scheduled Retraining
 - Weekly cadence (configurable)
 - Runs at 02:00 system time
 - Background scheduler with 1s sleep interval

## Incremental Data Handling
 - Loads last 30 days of new data
 - Merges with existing dataset
 - Automatic duplicate removal

## Full Pipeline
 - Data refresh
 - Feature re-engineering
 - Model retraining
 - Performance evaluation
 - Risk metric recalculation

# Monitoring & Evaluation
## Performance Metrics
 - Mean Absolute Error (MAE)
 - Training/validation loss (LSTM)
 - Feature importance scores

## Alerting
 - Error logging with stack traces
 - Training completion notifications
 - Significant metric change detection

# Technical Implementation
## Key Components
 - **CryptoDataFetcher:** Handles all API interactions
 - **FeatureEngineer:** Creates 50+ derived features
 - **ModelTrainer:** Manages training lifecycle
 - **RiskManager:** Calculates portfolio metrics
 - **Retrainer:** Automated model refresh system

# Operational Features
## Logging
 - **INFO:** level for operational messages
 - **ERROR:** level with tracebacks
 - **Progress:** tracking through stages

# Configuration
```python
# Constants
CRYPTO_SYMBOL = "BTC-USD"
START_DATE = "2020-01-01" 
MODEL_SAVE_PATH = "models/"
DATA_SAVE_PATH = "data/"
```

## Error Resilience
 - Checkpointing of processed data
 - Model version fallback
 - Graceful API failure handling

# Extensibility Points
## New Data Sources
 - Implement additional fetcher methods
 - Add to merge logic

## Additional Models
 - Inherit from base trainer class
 - Implement training protocol

## Custom Features
 - Add to FeatureEngineer methods
 - Modify selection criteria

## Alerting
 - Integrate Slack/email notifications
 - Add threshold-based triggers
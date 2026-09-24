[README.md](https://github.com/user-attachments/files/32609598/README.md)
# US Tech Market Monitor

A transparent, rules-based daily market monitor for U.S. technology and semiconductor stocks.

The project is designed to answer a specific question:

> **Is a market move supported by price, volume, trend, and breakout behavior?**

It is a **monitoring and research tool**, not a trading system and not proof of institutional buying or selling.

## Features

- Daily price change
- 20-day relative volume (RVOL20)
- EMA20 / EMA50 / EMA200 trend structure
- RSI14 momentum
- Prior 20-session high and breakout distance
- ATR14 volatility
- Transparent 0–100 price/volume alignment score
- Human-readable daily report
- CSV output for further analysis
- Optional GitHub Actions automation after the U.S. regular session

## Default universe

`GLW`, `KLAC`, `SPCX`, `VRT`

The monitor can easily be customized by editing `TICKERS` in `market_monitor.py`.

## Scoring model

The score is deliberately transparent:

| Component | Weight | What it measures |
|---|---:|---|
| Trend | 25 | Close vs EMA20/200 and EMA20 vs EMA50 |
| Volume | 25 | RVOL20 |
| Breakout | 20 | Distance above/below prior 20-day high |
| Momentum | 15 | RSI14 |
| Relative strength proxy | 15 | Positive 5-day and 20-day returns |
| **Total** | **100** | |

### Signal labels

- **STRONG PRICE/VOLUME**: 75–100
- **POSITIVE**: 60–74.9
- **WATCH**: 45–59.9
- **WEAK**: below 45

These labels describe the model's inputs. They are **not buy/sell recommendations**.

## Example interpretation

A stock that rises strongly while:

- RVOL20 is above 1.5,
- price is above EMA20 and EMA50,
- and price breaks the prior 20-day high

has stronger **price/volume confirmation** than a stock that rises on below-average volume.

The model does **not** infer that a particular institution is buying. Public OHLCV data cannot establish that conclusion by itself.

## Run locally

```bash
git clone https://github.com/YOUR_USERNAME/us-tech-market-monitor.git
cd us-tech-market-monitor

python -m venv .venv
source .venv/bin/activate       # macOS/Linux
# .venv\Scripts\activate        # Windows

pip install -r requirements.txt
python market_monitor.py
```

The script writes:

- `market_report_YYYYMMDD.txt`
- `market_report_YYYYMMDD.csv`

## GitHub Actions

The included workflow runs on weekdays after the U.S. regular session.

GitHub Actions uses UTC. The schedule is intentionally set late enough to be after the 16:00 ET close during both daylight-saving and standard-time periods.

Reports are uploaded as workflow artifacts, so the repository does not need to commit generated files on every run.

You can also run it manually from:

**GitHub → Actions → Daily Market Monitor → Run workflow**

## Data source

The project uses [`yfinance`](https://github.com/ranaroussi/yfinance) for market data.

Yahoo Finance data availability, adjustments, rate limits, and historical-data behavior can change. For production or trading use, consider replacing the data layer with a licensed market-data provider.

## Limitations

- This is a daily end-of-session screen, not a real-time trading engine.
- RVOL is calculated against the previous 20 sessions.
- Technical indicators are descriptive, not predictive guarantees.
- News and fundamental catalysts are not automatically verified by this script.
- Data-provider outages or changes can cause missing symbols or incomplete reports.
- The model intentionally avoids claiming that volume alone identifies institutional activity.

## License

MIT

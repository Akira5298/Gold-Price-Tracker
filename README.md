# Automated Gold Price Tracker

A Unix-based data management project that collects gold price information, stores historical records in **MySQL**, and generates analytical visualizations with **Gnuplot**.

Built for **COMP1314 — Data Management** coursework, the project combines Bash scripting, web data extraction, SQL, basic statistical analysis, and automated reporting.

## Features

- Collects gold price data from a public website using `curl` and multiple text-parsing patterns.
- Validates extracted prices before inserting or updating daily records in MySQL.
- Stores historical prices, price statistics, and collection-log information in a structured database.
- Uses SQL views to summarize prices and calculate day-to-day changes.
- Generates **12 charts** covering trends, moving averages, price changes, statistical bands, and volatility.
- Records collection and plotting activity in timestamped log files.
- Supports scheduling data collection through Unix `cron` (additional non-interactive authentication setup is needed).

## Visualizations

Selected examples from the `plots/` folder:

| Statistical analysis | Daily price changes |
|---|---|
| ![Gold price statistics](plots/goldprice_statistics.png) | ![Daily gold price changes](plots/goldprice_changes.png) |
| Average and standard-deviation reference lines | Positive and negative daily movements |

| Price distribution | Daily volatility |
|---|---|
| ![Gold price histogram](plots/goldprice_histogram.png) | ![Gold price volatility](plots/goldprice_volatility.png) |
| Frequency distribution across price ranges | Absolute day-to-day price changes |

<details>
<summary><strong>View the other 8 generated charts</strong></summary>

| Cumulative gain/loss | Seven-day smoothed trend |
|---|---|
| ![Cumulative gain and loss](plots/goldprice_gainloss.png) | ![Smoothed gold price trend](plots/goldprice_smoothed.png) |

| Weekly price range | Gold price data points |
|---|---|
| ![Weekly high and low](plots/goldprice_weeklyrange.png) | ![Gold prices with markers](plots/goldprice_points.png) |

| Filled price chart | Three-day moving average |
|---|---|
| ![Filled gold price chart](plots/goldprice_filled.png) | ![Gold price moving average](plots/goldprice_movingavg.png) |

| Recent price trend | Weekly average |
|---|---|
| ![Recent gold prices](plots/goldprice_recent.png) | ![Weekly average gold prices](plots/goldprice_weekly.png) |

</details>

The plotting script also creates `plots/summary.txt`, containing the observation period and descriptive price statistics. Charts reflect the database contents at the time they were generated; the repository includes seeded example records for demonstration.

## Database Design

The `goldtracker` MySQL database defines three main tables:

| Table | Purpose |
|---|---|
| `goldprice` | Stores collected prices and collection dates; one record per date |
| `pricestatistics` | Stores daily, weekly, and monthly summary fields |
| `collectionlogs` | Defines fields for collection status, timestamps, and errors |

Two SQL views provide convenient analysis: `recentprices` computes day-to-day price and percentage changes using `LAG()`, while `pricesummary` returns record counts and minimum, maximum, average, and standard-deviation statistics.

<p align="center">
  <img src="erd_diagram.png" alt="Entity relationship diagram of the goldtracker database" width="650">
</p>

*Entity relationship diagram of the database schema.*

## Getting Started

### Requirements

- macOS or another Unix-like environment with Bash
- MySQL with a local user permitted to create databases and write records
- `curl`, `grep`, `sed`, `awk`, `bc`, and `gnuplot`
- A working network connection for live data collection

**Configuration:** Both shell scripts currently reference MySQL at `/opt/homebrew/opt/mysql@5.7/bin/mysql`. Change the `mysqlcmd` variable near the top of `data_collection.sh` and `plot_data.sh` to the location of your MySQL client (find it with `which mysql`). The scripts also prompt for the MySQL root password.

### 1. Clone the repository

```bash
git clone https://github.com/Akira5298/gold-price-tracker.git
cd gold-price-tracker
```

### 2. Initialize the database

```bash
mysql -u root -p < db_setup.sql
```

**Warning:** `db_setup.sql` begins with `DROP DATABASE IF EXISTS goldtracker;`. Running it **deletes any existing `goldtracker` database and its data** before recreating the schema and inserting sample prices. Use it only when you want a fresh setup.

### 3. Collect a gold price

```bash
bash data_collection.sh
```

The script downloads and validates a candidate price, then inserts or updates that day's record. Review `logs/collection.log` and `logs/error.log` for collection activity and errors. Because the source page can change, the scraping patterns and hard-coded acceptable price range may require maintenance.

### 4. Generate plots and a summary

```bash
bash plot_data.sh
```

Generated PNGs and `summary.txt` are written to `plots/`; plotting activity is written to `logs/plot.log`.

> The scripts currently use interactive MySQL password prompts. To run them unattended with `cron`, first configure secure non-interactive MySQL authentication rather than storing passwords directly in scripts.

## Project Structure

```text
gold-price-tracker/
├── data_collection.sh      # Download, parse, validate, and store prices
├── db_setup.sql            # Schema, analytical views, and sample data
├── plot_data.sh            # Generate charts and a statistical summary
├── erd_diagram.png         # Database relationship diagram
├── plots/                  # Exported PNG charts and summary.txt
├── logs/                   # Collection and plotting logs
├── Screenshots/            # Additional coursework screenshots
├── .gitignore
└── README.md
```

## Technologies

**Bash · MySQL · SQL · Gnuplot · curl · awk · sed · grep · bc · Git**

---

*Coursework demonstration for data collection, database management, and historical price visualization. The bundled sample data and charts should not be treated as a live market feed or investment advice.*

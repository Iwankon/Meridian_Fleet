# Meridian Fleet Services — Dashboard Specification

## Core question
Are individual vehicles earning their keep — especially the newer cars?
This is the client's central worry; vehicle economics is the spine of the dashboard.

## Questions and metrics

### 1. Which vehicles are profitable once running costs are allocated?
- Contribution per vehicle = revenue − fuel − maintenance − commission

### 2. What does each platform really cost? (second priority)
- Platform contribution = total platform revenue − total platform fees
- Effective commission rate = (commission + fixed fees) ÷ gross fares
  (the two platforms have different fee structures — % alone is misleading)

### 3. Additional metrics
- Contribution per driver = driver revenue − commission
  (maintenance only allocable per-driver IF drivers map to specific vehicles — they don't here, so exclude it)
- Contribution per day
- Cumulative revenue by driver / platform / vehicle / day
- Cost per km (fuel + maintenance) per vehicle — the metric that reveals the fuel-type pattern

## Data limitations
- No platform comparison for H1 (that export has no platform column)
- No surge multiplier before 2026
- No revenue-per-hour per driver (hours not recorded, only trip timestamps)
- Driver-level totals carry a known gap (~240 trips have orphan driver IDs)
- H3 timestamps carry a +03:00 offset — must be handled or late trips shift date

## Report structure
- Page 1: Fleet overview
- Page 2: Vehicle economics
- Page 3: Platform comparison

## Nice-to-have (named, deferred)
- Revenue by payment method, revenue by zone, total distance covered
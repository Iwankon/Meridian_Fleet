# Meridian Fleet — Data Quality Log

*Two categories: [DEFECT] = needs cleaning · [MODEL] = business rule to handle in modelling, not a data error.*

## Trip files
- [DEFECT] trips_2026_H1: semicolon-delimited, not comma. Fixed with sep=";".
- [MODEL] Schema drift across the three trip exports — must reconcile to one schema before stacking:
    - H1 = 10 cols (no platform column)
    - H2 = 11 cols (platform added)
    - H3 = 12 cols (surge_multiplier added)
- [DEFECT] trips_2025_H1: fare_eur stored as text with decimal comma (e.g. "11,40").
- [DEFECT] payment column: inconsistent casing (card / Card / cash / CASH / wallet).

## Vehicles
- [DEFECT] fuel_type: same value written many ways (DSL / Diesel / DIESEL / Hybrid / etc.).
- [DEFECT] status: inconsistent casing (active / Active / sold / Sold).
- [DEFECT] seats: missing values present; null convention here is empty string ''.
- [DEFECT] Duplicated vehicles: VH-016, VH-019, VH-035 each appear twice.
- One duplicate pair has a CONFLICTING odometer reading — cannot drop exact dupes; need a rule (keep highest odometer as cumulative meter). Decision logged.
- [DEFECT] odometer_km contains an impossible -1 value (check min()).

## Platform payouts
- [DEFECT] commission_pct stored as text with % symbol (e.g. "23%") — strip % and /100.
- [CLEAN] Money columns (gross_fares, commission_amount, net_payout): decimal points, no currency symbols — parse directly, no cleaning needed.
- [CLEAN] week_starting: consistent date format (first column that hasn't drifted).
- [MODEL] Two platforms with DIFFERENT fee structures — one charges a fixed fee on top of commission, one doesn't. Effective commission rate must fold in fixed fees, not just use commission_pct, or one platform looks artificially cheaper.

## Fuel
- [DEFECT] litres: decimal comma (e.g. "61,90") — convert to point before parsing.
- [DEFECT] price_per_litre: decimal comma (e.g. "1,808") — TRAP: this is 1.808 €/litre, not 1,808. Must not be read as a thousands separator or every cost is off by ~1000×.
- [DEFECT] total_cost: € symbol + space + decimal comma (e.g. "€ 111,92") — strip all three.
- [DEFECT] station: inconsistent casing (Shell / shell).
- [DEFECT] some litres blank while total_cost is populated — missing value, cost still present.

## Drivers + maintenance

### Drivers sheet
- [DEFECT] Two banner rows above the real header (report title + "Exported by HR System"). Fixed with skiprows=2.
- [DEFECT] Hire Date: three formats in one column (21/08/2018 / 2021-03-19 / 06-Jan-20), plus at least one "unknown". Parse flexibly; treat "unknown" as null.
- [DEFECT] Contract: inconsistent labels for same category (PT / Part-time / Full-time). Needs mapping to canonical values.
- [DEFECT] Active: boolean written five ways (1 / Y / yes / N / no). Map to true/false.
- [DEFECT] Duplicated Driver IDs present (check value_counts).

### Maintenance sheet
- [DEFECT] date: mixed formats across the column, plus some "TBC" values where a date should be — parse flexibly, treat "TBC" as null.
- [DEFECT] job/type: same job written multiple ways — needs mapping to canonical values.
- [DEFECT] cost: currency symbols on some values and not others; same €/comma contamination family as fuel total_cost.
- [DEFECT] workshop: null values present.

## Cross-file / integrity (found by cross-checking files, not single-file profiling)
- [DEFECT] Orphan driver IDs: ~240 trips reference driver IDs absent from the driver sheet. Judgement call — create an "unknown driver" member rather than delete (deleting hides revenue).
- [DEFECT] Negative fares: refunds, not errors. Keep.
- [DEFECT] Zero-distance trips with a fare: cancellations-after-pickup. Keep — real revenue.
- [DEFECT] H3 timestamps carry +03:00 offset — handle timezone or late-evening trips shift date.
# Data Audit Key — do not open until you've profiled the files yourself

Write your own list of everything wrong with the data first. Then compare.
Finding these unaided is the exercise; reading the list is not.

---

## Schema drift across the three trip exports

- `trips_2025_H1` has **no platform column** — the legacy system didn't record it.
  Decide: exclude the period from platform analysis, or impute. Both defensible; say which.
- Fare column renamed twice: `fare_eur` → `fare_amount` → `fare_amount`.
- `trips_2026_H1` adds `surge_multiplier`, absent from earlier periods, and its fares
  are already surge-adjusted.
- **Delimiter changes**: the 2026 file is semicolon-separated, the others comma.

## Date and time formats — four conventions, one concept

- `%Y-%m-%d %H:%M:%S` (2025 H1), `%d/%m/%Y %H:%M` (2025 H2), ISO 8601 with a
  `+03:00` offset (2026 H1).
- Maintenance dates mix `%d/%m/%Y`, `%Y-%m-%d` and `%d.%m.%Y` in the same column,
  plus 30 rows reading `TBC`.
- Driver hire dates mix three formats, plus one reading `unknown`.
- **The `+03:00` offset matters.** Ignore it and late-evening trips shift date.

## Numbers stored as text

- 2025 H1 fares use a **decimal comma**: `11,40`.
- 2025 H2 fares carry an embedded currency code: `EUR 18.44`.
- Fuel litres, price per litre and total cost all use decimal commas; total cost also
  carries a `€` symbol and a space.
- Maintenance costs use four different formats, including a thousands separator that
  will silently truncate values above 1,000 if parsed carelessly.

## Duplicates

- 180 duplicated trip rows in `trips_2025_H2` (a re-submitted batch).
- 3 duplicated vehicles in `vehicles.csv` — one with a **conflicting odometer reading**,
  so de-duplicating on the full row won't catch it.
- 4 duplicated drivers.

## Referential integrity

- ~240 trips in the 2026 file reference driver IDs (`DR-9xxx`) that exist in **no**
  driver record. Real cause: contractors onboarded outside HR. Your call: exclude,
  or create an "unknown driver" dimension member. The second is usually right —
  excluding them silently deletes revenue.

## Impossible and suspicious values

- 90 trips with `distance_km = 0` but a positive fare — cancelled-after-pickup, or
  meter fault. Not automatically deletable; they carry real revenue.
- 55 trips with a **negative fare** (`EUR -12.50`) — refunds. Keep them; they're real.
- One vehicle with `odometer_km = -1`.

## Categorical inconsistency

- `fuel_type`: Diesel / diesel / DSL / DIESEL / " Diesel" / Gasoline vs Petrol /
  Hybrid / HYB / EV / Electric / ELEC — plus one `N/A`.
- `status`: Active / active / ACTIVE / In Service / Sold / sold / Off-fleet.
- `payment`: card / Card / cash / CASH / wallet.
- `Active` flag on drivers: Y / N / yes / no / 1.
- Workshop names: "Central Garage" vs "central garage", "NorthAuto" vs "North Auto".
- Stations: EKO / eko, Shell / shell.

## Whitespace

- ~12% of `pickup_zone` values in 2025 H2 have leading and trailing spaces.
- `" Diesel"`, `"Hybrid "`, `"  Depot B"`.

## Null conventions — five of them

Empty string, `NULL`, `-`, `N/A`, and genuinely absent. A single blank-handling rule
will miss four of the five.

## Structural

- The `Drivers` sheet has **two title rows above the real header** — reading it
  naively gives you a header row of `Meridian Fleet Services...`.
- 70 maintenance rows have blank costs; 90 fuel rows have blank litres but a
  populated total.

---

## The finding the dataset is built around

Cost per kilometre separates sharply by fuel type once fuel and maintenance are
allocated per vehicle — but the pattern is **invisible in fleet averages** and
partly confounded by vehicle age and depot. Electric and hybrid vehicles look
different from diesel on running cost, while maintenance and days-off-road cut the
other way for some individual vehicles.

That is the shape of a real finding: visible only after correct allocation, and
requiring a caveat about what the data can't separate. Say so in the case study.
Overstating it is the single most common mistake in portfolio projects, and any
competent interviewer will probe exactly there.

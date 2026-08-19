# Meridian Fleet Services — Power BI Project Brief

*A simulated client engagement. The client, the company and the data are fictional;
the mess in the data is not — every defect in these files is one that turns up in
real source-system exports.*

---

## 1. The client's email

> Hi,
>
> Thanks for the call. As I said, we run 42 vehicles and about 68 drivers across three
> depots, working through two platforms plus our own direct bookings.
>
> The problem is I genuinely don't know how we're doing. My accountant tells me the
> yearly result and that's it. I need something I can look at every month and actually
> trust — how the fleet is performing, whether the vehicles are earning their keep, and
> whether the platform commissions are worth it. A couple of the newer cars I'm not sure
> about at all.
>
> I've attached what we have. The trip data comes out of the booking system — we changed
> systems in 2025 so the older exports look a bit different. The driver list is from HR.
> The maintenance and fuel stuff is what the office keeps.
>
> Whatever you can do with it. Ideally something I can open myself without calling you.
>
> Thanks,
> The client

That is the entire brief. It is vague, slightly contradictory, and does not tell you
which numbers matter — which is the point. **Turning that into a specification is the
first deliverable, and it is the skill that separates good dashboard work from bad.**

---

## 2. What you're given

| File | Source system | Notes |
|---|---|---|
| `trips_2025_H1.csv` | legacy booking system | no platform column |
| `trips_2025_H2.csv` | new booking system | schema changed mid-year |
| `trips_2026_H1.csv` | new booking system | schema changed again |
| `vehicles.csv` | fleet register | |
| `drivers_and_maintenance.xlsx` | HR export + office spreadsheet | two sheets |
| `fuel_transactions.csv` | fuel card provider | |
| `platform_payouts.csv` | platform statements | two different fee structures |

~45,000 trips across 18 months. Large enough that spreadsheet-only work becomes
painful, which is deliberate.

---

## 3. Your deliverables

1. **A one-page specification.** Before touching the data: which 5–7 questions will the
   dashboard answer, which metric answers each, and what the data cannot support.
   Send this to the "client" — i.e. write it as if you were.
2. **A cleaned, modelled dataset.** Star schema: one trips fact table, dimensions for
   vehicle, driver, date, platform. Costs allocated to vehicles.
3. **A Power BI report**, three pages maximum. Fleet overview, vehicle economics,
   platform comparison.
4. **A written case study** — the artefact that actually goes on your CV and your
   freelance profile. Structure it: the situation, the state of the data, what you
   built, what it revealed, and the decision it enabled.

---

## 4. Suggested metrics

Don't take these as given — decide for yourself which survive contact with the data.

- Revenue per vehicle per active day
- Utilisation: proportion of days each vehicle recorded at least one trip
- Cost per kilometre (fuel + maintenance)
- Contribution per vehicle: net revenue less allocated direct costs
- Effective commission rate by platform, including fixed fees
- Revenue per driver hour, and the spread across drivers
- Days off road, and what that costs

The interesting finding in this dataset is at vehicle level: fleet averages hide it.

---

## 5. Three-week schedule

**Week 1 — understand and specify.** Profile every file before cleaning anything: row
counts, distinct values per column, null rates, date ranges. Write the specification.
Do not open Power BI yet.

**Week 2 — clean and model.** Power Query for ingestion and cleaning. Reconcile the
three trip schemas into one table. Build the star schema. Write your measures in DAX —
a small set done properly beats thirty.

**Week 3 — build, write, publish.** Three report pages. Publish to Power BI Service.
Write the case study. Push the Power Query M and DAX to GitHub with a README.

**Hard stop at three weeks.** The outreach to clubs and academies goes out during week
two, not after this is finished.

---

## 6. What "done" means

Not "the dashboard looks good." Done is: someone who has never seen the data can read
your case study, understand what was wrong with the inputs, and see what decision the
output supports. That is what a client is buying and what an interviewer is testing.

---

## 7. Guard against the obvious trap

Every defect below is recoverable, but some require a judgement call rather than a
rule — negative fares, zero-distance trips with a fare attached, driver IDs in the trip
data that exist in no driver record. **Document the call you made and why.** A client
does not need you to be right about every edge case; they need to know what you did
with it. Writing those decisions down is most of what a data quality note is.

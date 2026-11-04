# Task 2: Contracts, nested data and a star schema

Mohamed Sultan · ID: 58-2621 · Major: MET · Lab: 01

The notebook reads 283 lines, quarantines 24, and retains 259 bookings: four lines have invalid student IDs, four have incorrect totals, three have invalid ticket types, three have quantities below one, three are duplicate booking IDs, three were booked on or after the event date, two have null events, and two have empty ticket lists. Validation precedes deduplication so an invalid first transmission cannot reserve an ID and suppress a later valid correction; an invalid resend also retains its validation reason. The notebook simulates reversing the order: for this supplied file, both the accepted count and all reason counts stay unchanged, but they can change for other inputs. The drifting field is `promo_code`, present in 193 documents from the earliest observed booking timestamp of 20 October 2026 at 01:05 UTC (B0125); its first file occurrence is line 4, because records are not chronological. Unknown fields are allowed and retained; absent optional fields become null in the flattened table. The star contains 314 ticket entries, reconciled to EGP 31,285 across all accepted bookings. Booking-level `total` is excluded from the ticket-grain fact because repeating it for every entry would multiply multi-entry bookings: `SUM(total)` would incorrectly give EGP 42,000. Ticket-level `amount` is additive; the confirmed-sales query sums quantities and amounts per club and ticket type.

## Run

Use Python 3.11 (the tested version), from this repository's directory:

```sh
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Open `task2.ipynb` in VS Code, select the `.venv` Python kernel, and run all cells. The first code cell sets `DATA = "bookings.jsonl"`; change only that assignment to process another input. Every count is computed. The notebook overwrites `quarantine.jsonl` and its generated Parquet outputs, including obsolete month partitions. It never edits the source data.

`star/dim_event.parquet` and `star/dim_student.parquet` hold the dimensions. The fact is stored under `star/fact_ticket_sales/booked_month=YYYY-MM/`. Visible notebook outputs include validation counts, duplicate-order comparison, drift detection, flat tables, the star, confirmed sales, and assertion results.

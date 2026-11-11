# AI Use Log

## Why I used AI

I used ChatGPT as a helper to inspect the assignment instructions, review possible cleaning decisions. I did not treat an AI suggestion as proof that a value was wrong.

## Information provided

- The assignment sheet, *Assignment 2: The Dirty Ledger*.
- The supplied file `teaching_prices_messy.csv`.
- The assignment's requirements to diagnose problems, keep a cleaning log, judge suggested operations line by line, and reflect on decisions.

## Assistance received

AI helped identify checks to run for missing values, duplicate records, inconsistent item capitalization, price conversion, suspicious prices, and expected date-item-city combinations. It also helped draft a notebook that keeps the raw data unchanged and performs changes on a copy.

## What I checked against the file

The following facts were checked directly against the supplied CSV:

- Shape: 97 rows and 6 columns.
- Missing values: {'date': 0, 'item': 0, 'city': 0, 'price': 1, 'unit': 0, 'data_label': 0}.
- Exact duplicate rows: 1.
- Repeated date-item-city keys before cleaning: 1.
- Date count: 12.
- The raw price field contains a comma-formatted value (`1,250`) and a missing value.
- The item column includes a lowercase `oil b` label.
- The file labels its observations as illustrative teaching data, not actual PBS observations.

## How I evaluated suggestions

I kept operations that had direct support, such as removing one exact duplicate. I modified the capitalization operation after checking its effect: normalizing `oil b` to `Oil B` creates a conflicting date-item-city key with a different price, so the original label is retained and the possible match is flagged. I rejected automatic missing-value filling and automatic deletion of extreme prices because there was no evidence for the replacement values or deletion.


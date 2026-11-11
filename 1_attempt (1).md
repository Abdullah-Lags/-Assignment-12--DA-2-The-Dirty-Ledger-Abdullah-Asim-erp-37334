# Initial diagnosis – draft for personal review

**Dataset:** `teaching_prices_messy.csv`  
**Observed shape:** 97 rows × 6 columns  
**SHA-256 of supplied CSV:** `5818dad3807487d4e37f764e5d19cbaa152edd78fe4db182eb28b087c8709d35`


## 1. First look

The columns are `date`, `item`, `city`, `price`, `unit`, and `data_label`. There are 97 records. The file covers 12 distinct dates, from 2026-01-01 to 2026-03-19, and has two city labels. The `price` column was read as text (`object`) rather than a numeric type. This is understandable because the column contains a comma-formatted value (`1,250`) as well as a missing value.

The `data_label` column explicitly says that these are illustrative teaching data, not actual PBS observations. I will preserve that warning.

## 2. Problems noticed and planned decisions

| Issue | Evidence / how to check | Planned decision | Reason |
|---|---|---|---|
| Missing price | `df.isna().sum()` shows 1 missing value in `price` | **Flag and retain** | There is no source value to recover. Filling it with an average would invent a price. |
| Price stored as text | `df.dtypes` shows `price` as `object`; values include `1,250` | **Investigate, then convert carefully** | Remove a thousands separator only when converting the string to a number. Keep the original CSV untouched. |
| Comma-formatted price | The raw value `1,250` appears in the price column | **Convert to 1250 numerically and flag for review** | The comma appears to be a thousands separator, but the resulting price is unusually high compared with nearby entries. Conversion does not prove the value is correct. |
| Extremely high value | The raw ledger includes `9999` | **Flag and retain** | It may be an error, but the dataset does not provide a source to confirm a replacement. Do not delete or change it just because it looks unusual. |
| Inconsistent item capitalization | One row uses `oil b` while the other rows use `Oil B` | **Flag and investigate before standardizing** | The lowercase spelling may be a capitalization issue, but changing it to `Oil B` creates a date-item-city collision with another row that has a different price. I should not merge these records without more evidence. |
| Exact duplicate row | `df.duplicated().sum()` returns 1; the repeated row is the 8 January 2026 Staple A / City 1 record | **Remove one exact repeated record from the analysis copy** | All six fields match. Keeping both would count the same record twice. |
| Repeated date-item-city key | `df.duplicated(subset=['date','item','city']).sum()` returns 1 before label standardization | **Investigate with the exact-duplicate check** | The repeated key is the same repeated row, not evidence of a second distinct observation. |
| Possible unusual item-price pairing | The `oil b` row has price `108.5`, while the other Oil B / City 1 values are much higher | **Flag and retain** | The lowercase label may be a formatting inconsistency, but the price may be a separate issue. There is not enough evidence to replace it. |

## 3. Expected structure check

There are four recurring items and two cities. Across 12 weekly dates, a complete panel would contain:

`12 dates × 4 items × 2 cities = 96 date-item-city combinations`

The raw file has 97 rows, including one exact duplicate. After removing that exact duplicate, the number of rows should be 96. However, standardizing `oil b` to `Oil B` creates a repeated date-item-city key with a different price. This means the apparent four-item structure needs investigation; a clean count alone would hide that conflict.


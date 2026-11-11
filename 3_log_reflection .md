# Cleaning Code Court and Reflection

## A. Cleaning Code Court

The verdicts below apply to the cleaning operations used in `2_work.ipynb`. I treated each operation separately instead of accepting a complete cleaning script without checking it.

| Suggested operation | Verdict | Evidence and reason |
|---|---|---|
| Read the CSV with pandas and make a working copy | **KEEP** | This preserves the original data and makes the process repeatable. |
| Remove all rows containing any missing value | **REJECT** | The file has a missing price. Deleting the whole record would remove the date-item-city observation even though the other fields are present. |
| Fill the missing price with the mean or median | **REJECT** | There is no source evidence for the missing price. A calculated replacement would be invented data. |
| Remove exact duplicate rows | **KEEP** | The raw file contains one fully identical repeated row. Removing one copy avoids counting the same record twice. |
| Convert the price column to numeric after removing commas | **MODIFY / KEEP WITH CHECK** | This is useful because the raw column is text and includes `1,250`. I kept the converted value as 1250 but flagged it for review rather than assuming it is a valid ordinary price. |
| Convert all item labels to a consistent capitalization | **KEEP, narrowly** | Only the observed lowercase `oil b` label is changed to `Oil B`. The original label is retained in `item_original` so the change can be checked. |
| Delete every price above an arbitrary threshold | **REJECT** | A high value is suspicious, but the CSV does not establish the correct value or prove it is an error. The values are retained and flagged. |
| Replace the unusual `oil b` price with a nearby Oil B price | **REJECT** | The price is unusual compared with other Oil B / City 1 rows, but there is no reliable source for a replacement. |
| Check the date-item-city combinations after cleaning | **KEEP** | The file has 12 dates, 4 items and 2 cities, so the expected structure is 96 combinations. This check can reveal missing or repeated combinations. |

### Before-and-after summary

The notebook calculates the comparisons directly from the CSV when it runs. The main expected results are:

- Original rows: **97**
- Original columns: **6**
- Exact duplicate rows: **1**
- Missing price values: **1**
- Rows after removing the exact duplicate: **96**
- Distinct dates: **12**
- Distinct raw item labels: **5** (one is the lowercase `oil b` variant)
- Distinct cities: **2**
- Expected combinations if there are four intended items: **96**
- After testing a normalized label, there are **95 distinct normalized keys** and one repeated key with different prices. This unresolved conflict is kept visible.

The price column changes from text (`object`) to numeric (`float64` in the notebook). The missing price remains missing. The unusual values are retained and flagged.

## B. Reflection

The main difference between a cautious cleaning approach and an automatic one is that not every unusual value should be changed. For example, the value `9999` looks very different from the other prices, and `1,250` also needs checking. It would be easy to delete both values or replace them with averages, but that would hide the original evidence. I decided to keep them and add a review flag because the dataset does not tell me what the correct prices should be.

I also treated the missing price differently from a zero. A missing value means that the price was not supplied. It does not mean that the item was free. Filling it with an average would make the table look complete but would add a value that was never recorded.

The duplicate was different. All fields in the repeated row match, so removing one copy is supported by direct evidence. The lowercase `oil b` label looked like a formatting issue at first. However, when I tested the standardized spelling, it created a second record for the same date, item and city as an existing `Oil B` row, and the two prices were different. That is why I changed my approach: I kept the original label and flagged the possible match instead of merging it. This is a good example of why even a small-looking cleaning change needs to be checked.

If I had used AI without checking it, I might have accepted broad instructions such as dropping every row with missing data or removing every extreme value. That could have caused valid observations to disappear. The most useful lesson from this task is that cleaning is not just about making a dataset look neat. Each change needs a reason, and sometimes the most honest decision is to leave a questionable value in place and explain why it needs further checking.

**Personal verification before submission:** I should compare this reflection with the decisions I actually made during my own THINK stage and adjust the first-person wording if it does not describe my process accurately.

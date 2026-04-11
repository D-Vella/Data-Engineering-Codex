# Layered Documentation Strategy

**System/Area:** Data Engineering — Best Practices  
**Tags:** documentation, docstrings, unity catalog, readme, best practices, maintainability

---

## What It Is

A deliberate approach to documentation that operates at three distinct levels — function, framework, and catalog — each serving a different audience with a different need. The goal is not to document everything exhaustively at every level, but to put the right information in the right place for the right person.

---

## Why Layering Matters

A single notebook header cannot serve everyone. The engineer debugging a function needs something different from the analyst trying to understand a table in the catalog, who needs something different from the person inheriting an entire ingestion framework. Trying to serve all three audiences in one place means serving none of them well.

The three layers map roughly to:

| Layer | Audience | Question It Answers |
|---|---|---|
| Function (docstrings) | Engineers reading or reusing code | What does this do, what does it need, what does it return? |
| Framework (README) | Engineers maintaining or inheriting a system | How does this whole thing work and what do I need to know before touching it? |
| Catalog (table & column comments) | Anyone consuming the data | What is this table, what does this column mean, when was it last updated? |

---

## Layer 1 — Function Level (Docstrings)

Add a docstring to every function. Use Google style for compactness:

```python
def calculate_retention(df, customer_col, date_col, period_days=30):
    """
    Calculates customer retention rate over a rolling period.

    Args:
        df: Input Spark DataFrame containing customer transaction data.
        customer_col (str): Column name containing the customer identifier.
        date_col (str): Column name containing the transaction date.
        period_days (int): Rolling window size in days. Defaults to 30.

    Returns:
        DataFrame: Aggregated retention metrics with one row per customer.

    Raises:
        ValueError: If customer_col or date_col are not present in df.
    """
```

**What to always include:**
- What the function does in one sentence
- Every argument — name, type, and what it represents
- What it returns — type and a brief description
- What it raises — any meaningful failure conditions

**The `Raises` section** is the one most people omit and most wish was there when something breaks in production. If a function has meaningful failure conditions, document them.

**A useful instinct:** if you find yourself writing a long inline comment to explain a block of code inside a function, consider whether that block should be extracted into its own named function instead. A well-named function is often the best documentation.

---

## Layer 2 — Framework Level (Dedicated README)

Any framework responsible for ingesting or moving data gets its own README file. A notebook header is not sufficient here — an ingestion framework is the kind of thing where someone needs to understand the whole picture before touching any individual piece of it.

**Recommended README structure:**

```markdown
# Framework Name

**Purpose:** What this ingests and why it exists

---

## Architecture Overview
Brief description of how it works end to end. A simple diagram 
or flow description is worth more than paragraphs of prose here.

## Trigger & Schedule
How and when this runs. Is it scheduled? Event-driven? Manual?

## Sources & Destinations

| Source | Type | Description |
|---|---|---|
| `schema.source_table` | SQL Table | What this contains |

| Destination | Type | Description |
|---|---|---|
| `schema.output_table` | SQL Table | What gets written here |

## Dependencies
What must exist or have run before this framework executes.

## Failure & Recovery
What happens when it fails. How to rerun it safely. 
Whether reruns are idempotent. Who to notify.

## Known Issues / Quirks
Anything non-obvious. Workarounds in place. Decisions that look 
wrong but were deliberate.

## Change Log

| Date | Author | Change |
|---|---|---|
| YYYY-MM | Name | Description of change |
```

**The Failure & Recovery section** is the most commonly skipped and the most valuable when something breaks at 7am. Write it while the context is fresh — it is almost impossible to write well after the fact.

---

## Layer 3 — Catalog Level (Unity Catalog Comments)

Unity Catalog comments surface directly in the Catalog UI, meaning anyone browsing the catalog can understand a table or column without finding the notebook that created it. This decouples documentation from code, which matters because data consumers are often not the people who read notebooks.

**Table comments** should cover purpose, grain, and refresh cadence:

```sql
COMMENT ON TABLE gold.customer_retention IS 
'One row per customer per month. Retention metrics calculated on a 
30-day rolling window. Refreshed daily at 06:00 UTC by the 
customer_metrics pipeline.';
```
> Note that while the linage system shoudl cover where the data for a table comes from, don't rely on it. I have known systems where it fails.

**Column comments** should focus on anything not self-evident from the name — units, what null means, valid values, business definitions:

```sql
ALTER TABLE gold.customer_retention 
ALTER COLUMN retention_rate COMMENT 
'Proportion of customers active in the current period who were also 
active in the prior period. Expressed as a decimal between 0 and 1. 
Null indicates insufficient history (fewer than 30 days of data).';
```

**The key instinct:** even columns with obvious-seeming names like `is_active` or `status` need comments, because what those values *mean* in your specific business context is almost never as universal as it seems when you are writing them. Document the definition, not just the name.

## Layer 3 — Catalog Level (Reporting Catalog Comments)
Many reporting systems like Power BI, Tableau and SSRS have the ability to give a data source a description or commentary. Because these systems shouldn't be used for data engineering themselves the comments for these objects should allow someone to quickly and easily find the source object in another system. For example if the source is Unity Catalog in Databricks, you should give a fully qualified table name including workspace (if needed). 

---

## Notes / Gotchas

- The three layers are complementary, not redundant — resist the temptation to collapse them
- Catalog comments are the most visible to the widest audience and the most neglected — prioritise them for any table that others consume
- Docstrings pay double dividends in IDEs and notebooks that surface them as hover tooltips
- Framework READMEs should live in the same repository or workspace as the framework itself — documentation that lives somewhere else will drift out of sync
- Write the Failure & Recovery section of a framework README immediately after you have dealt with a real failure — that is when you know exactly what you wish had been written down

---

## References

- [Google Python Style Guide — Docstrings](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings)
- [Databricks Unity Catalog documentation](https://docs.databricks.com/en/data-governance/unity-catalog/index.html)

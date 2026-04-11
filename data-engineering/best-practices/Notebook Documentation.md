# Notebook Documentation

**System/Area:** Data Engineering — Best Practices  
**Tags:** documentation, notebooks, best practices, maintainability

---

## What It Is

A practical guide to documenting notebooks at a level that is genuinely useful without being burdensome. Written for the person who will inherit a broken notebook six months from now — which is usually you.

---

## The Core Principle

Write for the person who has zero context and is stressed because something is broken. The question to ask at the end of every notebook: **"If I came back to this in six months having completely forgotten it existed, what would I desperately wish someone had told me?"**

---

## The Four Things Worth Documenting

Most documentation advice tries to get you to document everything. That is why nobody does it. Focus on just these four:

**1. Purpose — what this notebook does and why it exists**  
One short paragraph at the top. Not *how* it works, just *what* it does and *why* it was built. This is the single most valuable thing you can write.

**2. Inputs and outputs — what goes in and what comes out**  
What data sources does this read from? What does it produce — a table, a file, a model? Where does that output go? If the inputs change or disappear, this is what tells someone where to start looking.

**3. Assumptions and dependencies**  
What has to be true for this to run? What has to exist or have run before it? These are the things that will bite someone when they are no longer true. Write them down explicitly.

**4. Gotchas and known quirks**  
The things that are not obvious. Why you did something a particular way. The weird edge case you handled. The thing that broke once and how you fixed it. This is the documentation nobody ever writes and everyone always wishes existed.

---

## Notebook Header Template

Place this as a Markdown cell at the very top of every notebook:

```markdown
# Notebook Title

**Purpose:**  
One or two sentences. What does this do and why does it exist?

**Owner:** Your Name  
**Last Updated:** YYYY-MM  
**Status:** Active / Deprecated / In Development

---

## Inputs

| Source | Type | Description |
|---|---|---|
| `schema.table_name` | SQL Table | What this contains |
| `/path/to/file` | CSV | What this contains |

## Outputs

| Destination | Type | Description |
|---|---|---|
| `schema.output_table` | SQL Table | What gets written here |

---

## Dependencies & Assumptions

- Assumes `other_notebook` has run successfully first
- Requires access to X database
- Expects the source table to be refreshed daily by Y process

---

## Known Issues / Quirks

- Note anything non-obvious here
- Why you made an unusual decision
- Any workarounds in place for known problems
```

---

## Inline Comments — The Right Level

The most common mistake is narrating the code — explaining *what* it does when anyone can read that themselves. What is actually useful is explaining *why*.

**Not useful:**
```python
# Filter out nulls
df = df.filter(df.customer_id.isNotNull())
```

**Useful:**
```python
# Nulls in customer_id indicate test records from the staging environment
# and should never reach the output table
df = df.filter(df.customer_id.isNotNull())
```

**The rule:** comment on intent and reasoning, not mechanics. If the code is clear enough to read, it does not need a comment explaining what it does — it may well need one explaining why.

---

## Section Headers

Break the notebook into named sections using Markdown cells. A reader should be able to skim just the headers and understand the flow without reading any code.

```markdown
## 1. Setup & Imports
## 2. Load Source Data
## 3. Clean & Validate
## 4. Apply Business Logic
## 5. Write Output
```

This costs almost nothing to add and makes navigation dramatically easier for anyone coming to the notebook cold.

---

## The Change Log (Optional but Valuable)

For any notebook that runs in production or gets revisited regularly, a simple change log at the bottom means you can understand why the notebook is the shape it is today:

```markdown
## Change Log

| Date | Author | Change |
|---|---|---|
| 2026-04 | Name | Added null handling for customer_id |
| 2025-11 | Name | Updated source table from v1 to v2 |
```

Not needed for throwaway or one-off notebooks. Worth adding for anything long-lived.

---

## The Minimum Bar

Rather than aiming for perfect documentation every time, hold yourself to this at the point you finish a notebook:

- The top cell explains what it does and why in plain English
- Inputs and outputs are listed
- Any non-obvious decision has a one-line comment explaining why
- Section headers exist so someone can navigate without reading everything

That is it. Hit that bar consistently and you will be in the top few percent of people documenting their work.

---

## Notes / Gotchas

- Avoid documenting *what* the code does — document *why* it does it that way
- Status field in the header matters — "Deprecated" saves someone hours of confusion
- If you find yourself writing a long comment to explain a block of code, consider whether that block should be a named function instead — a good function name is often the best documentation
- Date your last updated field as `YYYY-MM` rather than a full date — you are more likely to keep it current if it only needs a month and year

---

## References

- [Write the Docs — Documentation principles](https://www.writethedocs.org/guide/)

# Data Engineering — Patterns

Architectural and pipeline patterns for data systems.

---

## Index

*No entries yet.*

---

## Short topics:

### Incremental load strategies:
This is going to be source system dependant as a lot of this will depend on having some kind of "watermark" column. These columns (usualy numeric or timestamp data types) allow you to filter you data source to get only the records you are missing.

If you have a source that only adds new data to it, its simple to do something like `SELECT record_id, record_content WHERE record_id > last_known_record_id`. This means that both the source system and the pipeline are having to only deal with small data sets (the new records).

Many systems however are not append only and you have to deal with `UPDATE` and `DELETE` oprations too. This will depend on how your source system does this. Infor LN (an ERP system) maintains a log similar to SQL where records have `sequenceNumber`s assosciated and when a change happens that number is updated. In this case I get all records based on the created date of each records. This allows me get a complete set of changes and then "play back" the logs to get to the point of the latest `sequenceNumber` for each record.

Where possible I try to reference the table that the pipeline is writing to in order to capture the watermark. 

---
## Future Topics:

- ELT vs ETL trade-offs
- Slowly changing dimensions (SCDs)
- Event-driven pipeline patterns
- Partitioning strategies
- Orchestration patterns (e.g. DAG design)
- Standard Pipeline fetures (e.g kill switch)

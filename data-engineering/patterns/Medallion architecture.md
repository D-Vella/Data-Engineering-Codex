# The Medallion Architecure

## Description:
This is a way of organising a data warehouse into different teirs of Bronze, Silver & Gold that was coind by Databricks and they have a explainer on [their blog](https://www.databricks.com/blog/what-is-medallion-architecture). This is to help understand how the data is processed thoough the data warehouse and have divisions between the teirs to stop issues propergating. It should be noted that the architecture expects you to follow the Extract Load Transform methodology with the Load step being Bronze and Transform happening in Silver & Gold.

*PLEASE NOTE:* This is a guideline and not a set of hard and fast rules. In order to make your platform work, feel free to take this concept and mould it to your needs. My own experiance has shown I need 2 sub teirs as an example.

## Overview:
You have at base 3 teirs
- Bronze - This is raw data.
- Silver - This is cleaned / transformed data.
- Gold - This is data ready to be use by the business or external systems.

## Teirs in Detail:

### Bronze:
This is raw data. Data here will have minimal work done and schemas are likely to change. The idea here is to capture and land the data in the platform as it came to you. Minimal data validation is performed, and it's recommended to store most fields as string, VARIANT, or binary to protect against unexpected schema changes. Metadata columns might be added, such as the provenance or source of the data. 

Because of the lack of data quality check such as data validation and typing, reading from this should only be done by the silver layer. 
### Silver:
This is where the majority of data cleaning will take place. A silver layer entity may read from multiple bronze ones. Common operations that are carried out in the Silver Layer include:
- Deduplication
    - A Bronze table may have multiple versions of the same record over time. Silver transformations would remove all but the current record.
- Data Validation
    - Business rules would be enforced here. 
    - Very corse filters can be applied here. For example, if you ingested a general ledger into Bronze you may have multiple cost centre codes removed. These are unlikely to change and would be true for the gold layer items that will read from this.
- Schema enforcement
    - Dates and times will be stardardized.
    - Columns will be renamed.
    - Column data types will be changed to be correct to the data. 

At this point you should have an entity that has been cleaned with a low-granularity (for example 1 row is a single transaction in a ledger) and is ready to used by someone like a data scientist directly. Only use cases that need this granularity would use it, others would opt for gold.

### Gold:
The entites in this layer are ready to be used by downstream consumers like dashboards or applications.

Common operations can include:
- Aggregation
    - Common in Financial objects where having the granular information may overload a dashboard system. The gold layer would have data pre-aggregated to the day level to lessen the load.
- Star Schema modelling
    - You would create fact and dimension tables with keys between the different entities in gold. In my own experiance this requires a lot of slicing and dicing of multiple silver layer objects. This depends if this is the data engineering stadard that the business follows.
- Use case specfic entities
    - Some reporting systems need the data shaped in such a way for them to work. These unique tables would be defined here.

## My own experiance - Sub Teirs:
I have found that the model can break in two ways:
1. A considerable amount of work needs to be done to get the data ready to be used by silver (often casued by converting between technologies).
2. Converting between system aligned entites (finance system contains data in 3rd Normal Form) to business aligned entities (Finance think of a General Ledger as a single entity).

To get around this issue I implemented Sub Teirs. The total breakdown looks like this:
- Bronze_Raw - This is raw data as in comes into the system. This maybe saved straight to a table or stored as files.
- Bronze_Staging - Everything here will be in a certain format and ready to use by the next layer.
- Silver_Raw - Standard silver data cleaning operations applied.
- Silver_Staging - This is where the change between system alignment and business alignment happens.
- Gold_Mart - Dimensionaly modeled datasets ready to use by clients.
- Gold_External - 'Unique' use cases that demand significant data shape change or for some reason cannot use Gold_Mart.

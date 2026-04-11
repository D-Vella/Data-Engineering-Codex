# Data Engineering — Best Practices

Principles and habits worth applying consistently across data projects.

---

## Index

*No entries yet.*

---

## Short topics:

### Idempotency and why it matters:
Any object can be considerd as Idempotent when it is executed multiple times, the same result occurs. This is not defined in the way of output but more in terms of function and state.
As an example; assume you have a notebook that generates a balance sheet and saves it to a table. If you run it with the same inputs you should get the same balance sheet, this is Idempotency in terms of function. The table that the notebook saves the information to should only have 1 copy of that balance sheet, this is Idempotency in terms of state.

For Data engineering Idempotency is important as needed to re-run a pipeline, notebook or function is a frequent occurance and may even be automatic depending on the system in question. Knowing that these objects are idempotent allows for these reties to be done safely.

### Testing data pipelines:
Testing a pipeline should be done as close to real world as possible. This should use the same compute configuration, data volume, constraints and expectations that would be in production. To this end having a test environment that reflects production closely is important. When you run the pipeline for the first time take note of the following things:

 - Where and why does it fail?
 - What tasks take the longest?
 - How long does the pipeline as a whole take to run?
 - How is compute resource consumption?

 When you run subsequent tests you should note the above again and compare to how things differ. Many pipelines will only load incremental data so the time to run the pipeline should drop.
---

## Topics to add

- Schema design principles
- Data quality and validation approaches
- Naming conventions for tables, columns, pipelines
- Logging and observability in pipelines

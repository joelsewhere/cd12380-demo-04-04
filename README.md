## `transactions` (Dynamic Schema)

**Glue script (`glue_script.py`)**
- Added an **automatic schema evolution step**: on subsequent runs, computes the set difference between staging and target columns and issues `ALTER TABLE ... ADD COLUMN` for each new column, using each column's Spark type via `simpleString()`. New columns become part of the Iceberg table before the `MERGE INTO` runs.

**SQL templates**
- Switched `transactions.orders` from an explicit column list to `SELECT *`, so any new columns crawled into `raw.orders` (e.g. `delivery_window`, `gift_message`) automatically flow into `transactions.orders` instead of being dropped at the SQL boundary.

**Net effect**
Schema drift in the source now propagates end-to-end: the crawler picks up the new column in `raw`, `SELECT *` carries it through the SQL template, and the Glue script adds it to the Iceberg target before merging — no manual DDL or template edits needed when columns are added at the end.
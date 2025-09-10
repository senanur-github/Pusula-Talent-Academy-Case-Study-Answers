## Scenario:

A table below has been used for 5 years in a Hospital Information Management System (HBYS). Each day, about 25,000 rows are inserted.
Recently, queries on this table have become slower and users have reported difficulty accessing past records.

```sql
CREATE TABLE HastaIslemLog (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    HastaId INT,
    IslemTarihi DATETIME,
    IslemKodu NVARCHAR(20),
    Aciklama NVARCHAR(500)
);
```

## Question:

### 1. What could be the reasons for the performance degradation?

As the amount of data has increased over the years, indexing solely on the “Id” column (which is a clustered index and primary key) has become insufficient for query performance.

The field that takes up the most space in the table is the “Description” column, which has the NVARCHAR(500) data type. Depending on its usage, this can strain system resources in terms of both disk space and memory usage.

Furthermore, storing all records in a single table for 5 years negatively impacts access and query performance.

---

### 2. What improvements would you suggest for better sustainability?

To ensure the system's sustainability:

The table can be divided into yearly or monthly segments. This enables quick access to current data and reduces query times. Old data can be moved to archive tables or another database.

In addition to the primary key, non-clustered indexes can be defined for frequently queried columns such as PatientId, TransactionDate, and TransactionCode. Furthermore, composite indexes can be considered for frequently used query patterns.

By examining the use of the "Aciklama" field, the data type can be reduced to a smaller size, or if the operation code provides sufficient information, the description can be moved to a separate table to reduce the load on the main table.

It should be checked whether the CPU, RAM, and storage resources can handle the current and projected data volume.

---

### 3. Do you think using the table in this way for 5 years was the correct approach? Why or why not?

Using a single table for 5 years with daily inserts of 25,000 rows was not an optimal approach for several reasons:

- Data volume growth: Over time, the table grows very large, which slows down queries, especially those on non-indexed columns. Relying only on the primary key is insufficient.

- Maintenance difficulty: Managing indexes, backups, and database maintenance becomes harder with such a large table. Fragmentation and storage issues are more likely.

- User access patterns: Most users are likely to query recent data more often than very old records. Keeping all records in one table unnecessarily affects performance for the most common queries.

A better approach would have been partitioning the table by year or month from the beginning, archiving old data, and defining indexes based on frequent queries. This would maintain performance, simplify maintenance, and improve overall system sustainability.

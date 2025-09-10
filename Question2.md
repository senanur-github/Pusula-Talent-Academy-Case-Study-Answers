## Scenario:

The following query is frequently used by end users:

```sql
SELECT *
FROM HastaKayit
WHERE LOWER(AdSoyad) LIKE '%ahmet%' AND YEAR(KayitTarihi) = 2024
```

## Question:

### 1. What performance problems might arise from this query?

When the expression “YEAR(KayitTarihi) = 2024” is used, the query has to parse the date column for each row to perform the calculation. This prevents index usage and causes a table scan.

Since the expression “LIKE ‘%ahmet%’” starts with a %, index usage is not possible in any way, and a table scan is performed.

When SELECT \* is written, unnecessary columns are also retrieved, resulting in extra I/O operations and decreased performance.

When the “LOWER(AdSoyad)” expression is used, if there is a clustered or non-clustered index on the relevant column, the index is not used. Instead, a table scan is performed. Therefore, the defined index status should also be taken into account.

---

### 2. How would you optimize this query and/or the table structure?

For the date filter, write "KayitTarihi >= '2024-01-01' AND KayitTarihi < '2025-01-01'". This allows the index to be used and significantly improves query performance.

A full-text index can be created and a query such as "CONTAINS(FirstNameLastName, 'ahmet')" can be used. Using "ahmet%" is also an option, but in this case, a user whose second name is ahmet cannot be accessed. Purpose-driven solutions can be evaluated.

Instead of SELECT \*, only the required columns should be selected.

---

### 3. Are there any improvements that could be made on the application side?

On the application side, user inputs can be normalized (for example, converted to lowercase) during searches and sent to the database in that form. This eliminates the need to use functions like LOWER() on the query side.

When a user wants to filter by year, the application side can convert this directly into a date range and send it to the database (for example, '2024-01-01' and '2025-01-01' instead of 2024). This prevents the use of the YEAR function and allows for an index seek.

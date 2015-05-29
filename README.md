# postgres-syntax-cheatsheet
some common syntax examples i keep forgetting

Containment

```sql
select * from SOME_TABLE where SOME_ARRAY_COLUMN @> '{Red, Blue}';
```

Subqueries
```

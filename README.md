# postgres-syntax-cheatsheet

> Some common PostgreSQL syntax examples I keep forgetting

### Containment

```sql
select * from SOME_TABLE where SOME_ARRAY_COLUMN @> '{Red, Blue}';
```

### Returning nested data as JSON

```sql
select
  id, created_at, name,
  (select to_json(u) from (select id from users where users.id = user_id) as u) as user,
  (select to_json(d) from (select id from dbs where dbs.id = db_id) as d) as db
from snippets
where user_id = $1
```

```
 id |          created_at           |             name             |   user   |    db
----+-------------------------------+------------------------------+----------+----------
  5 | 2015-05-22 17:42:40.368898-05 | dsadasda                     | {"id":1} | {"id":1}
  7 | 2015-05-22 17:45:08.840262-05 | dsadasda                     | {"id":1} | {"id":1}
  8 | 2015-05-22 17:47:23.528823-05 | poop                         | {"id":1} | {"id":1}
```

This is much better than returning foreign keys like `user_id`, `db_id`

```js
[
  { 
    id: 5, 
    created_at: '2015-05-22 17:42:40.368898-05', 
    name: 'dsadasda', 
    user_id: 1, 
    db_id: 1 
  }
]
```

If you wanted more user or db info, you'd have to add more top level keys like `user_name`, `database_name`. This is shitty.

```js
[
  { 
    id: 5, 
    created_at: '2015-05-22 17:42:40.368898-05', 
    name: 'dsadasda', 
    user_id: 1, 
    user_name: 'Preston', 
    db_id: 1, 
    db_name: 'foo' 
  }
]
```

Instead, using the above nested json querying we can provide better hierarchy:

```js
[
  { 
    id: 5, 
    created_at: '2015-05-22 17:42:40.368898-05', 
    name: 'dsadasda', 
    user: { id: 1, name: 'Preston' }
    db: { id: 1, name: 'foo' }
  }
]
```

### Querying JSON

get key as text

```
select person->>'dogs';
```

get nested keys, note that `->` returns value as json

```
select person->'dogs'->0->'breed' from people where id = 77;
```

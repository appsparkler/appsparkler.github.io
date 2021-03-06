---
layout: blog
title: Couchbase
tags: Couchbase
---

### GET all results without nesting in bucket name:
```sql
SELECT t.*
FROM `<bucketname>` t
WHERE t.id
```

### GET deep nested results with `UNNEST`:
Example Data:
```json
{
  "id": "1234-abcd",
  "primaryTags": [
    {"id": "12-primary-tag"},
    {"id": "24-primary-tag"}
  ]
}
```
```sql
SELECT t.*
FROM `<bucketName>` t
UNNEST t.primaryTags primaryTag
WHERE t.id="<id-on-t>" AND primaryTag.id="<id-on-a-primary-tag>"
```

### DELETE items
```sql
DELETE
FROM `tesla-cms`
WHERE id
```

### UPDATING a nested object
```sql
UPDATE `<bucket-name>`

SET nestedItem.status="new-status"

FOR nestedItem in items
 WHEN nestedItem.id IN ["item-1-id","item-2-id"]
END

WHERE type="abcd"
```

### SELECT
This query utilizes a dummy scan to generate the projection:
```sql
SELECT
  "USER::001" AS _k,
  {
    "firstName": "John",
    "lastName": "Smith",
    "id": "001",
    "type": "user"
  } AS _v
```

### CREATING PRIMARY INDEX
This query will create a `primary-index` on bucket named `default`.
```sql
CREATE PRIMARY INDEX `default-primary-index` ON default
```

### UPDATING multiple documents
The `WHERE pageOrFragment.id IN ["id-1", "id-2", "id-3"]` will return multiple documents which will get updated.
```sql
UPDATE `tesla-cms` pageOrFragment
SET pageOrFragment.status={"isPagePublished": true, "timeStamp": 00001}, pageOrFragment.statusModifedBy={"firstName": "ABC", "lastName": "DEF"}
WHERE pageOrFragment.id IN ["id-1", "id-2", "id-3"]
```

### RETURNING after done
We can also return the results after an operation such as `UPDATE`:

```sql
UPDATE `tesla-cms` item
SET item.isPublished=true
WHERE item.id IN ["id-1", "id-2", "id-3"]
RETURNING item.*
```

### LIMIT 
We can limit the results - this is good to improve performance:
```sql
SELECT item.*
FROM `tesla-cms`
WHERE id
LIMIT 1
```
# TTL document

```sql
INSERT INTO `tesla-cms` (key, value) 
VALUES (
  "publish:123", 
  { "id": "publish:123" }, 
  { "expiration": 2 }
) 
RETURNING `tesla-cms`.*
```

# `CBIMPORT`

### Importing data with `JSON`:
`cbimport json -d <file-path> -b <bucket-name> -u <username> -p <password> -c <cluster> -f list --generate-key %id%`

For ex: `cbimport json -d file://dev-data.json -b tesla-cms -u dbuser -p dbuser -c localhost:8091 -f list --generate-key %id%`

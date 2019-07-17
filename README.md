# Future-Reference

##  OpenCV
https://yoyoinwanderland.github.io/CV-Applications/

## Some Useful Bigquery
1. BigQuery: Delete element in nested struct

reference: https://stackoverflow.com/questions/47707076/how-to-delete-update-nested-data-in-bigquery

- Original: described in json(?) format

Column Name: Original
```
[{
  "key": "A",
  "value": {"a": 123, "b": 456, "c": 789}
},
{
  "key": "B",
  "value": {"a": 123, "b": 456, "c": 789}
},
{
  "key": "C",
  "value": {"a": 123, "b": 456, "c": 789}
}]
```
- Query
```
SELECT 
  array(
    SELECT AS STRUCT
      key, STRUCT(a, b, c) value
      FROM (
        SELECT AS STRUCT
          key,
          CASE 
            WHEN key="A" OR key="B"
            THEN null
            ELSE value.a 
            END a,
          value.b,
          value.c
        FROM UNNEST(Original)
        )
    ) OriginalUpdate,
FROM table
```
- Output

Column Name: OriginalUpdate
```
[{
  "key": "A",
  "value": {"a": null, "b": 456, "c": 789}
},
{
  "key": "B",
  "value": {"a": null, "b": 456, "c": 789}
},
{
  "key": "C",
  "value": {"a": 123, "b": 456, "c": 789}
}]
```

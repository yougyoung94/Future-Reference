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
UPDATE table
SET Original = 
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
    )
WHERE TRUE
```
- Output

Column Name: Original
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
1-1. BigQuery: change key, value in nested struct
- Original: described in json(?) format

Column Name: Original
```
[{
  "key": 123,
  "value": {"a": "A", "b": 456, "c": 789}
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
UPDATE table
SET Original = 
  array(
    SELECT AS STRUCT
      key, STRUCT(a, b, c) value
      FROM (
        SELECT AS STRUCT
          CASE 
            WHEN key IN ("A", "B", "C")
            THEN key
            ELSE value.a 
            END key,
          CASE
            WHEN value.a NOT IN ("A", "B", "C")
            THEN value.a
            ELSE key
            END value.a,
          value.b,
          value.c
        FROM UNNEST(Original)
        )
    )
WHERE TRUE
```
- Output

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
1-2. BigQuery: drop an element in nested struct
- Original: described in json(?) format

Column Name: Original
```
[{
  "key": 123,
  "value": {"a": "A", "b": 456, "c": 789}
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
UPDATE table
SET Original = 
  array(
    SELECT AS STRUCT
      key, STRUCT(a, b, c) value
      FROM (
        SELECT AS STRUCT
          key,
          value.a,
          value.b,
          value.c
        FROM UNNEST(Original)
        WHERE key in ("B", "C")
        )
    )
WHERE TRUE
```
- Output

Column Name: Original
```
[{
  "key": "B",
  "value": {"a": 123, "b": 456, "c": 789}
},
{
  "key": "C",
  "value": {"a": 123, "b": 456, "c": 789}
}]
```

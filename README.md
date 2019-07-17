# Future-Reference

##  OpenCV
https://yoyoinwanderland.github.io/CV-Applications/

## Some Useful Bigquery
1. BigQuery: Delete element in nested struct
reference: https://stackoverflow.com/questions/47707076/how-to-delete-update-nested-data-in-bigquery

- Original: described in json format
₩₩₩
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
₩₩₩
\[{"key":"A", 
   "value"}]

SELECT event_date,
  event_timestamp,
  event_name,
  event_params,
  event_previous_timestamp,
  event_value_in_usd,
  event_bundle_sequence_id,
  event_server_timestamp_offset,
  user_pseudo_id,
  array(
    SELECT AS STRUCT
      key, STRUCT(string_value, int_value, float_value, double_value, set_timestamp_micros) value
      FROM (
        SELECT AS STRUCT
          key,
          CASE 
            WHEN key="user_id" OR key="firstName"
            THEN null
            ELSE value.string_value 
            END string_value,
          value.int_value,
          value.float_value,
          value.double_value,
          value.set_timestamp_micros
        FROM UNNEST(user_properties)
        )
    ) user_properties,
  user_first_touch_timestamp,
  user_ltv,
  device,
  geo,
  app_info,
  traffic_source,
  stream_id,
  platform,
  event_dimensions
FROM `gelato-675ce.analytics_153153500.copy`

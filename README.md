# SQl_Queries
I have been working on improving my SQL knowledge.  Here are a few of my most recent queries


This first query is using the Austin Bikeshare dataset in BigQuery.  The goal is to find the station where the longest bikeshare ride started:

WITH longest_used_bike AS (
    SELECT
      bikeid,
      SUM(duration_minutes) AS trip_duration
    FROM
      bigquery-public-data.austin_bikeshare.bikeshare_trips
    GROUP By
      bikeid
    ORDER BY
      trip_duration DESC
    LIMIT 1
)

##fnd that station at which the longest bikeshare ride started

SELECT
  trips.start_station_id,
  COUNT(*) AS trip_ct
FROM
  longest_used_bike AS longest
INNER JOIN
  `bigquery-public-data.austin_bikeshare.bikeshare_trips` AS trips
ON 
  longest.bikeid = trips.bikeid
GROUP BY
  trips.start_station_id
ORDER BY
  trip_ct DESC
LIMIT 1

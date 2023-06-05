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


##For this Query I pulled information in regards to different warehouses.  Including their ID, name, and the numbe rof orders associated with each warehouse.  I then used the CASE Function to calculate a fullfillment summary based on the ratio of orders fulfilled to the tolal number of orders in the system

SELECT  Warehouse.warehouse_id, 
CONCAT(Warehouse.state, ':', Warehouse.warehouse_alias) AS warehouse_name,
COUNT (orders.order_id) AS number_of_orders, 
(SELECT   COUNT(*)  FROM warehouse_orders.orders orders)   AS total_orders,
CASE    
    WHEN COUNT (orders.order_id)/(SELECT COUNT(*) FROM warehouse_orders.orders orders) <= 0.20    
    THEN "fulfilled 0-20% of orders"   
    WHEN COUNT (orders.order_id)/ (SELECT COUNT(*) FROM warehouse_orders.orders orders) >=0.20   
    AND COUNT (orders.order_id)/ (SELECT COUNT(*) FROM warehouse_orders.orders orders) <=0.60   
    THEN "Fulfilled 21-60% of orders"
    ELSE "Fullfilled more than 60% of orders"  
    END AS fullfillmet_summary
FROM warehouse_orders.Warehouse Warehouse
LEFT JOIN warehouse_orders.orders orders  on orders.warehouse_id = Warehouse.warehouse_idGROUP By  Warehouse.warehouse_id,  warehouse_name
HAVING  COUNT (orders.order_id  ) >0

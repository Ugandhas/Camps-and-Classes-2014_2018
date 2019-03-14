# Camps-and-Classes-2014_2018
Pull Events with guest status billed and reserved and here if and event has multiple sessions is considered to be one.  (like events might have 5 to 10 classes and so people can register for at least one to all of them but they are considered to be only one count for that entire event) After making multiple changes and analysis the below query now pulls two sets of info CountTotal by Mustafa has the final result and Cancat Total of mine has 10% errors. 
Uday,
Please use the following updated query and let me know if you are getting the values correct in "CountTotal". I didn't make any changes in your "CancatTotal0" so you can make any changes you want.

SELECT 
  YEAR,
  MONTH,
  eventtype,
  eventname,
  event_number,
  CancatTotal0,
  CountTotal 
FROM
  (SELECT 
    YEAR(a.date) YEAR,
    MONTH(A.DATE) MONTH,
    b.name eventtype,
    a.name eventname,
    event_number,
    COUNT(DISTINCT TAG_NAME) TagTotal,
    COUNT(
      DISTINCT CONCAT(
        CAST(guest_id AS CHAR),
        CAST(guest_no AS CHAR),
        TAG_NAME
      )
    ) CancatTotal0,
    COUNT(
      DISTINCT CONCAT(
        CAST(guest_id AS CHAR),
        CAST(guest_no AS CHAR),
        CAST(IFNULL(pos_check_id, 0) AS CHAR),
        TAG_NAME
      )
    ) CancatTotal,
    COUNT(d.reservation_detail_id) CountTotal 
  FROM
    es_events a 
    JOIN es_event_schedule es 
      ON (a.event_id = es.event_id) 
    INNER JOIN es_event_types b 
      ON a.event_type_id = b.event_type_id 
    INNER JOIN es_reservations c 
      ON es.event_schedule_id = c.event_schedule_id 
    INNER JOIN es_reservation_details d 
      ON c.reservation_id = d.reservation_id 
  WHERE a.date >= '2017-01-01' 
    AND a.date < '2019-01-01' 
    AND event_number = 'ASL202 FA17' 
    AND d.guest_status IN ('C', 'U') 
    AND (
      c.parent_reservation_id IS NULL 
      OR c.parent_reservation_id <= 0
    ) 
  GROUP BY es.event_id) AS a;                

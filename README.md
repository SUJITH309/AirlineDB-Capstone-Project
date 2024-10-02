# AirlineDB-Capstone-Project
Developed a database schema to track passenger bookings, flights, and boarding passes. Ensured data integrity by enforcing constraints on ticket-flight relationships and seat assignments. Incorporated mechanisms to handle changes in passenger information and accommodate various flight scenarios.

SQL Capstone Project
Answers for the following SQL Capstone Project Questions
1. Represent the “book_date” column in “yyyy-mmm-dd” format using Bookings table
Expected output: book_ref, book_date (in “yyyy-mmm-dd” format) , total amount
Answer:
SELECT
book_ref,
TO_CHAR(book_date, 'YYYY-MON-DD') as formatted_date,
total_amount
FROM bookings
2. Get the following columns in the exact same sequence.
Expected columns in the output: ticket_no, boarding_no, seat_number, passenger_id,
passenger_name.
Answer:
SELECT
bp.ticket_no,
bp.boarding_no,
bp.seat_no,
t.passenger_id,
t.passenger_name
FROM boarding_passes as bp
JOIN tickets as t
ON bp.ticket_no = t.ticket_no
ORDER BY boarding_no
3. Write a query to find the seat number which is least allocated among all the seats?
Answer:
SELECT
seat_no
FROM (
SELECT
seat_no,
COUNT(*) as seat_count
FROM boarding_passes
SQL Capstone Project
GROUP BY seat_no
ORDER BY seat_count
LIMIT 1
) as seat_no
4. In the database, identify the month wise highest paying passenger name and
passenger id.
Expected output: Month_name(“mmm-yy” format), passenger_id, passenger_name and total
amount
Answer:
WITH MonthlyHighestAmounts AS (
SELECT
TO_CHAR(b.book_date, 'Mon-YY') as Month_Name,
t.passenger_id,
t.passenger_name,
b.total_amount,
ROW_NUMBER () OVER ( PARTITION BY TO_CHAR (
b.book_date, 'Mon-YY') ORDER BY b.total_amount DESC ) as
Row_Num
FROM bookings b
JOIN tickets t
ON b.book_ref = t.book_ref
)
SELECT
Month_Name,
passenger_id,
passenger_name,
total_amount
FROM MonthlyHighestAmounts
WHERE Row_Num = 1
ORDER BY Month_Name
5. In the database, identify the month wise least paying passenger name and
passenger id?
Expected output: Month_name(“mmm-yy” format), passenger_id, passenger_name and total
amount
Answer:
SQL Capstone Project
WITH MonthlyLeastAmounts AS (
SELECT
TO_CHAR(b.book_date, 'Mon-YY') as Month_Name,
t.passenger_id,
t.passenger_name,
b.total_amount,
ROW_NUMBER () OVER ( PARTITION BY TO_CHAR ( b.book_date,
'Mon-YY') ORDER BY b.total_amount ASC ) as Row_Num
FROM bookings b
JOIN tickets t
ON b.book_ref = t.book_ref
)
SELECT
Month_Name,
passenger_id,
passenger_name,
total_amount
FROM MonthlyLeastAmounts
WHERE Row_Num = 1
ORDER BY Month_Name
6. Identify the travel details of non stop journeys or return journeys (having more than
1 flight).
Expected Output: Passenger_id, passenger_name, ticket_number and flight count.
Answer:
SELECT
t.passenger_id,
t.passenger_name,
t.ticket_no,
COUNT(f.flight_id) AS flight_count
FROM tickets t
JOIN ticket_flights f
ON t.ticket_no=f.ticket_no
GROUP BY t.passenger_id, t.passenger_name, t.ticket_no
HAVING COUNT(f.flight_id) = 1 OR COUNT(f.flight_id) > 1
7. How many tickets are there without boarding passes?
Expected Output: just one number is required.
SQL Capstone Project
Answer:
SELECT
COUNT ( * ) AS ticket_count_without_boarding_pass
FROM tickets t
LEFT JOIN boarding_passes b
ON t.ticket_no = b.ticket_no
WHERE b.ticket_no IS NULL
8. Identify details of the longest flight (using flights table)?
Expected Output: Flight number, departure airport, arrival airport, aircraft code and durations.
Answer:
SELECT
flight_no,
departure_airport,
arrival_airport,
aircraft_code,
(scheduled_arrival-scheduled_departure)/60 as duration
FROM flights
ORDER BY duration DESC
LIMIT 1
9. Identify details of all the morning flights (morning means between 6AM to 11 AM,
using flights table)?
Expected output: flight_id, flight_number, scheduled_departure, scheduled_arrival and timings.
Answer:
SELECT
flight_id,
flight_no,
scheduled_departure,
scheduled_arrival,
CAST ( scheduled_departure AS time) as timing
FROM flights
WHERE CAST(scheduled_departure AS time) BETWEEN '06:00:00' AND
'11:00:00'
10. Identify the earliest morning flight available from every airport.
SQL Capstone Project
Expected output: flight_id, flight_number, scheduled_departure, scheduled_arrival, departure
airport and timings.
Answer:
WITH EarlyMorningFlights AS (
SELECT
flight_id,
flight_no,
scheduled_departure,
scheduled_arrival,
departure_airport,
CAST(scheduled_departure AS time) as timing,
ROW_NUMBER() OVER(PARTITION BY departure_airport ORDER BY
scheduled_departure) AS row_num
FROM flights
WHERE CAST(scheduled_departure AS time)BETWEEN '06:00:00' AND
'11:00:00'
)
SELECT
flight_id,
flight_no,
scheduled_departure,
scheduled_arrival,
departure_airport,
timing
FROM EarlyMorningFlights
WHERE row_num = 1
11. Questions: Find list of airport codes in Europe/Moscow timezone
Expected Output: Airport_code.
Answer:
SELECT
DISTINCT airport_code
FROM airports
WHERE timezone = 'Europe/Moscow'
12. Write a query to get the count of seats in various fare condition for every aircraft code?
Expected Outputs: Aircraft_code, fare_conditions ,seat count
SQL Capstone Project
Answer:
SELECT
aircraft_code,
fare_conditions,
COUNT(*) AS seat_count
FROM seats
GROUP BY aircraft_code, fare_conditions
ORDER BY aircraft_code, fare_conditions
13. How many aircrafts codes have at least one Business class seats?
Expected Output : Count of aircraft codes
Answer:
SELECT
COUNT(DISTINCT aircraft_code) AS count_of_aircrafts
FROM seats
WHERE fare_conditions = 'Business'
14. Find out the name of the airport having maximum number of departure flight
Expected Output : Airport_name
Answer:
SELECT
airport_name
FROM airports
WHERE airport_code=(
SELECT
departure_airport
FROM flights
GROUP BY departure_airport
ORDER BY COUNT(*) DESC
LIMIT 1
)
15. Find out the name of the airport having least number of scheduled departure flights
Expected Output : Airport_name
SQL Capstone Project
Answer:
SELECT
airport_name
FROM airports
WHERE airport_code = (
SELECT
departure_airport
FROM flights
GROUP BY departure_airport
ORDER BY COUNT(*) ASC
LIMIT 1
)
16. How many flights from ‘DME’ airport don’t have actual departure?
Expected Output : Flight Count
Answer:
SELECT
COUNT(*) AS Flight_Count
FROM flights
WHERE departure_airport = 'DME' AND actual_departure IS NULL
17. Identify flight ids having range between 3000 to 6000
Expected Output : Flight_Number , aircraft_code, ranges
Answer:
SELECT
f.flight_no,
f.aircraft_code,
a.range
FROM flights f
JOIN aircrafts a
ON f.aircraft_code=a.aircraft_code
WHERE a.range BETWEEN 3000 AND 6000
GROUP BY f.flight_no, f.aircraft_code, a.range
ORDER BY a.range
SQL Capstone Project
18. Write a query to get the count of flights flying between URS and KUF?
Expected Output : Flight_count
Answer:
SELECT
COUNT(*) AS flight_count
FROM flights
WHERE departure_airport = 'URS' AND arrival_airport = 'KUF'
19. Write a query to get the count of flights flying from either from NOZ or KRR?
Expected Output : Flight count
Answer:
SELECT
COUNT(*) AS Flight_count
FROM flights
WHERE departure_airport = 'NOZ' OR departure_airport = 'KRR'
20. Write a query to get the count of flights flying from KZN,DME,NBC,NJC,GDX,SGC,VKO,ROV
Expected Output : Departure airport ,count of flights flying from these airports.
Answer:
SELECT
departure_airport AS departure_airport,
COUNT(*) AS Flight_count
FROM flights
WHERE departure_airport IN
('KZN','DME','NBC','NJC','GDX','SGC','VKO','ROV')
GROUP BY departure_airport
ORDER BY Flight_count
21. Write a query to extract flight details having range between 3000 and 6000 and flying from
DME
Expected Output :Flight_no,aircraft_code,range,departure_airport
Answer:
SELECT
f.flight_no,
f.aircraft_code,
SQL Capstone Project
a.range,
f.departure_airport
FROM flights AS f
JOIN aircrafts AS a
ON f.aircraft_code = a.aircraft_code
WHERE a.range BETWEEN 3000 AND 6000 AND departure_airport
='DME'
GROUP BY 1,2,3,4
ORDER BY a.range
22. Find the list of flight ids which are using aircrafts from “Airbus” company and got cancelled
or delayed
Expected Output : Flight_id,aircraft_model
Answer:
SELECT
F.flight_id,
A.model
FROM flights F
JOIN aircrafts A
ON F.aircraft_code = A.aircraft_code
WHERE A.model LIKE '%Airbus%' AND (F.status = 'Cancelled' OR
F.status = 'Delayed')
23. Find the list of flight ids which are using aircrafts from “Boeing” company and got cancelled
or delayed
Expected Output : Flight_id,aircraft_model
Answer:
SELECT
F.flight_id,
A.model
FROM flights F
JOIN aircrafts A
ON F.aircraft_code = A.aircraft_code
WHERE A.model LIKE '%Boeing%' AND (F.status = 'Cancelled' OR
F.status = 'Delayed')
SQL Capstone Project
24. Which airport(name) has most cancelled flights (arriving)?
Expected Output : Airport_name
Answer:
SELECT
a.airport_name
FROM airports AS a
JOIN flights AS f
ON a.airport_code=f.arrival_airport
WHERE f.status='Cancelled'
GROUP BY a.airport_name
ORDER BY COUNT(*) DESC
LIMIT 1
25. Identify flight ids which are using “Airbus aircrafts”
Expected Output : Flight_id,aircraft_model
Answer:
SELECT
f.flight_id,
a.model
FROM flights AS f
JOIN aircrafts AS a
ON f.aircraft_code=a.aircraft_code
WHERE a.model LIKE '%Airbus%'
26. Identify date-wise last flight id flying from every airport?
Expected Output: Flight_id,flight_number,schedule_departure,departure_airport
Answer:
WITH Last_Flights AS (
SELECT
f.flight_id,
f.flight_no,
f.scheduled_departure,
f.departure_airport,
MAX(scheduled_departure) OVER(PARTITION BY
departure_airport,
DATE(scheduled_departure)) AS max_scheduled_departure
FROM flights AS f
SQL Capstone Project
)
SELECT
flight_id,
flight_no,
scheduled_departure,
departure_airport
FROM Last_Flights
WHERE scheduled_departure=max_scheduled_departure
ORDER BY flight_no
27. Identify list of customers who will get the refund due to cancellation of the flights and how
much amount they will get?
Expected Output : Passenger_name,total_refund.
Answer:
SELECT
T.passenger_name,
SUM(TF.amount) AS refund_amount
FROM TICKETS T
JOIN TICKET_FLIGHTS TF
ON T.ticket_no=TF.ticket_no
JOIN FLIGHTS F
ON TF.flight_id=F.flight_id
WHERE
F.status = 'Cancelled'
GROUP BY 1
28. Identify date wise first cancelled flight id flying for every airport?
Expected Output : Flight_id,flight_number,schedule_departure,departure_airport
Answer:
SELECT
flight_id,
flight_no as flight_number,
CAST(scheduled_departure AS DATE) AS scheduled_departure,
departure_airport
FROM
flights
SQL Capstone Project
WHERE
status = 'Cancelled'
GROUP BY 2,1
ORDER BY 1 asc, 2 asc
29. Identify list of Airbus flight ids which got cancelled.
Expected Output : Flight_id
Answer:
SELECT
F.flight_id
FROM
flights F
JOIN aircrafts A ON F.aircraft_code = A.aircraft_code
WHERE
A.model LIKE '%Airbus%' AND F.status = 'Cancelled'
30. Identify list of flight ids having highest range.
Expected Output : Flight_no, range
Answer:
SELECT
f.flight_no,
max(a.range) as range
FROM flights f
JOIN aircrafts a
ON f.aircraft_code=a.aircraft_code
GROUP BY flight_no

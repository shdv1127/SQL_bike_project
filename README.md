/*SQL_bike_project
This was my Google data analytics certificate program capstone project. The goal was to analyze bikeshare data in Chicago from the previous year and come up with ideas to increase annual membership, specifically by targeting current casual riders for membership. My analysis focused on how members and casual riders use the bikeshare service differently. The data was already cleaned and ready to use and it was provided as part of the capstone course.*/

/*First, I wanted to get an idea of the habits of the different rider types. To that end, I wanted to start by figuring out what the average bike ride length was across all months, and then see if there was variability across months and variability between members and casual riders. The shortest ride lengths corresponded to the coldest months (January and February). Overall the average ride lenth was 15.5 minutes, with members averaging shorter rides (11.8 minutes) and casual riders averaging longer rides (22.1 minutes).*/ 

/*Average ride length for member versus casual riders*/
SELECT member_casual, AVG(TIMESTAMPDIFF(MINUTE, started_at, ended_at)) AS avg_ride_length_minutes
FROM rides
group by member_casual;  

/*Average ride length per month overall*/
SELECT MONTH(started_at) as ride_month, 
	AVG(TIMESTAMPDIFF(MINUTE, started_at, ended_at)) AS avg_ride_length_minutes
FROM rides
GROUP BY ride_month
ORDER BY ride_month;

/*Next, I wanted to know if there was a difference in rider type across months*/ 
SELECT MONTH(started_at) as ride_month,
	SUM(CASE WHEN member_casual = "member" THEN 1 ELSE 0 END) AS member_rides,
    SUM(CASE WHEN member_casual = "casual" THEN 1 ELSE 0 END) AS casual_rides
FROM rides
GROUP BY ride_month
ORDER BY ride_month; #member versus casual rides per month

/*Then, I wanted to see which bikeshare start stations were most popular with which rider type: I limited to the top 5 most popular stations for casual riders. The goal being to test out any new marketing initiative at these five stations before expanding further.*/

SELECT start_station_name, member_casual, COUNT(start_station_name)
FROM rides
WHERE start_station_name IS NOT NULL and member_casual = "casual"
GROUP BY start_station_name, member_casual
ORDER BY COUNT(start_station_name) desc limit 5;

/*At this point, I dove deeper into the data to see what trends emerged on different days of the week. For example, I wanted to know if there were more casual riders during weekdays or weekends, and also what times of day (morning, afternoon, evening) were the most popular.*/ 

SELECT DAYOFWEEK(started_at) as ride_day_of_week,
	SUM(CASE WHEN member_casual = "member" THEN 1 ELSE 0 END) AS member_rides,
    SUM(CASE WHEN member_casual = "casual" THEN 1 ELSE 0 END) AS casual_rides
FROM rides
GROUP BY ride_day_of_week
ORDER BY ride_day_of_week;

SELECT
	CASE
		WHEN HOUR(started_at) BETWEEN 5 AND 11 THEN "Morning"
        WHEN HOUR(started_at) BETWEEN 12 AND 16 THEN "Afternoon"
        WHEN HOUR(started_at) BETWEEN 17 and 20 then "Evening"
        ELSE "Overnight"
        END as time_of_day,
        SUM(CASE WHEN member_casual = "member" then 1 ELSE 0 END) AS member_rides,
        SUM(CASE WHEN member_casual = "casual" then 1 else 0 END) AS casual_rides
FROM rides
GROUP BY time_of_day
ORDER BY 
	FIELD(time_of_day, "Morning", "Afternoon", "Evening", "Overnight");

/*I would have liked to have more specific information about identifying repeat casual riders so these users could be more accurately targeted. Depending on how many times a month they are riding, it could be easy to convince these casual riders that a membership is the more logical and economical option. They could also use the service more often and for shorter trips at their convenience.*/   

/*Based on my analysis, I determined that there are more casual riders Friday-Sunday in the afternoon and evenings and into overnight. The peak months for casual riders are April through October (and especially June-August). My marketing strategy would be to begin advertising at the most popular start and end stations in April when casual ridership begins to increase. The message to casual riders would focus on benefits of membership, such as being able to take more frequent, possibly shorter rides, and also to always have access to reliable transportation at any time of the day (or overnight).*/







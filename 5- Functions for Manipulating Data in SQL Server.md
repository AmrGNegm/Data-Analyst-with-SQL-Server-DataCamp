https://blog.csdn.net/weixin_42871941/article/details/107307563

1. Choosing the appropriate data type
</> Working with different types of data
Select information from the ratings table for the Belgian companies that received a rating higher than 3.5.

SELECT 
	company, 
	company_location, 
	bean_origin, 
	cocoa_percent, 
	rating
FROM ratings
WHERE company_location = 'Belgium'
	AND rating > 3.5;

company	company_location	bean_origin		cocoa_percent	rating
Callebaut					Belgium	Baking	0.7000			3.7500
Neuhaus (Callebaut)			Belgium	Dark	0.7300			4.0000
Pierre Marcolini			Belgium	Cabosse	0.7000			4.0000
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
Query the voters table where birthdate is greater than ‘1990-01-01’ and the total_votes is between 100 and 200.

SELECT 
	first_name,
	last_name,
	birthdate,
	gender,
	email,
	country,
	total_votes
FROM voters
WHERE birthdate > '1990-01-01'
  AND total_votes > 100
  AND total_votes < 200;

first_name	last_name	birthdate	gender	email				country		total_votes
Julie		Becker		1996-04-21	F 		julie22@yahoo.com	Belgium		189
Jessica		Gonzales	1993-04-11	F 		jessica41@yahoo.com	Belgium		171
Willie		She			1991-05-05	M 		willie20@yahoo.com	Switzerland	125
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
</> Storing dates in a database
Add a new column with the correct data type, for storing the last date a person voted (“2018-01-17”).

ALTER TABLE voters
ADD last_vote_date date;
1
2
Add a new column called last_vote_time, to keep track of the most recent time when a person voted (“16:55:00”).

ALTER TABLE voters
ADD last_vote_time time;
1
2
Add a new column,last_login, storing the most recent time a person accessed the application (“2019-02-02 13:44:00”).

ALTER TABLE voters
ADD last_login datetime2;
1
2
</> Types of character strings
To what data category does the nvarchar type belong?

 Exact numerics
 Date and time
 Unicode character strings
 Other data types
</> Implicit conversion between data types

Restrict the query to show only the rows where total_votes is higher than the character string ‘120’.

SELECT 
	first_name,
	last_name,     
	total_votes
FROM voters
WHERE total_votes > '120';

first_name	last_name	total_votes
Carol		Rai			136
Ana			Price		124
Melissa		Richardson	145
...
1
2
3
4
5
6
7
8
9
10
11
12
What kind of implicit conversion is performed by SQL Server to make this query execute successfully?

 The values from the total_votes column are converted to a character type.
 The values from the total_votes column and the character ‘120’ are converted to an intermediary data type, because comparing data works only when the data types are identical.
 The character ‘120’ is converted to a numeric type.
</> Data type precedence
Select information about all the ratings that were higher than 3.

SELECT 
	bean_type,
	rating
FROM ratings
WHERE rating > 3;

bean_type	rating
Unknown		3.2500
Trinitario	3.2500
Unknown		3.2500
...
1
2
3
4
5
6
7
8
9
10
11
Taking into account that the rating column is a decimal, which statement is true about the execution of this query?

 The values from the rating column are converted to int, because int has higher precedence than decimal.
 The integer value is converted to decimal because decimal has higher precedence than int.
 No conversion is performed because decimal and integer numbers can be compared as they are.
</> CASTing data
Write a query that will show a message like the following, for each voter: Carol Rai was born in 1989.

SELECT 
	first_name + ' ' + last_name + ' was born in ' + CAST(YEAR(birthdate) AS nvarchar) + '.' 
FROM voters;

Carol Rai was born in 1989.
Ana Price was born in 1987.
Melissa Richardson was born in 1989.
...
1
2
3
4
5
6
7
8
Divide the total votes by 5.5. Transform the result to an integer.

SELECT 
	CAST(total_votes/5.5 AS integer) AS DividedVotes
FROM voters;

DividedVotes
24
22
26
...
1
2
3
4
5
6
7
8
9
Select the voters whose total number of votes starts with 5.

SELECT 
	first_name,
	last_name,
	total_votes
FROM voters
WHERE CAST(total_votes AS varchar) LIKE '5%';

first_name	last_name	total_votes
Angela		Griffin		52
Kaylee		Cook		58
Gabrielle	Cox			55
...
1
2
3
4
5
6
7
8
9
10
11
12
</> CONVERTing data
Retrieve the birth date from voters, in this format: Mon dd,yyyy.

SELECT 
	email,
    CONVERT(varchar, birthdate, 107) AS birthdate
FROM voters;

email					birthdate
carol8@yahoo.com		Jan 15, 1989
ana0@gmail.com			Feb 17, 1987
melissa31@yahoo.com		Apr 25, 1989
...
1
2
3
4
5
6
7
8
9
10
Select the company, bean origin and the rating from the ratings table. The rating should be converted to a whole number.

SELECT 
	company,
    bean_origin,
    CONVERT(int, rating) AS rating
FROM ratings;

company		bean_origin		rating
Amedei		Toscano Black	2
Amedei		Toscano Black	2
Bonnat		Trinite			2
...
1
2
3
4
5
6
7
8
9
10
11
Select the company, bean origin and the rating from the ratings table where the whole part of the rating equals 3.

SELECT 
	company,
    bean_origin,
    rating
FROM ratings
-- Convert the rating to an integer before comparison
WHERE CONVERT(int, rating) = 3;

company		bean_origin				rating
Bonnat		Maracaibo- El Rosario	3.0000
Bonnat		Madagascar				3.0000
Bonnat		One Hundred				3.0000
...
1
2
3
4
5
6
7
8
9
10
11
12
13
</> Working with the correct data types
Restrict the query to retrieve only the female voters who voted more than 20 times.

SELECT 
	first_name,
	last_name,
	gender,
	country
FROM voters
WHERE country = 'Belgium'
	AND gender = 'F'
    AND total_votes > 20;

first_name	last_name	gender	country
Angela		Griffin		F 		Belgium
Julie		Becker		F 		Belgium
Jessica		Gonzales	F 		Belgium
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
Now that we have the data set prepared, let’s make it more user-friendly. Perform an explicit conversion from datetime to varchar(10), to show the dates as yy/mm/dd.

SELECT 
	first_name,
    last_name,
	CONVERT(varchar(10), birthdate, 11) AS birthdate,
    gender,
    country
FROM voters
WHERE country = 'Belgium' 
	AND gender = 'F'
    AND total_votes > 20;

first_name	last_name	birthdate	gender	country
Angela		Griffin		93/03/08	F 		Belgium
Julie		Becker		96/04/21	F 		Belgium
Jessica		Gonzales	93/04/11	F 		Belgium
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
Let’s now create a comments column that will show the number of votes performed by each person, in the following form: “Voted “x” times.”

SELECT
	first_name,
    last_name,
	CONVERT(varchar(10), birthdate, 11) AS birthdate,
    gender,
    country,
    'Voted ' + CAST(total_votes AS varchar) + ' times.' AS comments
FROM voters
WHERE country = 'Belgium'
	AND gender = 'F'
    AND total_votes > 20;

first_name	last_name	birthdate	gender	country	comments
Angela		Griffin		93/03/08	F 		Belgium	Voted 52 times.
Julie		Becker		96/04/21	F 		Belgium	Voted 189 times.
Jessica		Gonzales	93/04/11	F 		Belgium	Voted 171 times.
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
2. Manipulating time
</> Get the know the system date and time functions
Use the most common date function for retrieving the current date.

SELECT 
	SYSDATETIME() AS CurrentDate;

CurrentDate
2020-07-14 11:43:05.434873
1
2
3
4
5
Select the current date in UTC time (Universal Time Coordinate) using two different functions.

SELECT 
	SYSUTCDATETIME() AS UTC_HighPrecision,
	SYSDATETIMEOFFSET() AS UTC_LowPrecision;

UTC_HighPrecision			UTC_LowPrecision
2020-07-14 11:44:07.147463	2020-07-14 11:44:07.1474639 +00:00
1
2
3
4
5
6
Select the local system’s date, including the timezone information.

SELECT 
	GETDATE() AS Timezone;

Timezone
2020-07-14 11:44:59.400000
1
2
3
4
5
</> Selecting parts of the system’s date and time
Use two functions to query the system’s local date, without timezone information. Show the dates in different formats.

SELECT 
	CONVERT(VARCHAR(24), SYSUTCDATETIME(), 107) AS HighPrecision,
	CONVERT(VARCHAR(24), SYSDATETIMEOFFSET(), 102) AS LowPrecision;

HighPrecision	LowPrecision
Jul 14, 2020	2020.07.14
1
2
3
4
5
6
Use two functions to retrieve the current time, in Universal Time Coordinate.

SELECT 
	CAST(SYSUTCDATETIME() AS time) AS HighPrecision,
	CAST(SYSDATETIMEOFFSET() AS time) AS LowPrecision;

HighPrecision	LowPrecision
11:48:01.953926	11:48:01.953926
1
2
3
4
5
6
</> Extracting parts from a date
Extract the year, month and day of the first vote.

SELECT 
	first_name,
	last_name,
	YEAR(first_vote_date)  AS first_vote_year,
	MONTH(first_vote_date) AS first_vote_month,
	DAY(first_vote_date) AS first_vote_day
FROM voters;

first_name	last_name	first_vote_year	first_vote_month	first_vote_day
Carol		Rai			2015			3					9
Ana			Price		2015			1					17
Melissa		Richardson	2015			4					9
...
1
2
3
4
5
6
7
8
9
10
11
12
13
Restrict the query to show only the voters who started to vote after 2015.

SELECT 
	first_name,
	last_name,
	YEAR(first_vote_date)  AS first_vote_year,
	MONTH(first_vote_date) AS first_vote_month,
	DAY(first_vote_date)   AS first_vote_day
FROM voters
WHERE YEAR(first_vote_date) > 2015;

first_name	last_name	first_vote_year	first_vote_month	first_vote_day
Sebastian	Rogers		2016			1					6
Barbara		Zhao		2016			1					21
Haley		Sanchez		2016			1					5
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
Restrict the query to show only the voters did not vote on the first day of the month.

SELECT 
	first_name,
	last_name,
	YEAR(first_vote_date)  AS first_vote_year,
	MONTH(first_vote_date) AS first_vote_month,
	DAY(first_vote_date)   AS first_vote_day
FROM voters
WHERE YEAR(first_vote_date) > 2015
  AND DAY(first_vote_date) <> 1;

first_name	last_name	first_vote_year	first_vote_month	first_vote_day
Sebastian	Rogers		2016			1					6
Barbara		Zhao		2016			1					21
Haley		Sanchez		2016			1					5
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
</> Generating descriptive date parts
Select information from the voters table, including the name of the month when they first voted.

SELECT 
	first_name,
	last_name,
	first_vote_date,
	DATENAME(month, first_vote_date) AS first_vote_month
FROM voters;

first_name	last_name	first_vote_date	first_vote_month
Carol		Rai			2015-03-09		March
Ana			Price		2015-01-17		January
Melissa		Richardson	2015-04-09		April
...
1
2
3
4
5
6
7
8
9
10
11
12
Select information from the voters table, including the day of the year when they first voted.

SELECT 
	first_name,
	last_name,
	first_vote_date,
	DATENAME(dayofyear, first_vote_date) AS first_vote_dayofyear
FROM voters;

first_name	last_name	first_vote_date	first_vote_dayofyear
Carol		Rai			2015-03-09		68
Ana			Price		2015-01-17		17
Melissa		Richardson	2015-04-09		99
...
1
2
3
4
5
6
7
8
9
10
11
12
Select information from the voters table, including the day of the week when they first voted.

SELECT 
	first_name,
	last_name,
	first_vote_date,
	DATENAME(weekday, first_vote_date) AS first_vote_dayofweek
FROM voters;

first_name	last_name	first_vote_date	first_vote_dayofweek
Carol		Rai			2015-03-09		Monday
Ana			Price		2015-01-17		Saturday
Melissa		Richardson	2015-04-09		Thursday
...
1
2
3
4
5
6
7
8
9
10
11
12
</> Presenting parts of a date
Extract the month number of the first vote.

Extract the month name of the first vote.

Extract the weekday number of the first vote.

Extract the weekday name of the first vote.

SELECT 
	first_name,
	last_name,
	DATEPART(MONTH,first_vote_date) AS first_vote_month1,
    DATENAME(MONTH,first_vote_date) AS first_vote_month2,
	DATEPART(WEEKDAY,first_vote_date) AS first_vote_weekday1,
	DATENAME(WEEKDAY,first_vote_date) AS first_vote_weekday2
FROM voters;

first_name	last_name	first_vote_month1	first_vote_month2	first_vote_weekday1	first_vote_weekday2
Carol		Rai			3					March				2					Monday
Ana			Price		1					January				7					Saturday
Melissa		Richardson	4					April				5					Thursday
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
</> Creating a date from parts
Select the year of the first vote.

Select the month of the first vote date.

Create a date as the start of the month of the first vote.

SELECT 
	first_name,
	last_name,
   	YEAR(first_vote_date) AS first_vote_year, 
	MONTH(first_vote_date) AS first_vote_month,
	DATEFROMPARTS(YEAR(first_vote_date), MONTH(first_vote_date), 1) AS first_vote_starting_month
FROM voters;

first_name	last_name	first_vote_year	first_vote_month	first_vote_starting_month
Carol		Rai			2015			3					2015-03-01
Ana			Price		2015			1					2015-01-01
Melissa		Richardson	2015			4					2015-04-01
...
1
2
3
4
5
6
7
8
9
10
11
12
13
</> Arithmetic operations with dates
What is the result returned by the DATEDIFF() function?

 0 years
 238 years
 261 years
 23 years
DECLARE @date1 datetime = '2018-12-01';
DECLARE @date2 datetime = '2030-03-03';

select
@date2 - @date1 as sub1,
@date1 + @date2 as add1,
DATEDIFF(year, @date2 - @date1, @date1 + @date2)

sub1				add1	
1911-04-04 00:00:00	2149-01-31 00:00:00	238
1
2
3
4
5
6
7
8
9
10
</> Modifying the value of a date
Retrieve the date when each voter had their 18th birthday.

SELECT 
	first_name,
	birthdate,
	DATEADD(YEAR, 18, birthdate) AS eighteenth_birthday
  FROM voters;

first_name	birthdate	eighteenth_birthday
Carol		1989-01-15	2007-01-15
Ana			1987-02-17	2005-02-17
Melissa		1989-04-25	2007-04-25
...
1
2
3
4
5
6
7
8
9
10
11
Add five days to the first_vote_date, to calculate the date when the vote was processed.

SELECT 
	first_name,
	first_vote_date,
	DATEADD(DAY, 5, first_vote_date) AS processing_vote_date
  FROM voters;

first_name	first_vote_date	processing_vote_date
Carol		2015-03-09		2015-03-14
Ana			2015-01-17		2015-01-22
Melissa		2015-04-09		2015-04-14
...
1
2
3
4
5
6
7
8
9
10
11
Calculate what day it was 476 days ago.

SELECT
	getdate() - 476 AS date_476days_ago;

date_476days_ago
2019-03-27 11:25:59.870000
1
2
3
4
5
</> Calculating the difference between dates
Calculate the number of years since a participant celebrated their 18th birthday until the first time they voted.

SELECT
	first_name,
	birthdate,
	first_vote_date,
    -- Select the diff between the 18th birthday and first vote
	DATEDIFF(year, DATEADD(YEAR, 18, birthdate), first_vote_date) AS adult_years_until_vote
FROM voters;

first_name	birthdate	first_vote_date	adult_years_until_vote
Carol		1989-01-15	2015-03-09		8
Ana			1987-02-17	2015-01-17		10
Melissa		1989-04-25	2015-04-09		8
...
1
2
3
4
5
6
7
8
9
10
11
12
13
How many weeks have passed since the beginning of 2019 until now?

SELECT 
	DATEDIFF(week, '2019-01-01', getdate()) AS weeks_passed;

weeks_passed
80
1
2
3
4
5
</> Changing the date format
Set the correct date format so that the variable @date1 is interpreted as a valid date.

DECLARE @date1 NVARCHAR(20) = '2018-30-12';
SET DATEFORMAT ydm;
SELECT ISDATE(@date1) AS result;

result
1
1
2
3
4
5
6
Set the correct date format so that the variable @date1 is interpreted as a valid date.

DECLARE @date1 NVARCHAR(20) = '15/2019/4';
SET DATEFORMAT dym;
SELECT ISDATE(@date1) AS result;

result
1
1
2
3
4
5
6
Set the correct date format so that the variable @date1 is interpreted as a valid date.

DECLARE @date1 NVARCHAR(20) = '10.13.2019';
SET DATEFORMAT mdy;
SELECT ISDATE(@date1) AS result;

result
1
1
2
3
4
5
6
Set the correct date format so that the variable @date1 is interpreted as a valid date.

DECLARE @date1 NVARCHAR(20) = '18.4.2019';
SET DATEFORMAT dmy;
SELECT ISDATE(@date1) AS result;

result
1
1
2
3
4
5
6
</> Changing the default language
Language	Date format
English	mdy
Croatian	ymd
Dutch	dmy
Change the language, so that ‘30.03.2019’ is considered a valid date. Select the name of the month.

DECLARE @date1 NVARCHAR(20) = '30.03.2019';
SET LANGUAGE Dutch;
SELECT
	@date1 AS initial_date,
	ISDATE(@date1) AS is_valid,
	DATENAME(MONTH, @date1) AS month_name;

initial_date	is_valid	month_name
30.03.2019		1			maart
1
2
3
4
5
6
7
8
9
Change the language, so that ‘32/12/13’ is interpreted as a valid date. Select the name of the month. Select the year.

DECLARE @date1 NVARCHAR(20) = '32/12/13';
SET LANGUAGE Croatian;
SELECT
	@date1 AS initial_date,
	ISDATE(@date1) AS is_valid,
	DATENAME(MONTH, @date1) AS month_name,
	YEAR(@date1) AS year_name;

initial_date	is_valid	month_name	year_name
32/12/13		1			prosinac	2032
1
2
3
4
5
6
7
8
9
10
Change the language, so that ‘12/18/55’ is interpreted as a valid date. Select the day of week. Select the year.

DECLARE @date1 NVARCHAR(20) = '12/18/55';
SET LANGUAGE English;
SELECT
	@date1 AS initial_date,
	ISDATE(@date1) AS is_valid,
	DATENAME(weekday, @date1) AS week_day,
	YEAR(@date1) AS year_name;

initial_date	is_valid	week_day	year_name
12/18/55		1			Sunday		1955
1
2
3
4
5
6
7
8
9
10
</> Correctly applying different date functions
Extract the weekday from the first_vote_date.

SELECT
	first_name,
    last_name,
    birthdate,
	first_vote_date,
	DATENAME(weekday, first_vote_date) AS first_vote_weekday
FROM voters;

first_name	last_name	birthdate	first_vote_date	first_vote_weekday
Carol		Rai			1989-01-15	2015-03-09		Monday
Ana			Price		1987-02-17	2015-01-17		Saturday
Melissa		Richardson	1989-04-25	2015-04-09		Thursday
...
1
2
3
4
5
6
7
8
9
10
11
12
13
Find out the year when each person voted for the first time.

SELECT
	first_name,
    last_name,
    birthdate,
	first_vote_date,
	DATENAME(weekday, first_vote_date) AS first_vote_weekday,
	YEAR(first_vote_date) AS first_vote_year	
FROM voters;

first_name	last_name	birthdate	first_vote_date	first_vote_weekday	first_vote_year
Carol		Rai			1989-01-15	2015-03-09		Monday				2015
Ana			Price		1987-02-17	2015-01-17		Saturday			2015
Melissa		Richardson	1989-04-25	2015-04-09		Thursday			2015
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
Calculate the age of each participant when they first joined the voting contest.

SELECT
	first_name,
    last_name,
    birthdate,
	first_vote_date,
	DATENAME(weekday, first_vote_date) AS first_vote_weekday,
	YEAR(first_vote_date) AS first_vote_year,
	DATEDIFF(year, birthdate, first_vote_date) AS age_at_first_vote	
FROM voters;

first_name	last_name	birthdate	first_vote_date	first_vote_weekday	first_vote_year	age_at_first_vote
Carol		Rai			1989-01-15	2015-03-09		Monday				2015			26
Ana			Price		1987-02-17	2015-01-17		Saturday			2015			28
Melissa		Richardson	1989-04-25	2015-04-09		Thursday			2015			26
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
Calculate the current age of each participant. Remember that you can use functions as parameters for other functions.

SELECT
	first_name,
    last_name,
    birthdate,
	first_vote_date,
	DATENAME(weekday, first_vote_date) AS first_vote_weekday,
	YEAR(first_vote_date) AS first_vote_year,
	DATEDIFF(YEAR, birthdate, first_vote_date) AS age_at_first_vote,	
	DATEDIFF(YEAR, birthdate, getdate()) AS current_age
FROM voters;

first_name	last_name	birthdate	first_vote_date	first_vote_weekday	first_vote_year	age_at_first_vote	current_age
Carol		Rai			1989-01-15	2015-03-09		Monday				2015			26					31
Ana			Price		1987-02-17	2015-01-17		Saturday			2015			28					33
Melissa		Richardson	1989-04-25	2015-04-09		Thursday			2015			26					31
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
3. Working With Strings
</> Calculating the length of a string
Calculate the length of each broad_bean_origin.

Order the results from the longest to shortest.

SELECT TOP 10 
	company, 
	broad_bean_origin,
	LEN(broad_bean_origin) AS length
FROM ratings
ORDER BY LEN(broad_bean_origin) DESC;

company	broad_bean_origin				length
Raaka	Peru(SMartin-Pangoa-nacional)	30
Soma	Guat.- D.R.- Peru- Mad.- PNG	29
Bonnat	Venez-Africa-Brasil-Peru-Mex	29
...
1
2
3
4
5
6
7
8
9
10
11
12
</> Looking for a string within a string
Restrict the query to select only the voters whose first name contains the expression “dan”.

SELECT 
	first_name,
	last_name,
	email 
FROM voters
WHERE CHARINDEX('dan', first_name) > 0;

first_name	last_name	email
Daniel		Martin		daniel17@yahoo.com
Daniel		Martinez	daniel15@yahoo.com
Dana		Vazquez		dana7@yahoo.com
...
1
2
3
4
5
6
7
8
9
10
11
12
Restrict the query to select the voters with “dan” in the first_name and “z” in the last_name.

SELECT 
	first_name,
	last_name,
	email 
FROM voters
WHERE CHARINDEX('dan', first_name) > 0 
	AND CHARINDEX('z', last_name) > 0;

first_name	last_name	email
Daniel		Martinez	daniel15@yahoo.com
Dana		Vazquez		dana7@yahoo.com
Danny		Ruiz		danny2@yahoo.com
1
2
3
4
5
6
7
8
9
10
11
12
Restrict the query to select the voters with “dan” in the first_name and DO NOT have the letter “z” in the last_name.

SELECT 
	first_name,
	last_name,
	email 
FROM voters
WHERE CHARINDEX('dan', first_name) > 0 
	AND CHARINDEX('z', last_name) = 0;

first_name	last_name	email
Daniel		Martin		daniel17@yahoo.com
Danny		Martin		danny0@yahoo.com
1
2
3
4
5
6
7
8
9
10
11
</> Looking for a pattern within a string
pattern	match
%	any string of zero or more characters
_	any single character
[]	any single character within the range specified in brackets
Write a query to select the voters whose first name contains the letters “rr”.

SELECT 
	first_name,
	last_name,
	email 
FROM voters
WHERE PATINDEX('%rr%', first_name) > 0;

first_name	last_name	email
Darryl		Liu			darryl4@yahoo.com
Terrence	Andersen	terrence13@yahoo.com
Darrell		Shan		darrell18@yahoo.com
1
2
3
4
5
6
7
8
9
10
11
Write a query to select the voters whose first name starts with “C” and has “r” as the third letter.

SELECT 
	first_name,
	last_name,
	email 
FROM voters
WHERE PATINDEX('C_r%', first_name) > 0;

first_name	last_name	email
Carol		Rai			carol8@yahoo.com
Caroline	Griffin		caroline22@yahoo.com
Christine	Chander		christine11@yahoo.com
...
1
2
3
4
5
6
7
8
9
10
11
12
Select the voters whose first name contains an “a” followed by other letters, then a “w”, followed by other letters.

SELECT 
	first_name,
	last_name,
	email 
FROM voters
WHERE PATINDEX('%a%w%', first_name) > 0;

first_name	last_name	email
Mathew		Sanz		mathew16@yahoo.com
Dawn		Pal			dawn36@yahoo.com
Andrew		Clark		andrew26@gmail.com
1
2
3
4
5
6
7
8
9
10
11
Write a query to select the voters whose first name contains one of these letters: “x”, “w” or “q”.

SELECT 
	first_name,
	last_name,
	email 
FROM voters
WHERE PATINDEX('%[xwq]%', first_name) > 0;

first_name	last_name	email
Mathew		Sanz		mathew16@yahoo.com
Wesley		Yang		wesley5@yahoo.com
Alexandra	Hill		alexandra63@gmail.com
...
1
2
3
4
5
6
7
8
9
10
11
12
</> Changing to lowercase and uppercase
Select information from the ratings table, excluding the unknown broad_bean_origins.

Convert the broad_bean_origins to lowercase when comparing it to the ‘%unknown%’ expression.

SELECT 
	company,
	bean_type,
	broad_bean_origin,
	'The company ' +  company + ' uses beans of type "' + bean_type + '", originating from ' + broad_bean_origin + '.'
FROM ratings
WHERE
	LOWER(broad_bean_origin) NOT LIKE '%unknown%';

company	bean_type	broad_bean_origin	
Bonnat	Trinitario	Carribean			The company Bonnat uses beans of type "Trinitario", originating from Carribean.
Bonnat	Unknown		Venezuela			The company Bonnat uses beans of type "Unknown", originating from Venezuela.
Bonnat	Criollo		Venezuela			The company Bonnat uses beans of type "Criollo", originating from Venezuela.
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
Restrict the query to make sure that the bean_type is not unknown.

Convert the bean_type to lowercase and compare it with an expression that contains the ‘%unknown%’ word.

SELECT 
	company,
	bean_type,
	broad_bean_origin,
	'The company ' +  company + ' uses beans of type "' + bean_type + '", originating from ' + broad_bean_origin + '.'
FROM ratings
WHERE 
	LOWER(broad_bean_origin) NOT LIKE '%unknown%'
    AND LOWER(bean_type) NOT LIKE '%unknown%';

company	bean_type	broad_bean_origin	
Bonnat	Trinitario	Carribean			The company Bonnat uses beans of type "Trinitario", originating from Carribean.
Bonnat	Criollo		Venezuela			The company Bonnat uses beans of type "Criollo", originating from Venezuela.
Bonnat	Trinitario	Madagascar			The company Bonnat uses beans of type "Trinitario", originating from Madagascar.
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
Format the message so that company and broad_bean_origin are uppercase.

SELECT 
	company,
	bean_type,
	broad_bean_origin,
	'The company ' +  UPPER(company) + ' uses beans of type "' + bean_type + '", originating from ' + UPPER(broad_bean_origin) + '.'
FROM ratings
WHERE 
	LOWER(broad_bean_origin) NOT LIKE '%unknown%'
    AND LOWER(bean_type) NOT LIKE '%unknown%';

company	bean_type	broad_bean_origin	
Bonnat	Trinitario	Carribean			The company BONNAT uses beans of type "Trinitario", originating from CARRIBEAN.
Bonnat	Criollo		Venezuela			The company BONNAT uses beans of type "Criollo", originating from VENEZUELA.
Bonnat	Trinitario	Madagascar			The company BONNAT uses beans of type "Trinitario", originating from MADAGASCAR.
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
</> Using the beginning or end of a string
Select information from the voters table, including a new column called part1, containing only the first 3 letters from the first name.

SELECT 
	first_name,
	last_name,
	country,
	LEFT(first_name, 3) AS part1
FROM voters;

first_name	last_name	country		part1
Carol		Rai			Denmark		Car
Ana			Price		France		Ana
Melissa		Richardson	Denmark		Mel
...
1
2
3
4
5
6
7
8
9
10
11
12
Add a new column to the previous query, containing the last 3 letters from the last name.

SELECT 
	first_name,
	last_name,
	country,
	LEFT(first_name, 3) AS part1,
    RIGHT(last_name, 3) AS part2
FROM voters;

first_name	last_name	country		part1	part2
Carol		Rai			Denmark		Car		Rai
Ana			Price		France		Ana		ice
Melissa		Richardson	Denmark		Mel		son
...
1
2
3
4
5
6
7
8
9
10
11
12
13
Add another column to the previous query, containing the last 2 digits from the birth date.

SELECT 
	first_name,
	last_name,
	country,
	LEFT(first_name, 3) AS part1,
    RIGHT(last_name, 3) AS part2,
    RIGHT(birthdate, 2) AS part3
FROM voters;

first_name	last_name	country		part1	part2	part3
Carol		Rai			Denmark		Car		Rai		15
Ana			Price		France		Ana		ice		17
Melissa		Richardson	Denmark		Mel		son		25
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
Create an alias for each voter with the following parts: the first 3 letters from the first name concatenated with the last 3 letters from the last name, followed by the _ character and the last 2 digits from the birth date.

SELECT 
	first_name,
	last_name,
	country,
	LEFT(first_name, 3) AS part1,
    RIGHT(last_name, 3) AS part2,
    RIGHT(birthdate, 2) AS part3,
    LEFT(first_name, 3) + RIGHT(last_name, 3) + '_' + RIGHT(birthdate, 2) 
FROM voters;

first_name	last_name	country		part1	part2	part3	
Carol		Rai			Denmark		Car		Rai		15		CarRai_15
Ana			Price		France		Ana		ice		17		Anaice_17
Melissa		Richardson	Denmark		Mel		son		25		Melson_25
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
</> Extracting a substring
Select 5 characters from the email address, starting with position 3.

SELECT 
	email,
	SUBSTRING(email, 3, 5) AS some_letters
FROM voters;

email				some_letters
carol8@yahoo.com	rol8@
ana0@gmail.com		a0@gm
melissa31@yahoo.com	lissa
...
1
2
3
4
5
6
7
8
9
10
Extract the fruit names from the following sentence: “Apples are neither oranges nor potatoes”.

DECLARE @sentence NVARCHAR(200) = 'Apples are neither oranges nor potatoes.'
SELECT
	SUBSTRING(@sentence, 1, 6) AS fruit1,
	SUBSTRING(@sentence, 20, 7) AS fruit2;

fruit1	fruit2
Apples	oranges
1
2
3
4
5
6
7
</> Replacing parts of a string
Add a new column in the query in which you replace the “yahoo.com” in all email addresses with “live.com”.

SELECT 
	first_name,
	last_name,
	email,
	REPLACE(email, 'yahoo.com', 'live.com') AS new_email
FROM voters;

first_name	last_name	email				new_email
Carol		Rai			carol8@yahoo.com	carol8@live.com
Ana			Price		ana0@gmail.com		ana0@gmail.com
Melissa		Richardson	melissa31@yahoo.com	melissa31@live.com
...
1
2
3
4
5
6
7
8
9
10
11
12
Replace the character “&” from the company name with “and”.

SELECT 
	company AS initial_name,
	REPLACE(company, '&', 'and') AS new_name 
FROM ratings
WHERE CHARINDEX('&', company) > 0;

initial_name						new_name
Green & Black's (ICAM)				Green and Black's (ICAM)
Lindt & Sprungli					Lindt and Sprungli
Debauve & Gallais (Michel Cluizel)	Debauve and Gallais (Michel Cluizel)
...
1
2
3
4
5
6
7
8
9
10
11
Remove the string “(Valrhona)” from the company name “La Maison du Chocolat (Valrhona)”.

SELECT 
	company AS old_company,
	REPLACE(company, '(Valrhona)', '') AS new_company,
	bean_type,
	broad_bean_origin
FROM ratings
WHERE company = 'La Maison du Chocolat (Valrhona)';

old_company							new_company				bean_type			broad_bean_origin
La Maison du Chocolat (Valrhona)	La Maison du Chocolat 	Blend				Ven.- Indonesia- Ecuad.
La Maison du Chocolat (Valrhona)	La Maison du Chocolat 	Forastero			Ghana
La Maison du Chocolat (Valrhona)	La Maison du Chocolat 	Criollo (Porcelana)	Venezuela
...
1
2
3
4
5
6
7
8
9
10
11
12
13
</> Concatenating data
Create a message similar to this one: “Chocolate with beans from Belize has a cocoa percentage of 0.6400” for each result of the query.

Use the + operator to concatenate data and the ’ ’ character as a separator.

DECLARE @string1 NVARCHAR(100) = 'Chocolate with beans from';
DECLARE @string2 NVARCHAR(100) = 'has a cocoa percentage of';

SELECT 
	bean_type,
	bean_origin,
	cocoa_percent,
	@string1 + ' ' + bean_origin + ' ' + @string2 + ' ' + CAST(cocoa_percent AS nvarchar) AS message1
FROM ratings
WHERE 
	company = 'Ambrosia' 
	AND bean_type <> 'Unknown';

bean_type	bean_origin	cocoa_percent	message1
Trinitario	Belize		0.6400			Chocolate with beans from Belize has a cocoa percentage of 0.6400
Trinitario	Madagascar	0.6600			Chocolate with beans from Madagascar has a cocoa percentage of 0.6600
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
Create the same message, using the CONCAT() function.

DECLARE @string1 NVARCHAR(100) = 'Chocolate with beans from';
DECLARE @string2 NVARCHAR(100) = 'has a cocoa percentage of';

SELECT 
	bean_type,
	bean_origin,
	cocoa_percent,
	@string1 + ' ' + bean_origin + ' ' + @string2 + ' ' + CAST(cocoa_percent AS nvarchar) AS message1,
	CONCAT(@string1, ' ', bean_origin, ' ', @string2, ' ', CAST(cocoa_percent AS nvarchar)) AS message2
FROM ratings
WHERE 
	company = 'Ambrosia' 
	AND bean_type <> 'Unknown';

bean_type	bean_origin	cocoa_percent	message1																message2
Trinitario	Belize		0.6400			Chocolate with beans from Belize has a cocoa percentage of 0.6400		Chocolate with beans from Belize has a cocoa percentage of 0.6400
Trinitario	Madagascar	0.6600			Chocolate with beans from Madagascar has a cocoa percentage of 0.6600	Chocolate with beans from Madagascar has a cocoa percentage of 0.6600
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
Create the same message, using the CONCAT_WS() function. Evaluate the difference between this method and the previous ones.

DECLARE @string1 NVARCHAR(100) = 'Chocolate with beans from';
DECLARE @string2 NVARCHAR(100) = 'has a cocoa percentage of';

SELECT 
	bean_type,
	bean_origin,
	cocoa_percent,
	@string1 + ' ' + bean_origin + ' ' + @string2 + ' ' + CAST(cocoa_percent AS nvarchar) AS message1,
	CONCAT(@string1, ' ', bean_origin, ' ', @string2, ' ', cocoa_percent) AS message2,
	CONCAT_WS(' ', @string1, bean_origin, @string2, cocoa_percent) AS message3
FROM ratings
WHERE 
	company = 'Ambrosia' 
	AND bean_type <> 'Unknown';

bean_type	bean_origin	cocoa_percent	message1																message2																message3
Trinitario	Belize		0.6400			Chocolate with beans from Belize has a cocoa percentage of 0.6400		Chocolate with beans from Belize has a cocoa percentage of 0.6400		Chocolate with beans from Belize has a cocoa percentage of 0.6400
Trinitario	Madagascar	0.6600			Chocolate with beans from Madagascar has a cocoa percentage of 0.6600	Chocolate with beans from Madagascar has a cocoa percentage of 0.6600	Chocolate with beans from Madagascar has a cocoa percentage of 0.6600
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
</> Aggregating strings
Create a list with all the values found in the bean_origin column for the companies: ‘Bar Au Chocolat’, ‘Chocolate Con Amor’, ‘East Van Roasters’. The values should be separated by commas (,).

SELECT
	STRING_AGG(bean_origin, ',') AS bean_origins
FROM ratings
WHERE company IN ('Bar Au Chocolat', 'Chocolate Con Amor', 'East Van Roasters');

bean_origins
Duarte Province,Chiapas,Sambirano,Maranon Canyon,Peru,Madagascar,Dominican Republic,Bahia,Wild Bolivian,Uganda,Dominican Republic,Dominican Republic,Ocumare,Ecuador,Nicaragua
1
2
3
4
5
6
7
Create a list with the values found in the bean_origin column for each of the companies: ‘Bar Au Chocolat’, ‘Chocolate Con Amor’, ‘East Van Roasters’. The values should be separated by commas (,).

SELECT 
	company,
	STRING_AGG(bean_origin, ',') AS bean_origins
FROM ratings
WHERE company IN ('Bar Au Chocolat', 'Chocolate Con Amor', 'East Van Roasters')
GROUP BY company;

company				bean_origins
Bar Au Chocolat		Bahia,Duarte Province,Chiapas,Sambirano,Maranon Canyon
Chocolate Con Amor	Wild Bolivian,Uganda,Dominican Republic,Dominican Republic,Ocumare,Ecuador,Nicaragua
East Van Roasters	Peru,Madagascar,Dominican Republic
1
2
3
4
5
6
7
8
9
10
11
Arrange the values from the list in alphabetical order.

SELECT 
	company,
	STRING_AGG(bean_origin, ',') WITHIN GROUP (ORDER BY bean_origin) AS bean_origins
FROM ratings
WHERE company IN ('Bar Au Chocolat', 'Chocolate Con Amor', 'East Van Roasters')
GROUP BY company;

company				bean_origins
Bar Au Chocolat		Bahia,Chiapas,Duarte Province,Maranon Canyon,Sambirano
Chocolate Con Amor	Dominican Republic,Dominican Republic,Ecuador,Nicaragua,Ocumare,Uganda,Wild Bolivian
East Van Roasters	Dominican Republic,Madagascar,Peru
1
2
3
4
5
6
7
8
9
10
11
</> Splitting a string into pieces
Split the phrase declared in the variable @phrase into sentences (using the . separator).

DECLARE @phrase NVARCHAR(MAX) = 'In the morning I brush my teeth. In the afternoon I take a nap. In the evening I watch TV.'

SELECT value
FROM STRING_SPLIT(@phrase, '.');

value
In the morning I brush my teeth
In the afternoon I take a nap
In the evening I watch TV
1
2
3
4
5
6
7
8
9
Split the phrase declared in the variable @phrase into individual words.

DECLARE @phrase NVARCHAR(MAX) = 'In the morning I brush my teeth. In the afternoon I take a nap. In the evening I watch TV.'

SELECT value
FROM STRING_SPLIT(@phrase, ' ');

value
In
the
morning
...
1
2
3
4
5
6
7
8
9
10
</> Applying various string functions on data
Select only the voters whose first name has fewer than 5 characters and email address meets these conditions in the same time: (1) starts with the letter “j”, (2) the third letter is “a” and (3) is created at yahoo.com.

SELECT
	first_name,
    last_name,
	birthdate,
	email,
	country
FROM voters
WHERE LEN(first_name) < 5
	AND PATINDEX('j_a%@yahoo.com', email) > 0;

first_name	last_name	birthdate	email				country
Juan		Romero		1978-03-15	juan1@yahoo.com		Germany
Joan		Ross		1992-06-22	joan8@yahoo.com		Switzerland
Joan		Martin		1985-03-26	joan19@yahoo.com	Spain
1
2
3
4
5
6
7
8
9
10
11
12
13
14
Concatenate the first name and last name in the same column and present it in this format: " *** Firstname LASTNAME *** ".

SELECT
	CONCAT('***' , first_name, ' ', UPPER(last_name), '***') AS name,
    last_name,
	birthdate,
	email,
	country
FROM voters
WHERE LEN(first_name) < 5
	AND PATINDEX('j_a%@yahoo.com', email) > 0; 

name				last_name	birthdate	email				country
***Juan ROMERO***	Romero		1978-03-15	juan1@yahoo.com		Germany
***Joan ROSS***		Ross		1992-06-22	joan8@yahoo.com		Switzerland
***Joan MARTIN***	Martin		1985-03-26	joan19@yahoo.com	Spain
1
2
3
4
5
6
7
8
9
10
11
12
13
14
Mask the year part from the birthdate column, by replacing the last two digits with “XX” (1986-03-26 becomes 19XX-03-26).

SELECT
	CONCAT('***' , first_name, ' ', UPPER(last_name), '***') AS name,
    REPLACE(birthdate, SUBSTRING(CAST(birthdate AS varchar), 3, 2), 'XX') AS birthdate,
	email,
	country
FROM voters
WHERE LEN(first_name) < 5
	AND PATINDEX('j_a%@yahoo.com', email) > 0;    

name				birthdate	email				country
***Juan ROMERO***	19XX-03-15	juan1@yahoo.com		Germany
***Joan ROSS***		19XX-06-22	joan8@yahoo.com		Switzerland
***Joan MARTIN***	19XX-03-26	joan19@yahoo.com	Spain
1
2
3
4
5
6
7
8
9
10
11
12
13
4. Recognizing Numeric Data Properties
</> Learning how to count and add
Count the number of voters for each group.

Calculate the total number of votes per group.

SELECT 
	gender, 
	COUNT(customer_id) AS voters,
	SUM(total_votes) AS total_votes
FROM voters
GROUP BY gender;

gender	voters	total_votes
F 		91		9193
M 		105		10708
1
2
3
4
5
6
7
8
9
10
</> MINimizing and MAXimizing some results
Calculate the average percentage of cocoa used by each company.

SELECT 
	company,
	AVG(cocoa_percent) AS avg_cocoa
FROM ratings
GROUP BY company;

company		avg_cocoa
A. Morin	0.690869
Acalli		0.700000
Adi			0.750000
...
1
2
3
4
5
6
7
8
9
10
11
Calculate the minimum rating received by each company.

SELECT 
	company,
	AVG(cocoa_percent) AS avg_cocoa,
	MIN(rating) AS min_rating	
FROM ratings
GROUP BY company;

company		avg_cocoa	min_rating
A. Morin	0.690869	2.5000
Acalli		0.700000	3.5000
Adi			0.750000	2.7500
...
1
2
3
4
5
6
7
8
9
10
11
12
Calculate the maximum rating received by each company.

SELECT 
	company,
	AVG(cocoa_percent) AS avg_cocoa,
	MIN(rating) AS min_rating,
	MAX(rating) AS max_rating
FROM ratings
GROUP BY company;

company		avg_cocoa	min_rating	max_rating
A. Morin	0.690869	2.5000		4.0000
Acalli		0.700000	3.5000		3.5000
Adi			0.750000	2.7500		3.2500
...
1
2
3
4
5
6
7
8
9
10
11
12
13
Use an aggregate function to order the results of the query by the maximum rating, in descending order.

SELECT 
	company,
	AVG(cocoa_percent) AS avg_cocoa,
	MIN(rating) AS min_rating,
	MAX(rating) AS max_rating
FROM ratings
GROUP BY company
ORDER BY MAX(rating) DESC;

company			avg_cocoa	min_rating	max_rating
Askinosie		0.720000	3.0000		5.0000
Bonnat			0.755555	2.7500		5.0000
Black Mountain	0.700000	2.7500		4.0000
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
</> Accessing values from the next row
Create a new column, showing the number of votes recorded for the next person in the list.

Create a new column with the difference between the current voter’s total_votes and the votes of the next person.

SELECT 
	first_name,
	last_name,
	total_votes AS votes,
	LEAD(total_votes) OVER (ORDER BY total_votes) AS votes_next_voter,
	LEAD(total_votes) OVER (ORDER BY total_votes) - total_votes AS votes_diff
FROM voters
WHERE country = 'France'
ORDER BY total_votes;

first_name	last_name	votes	votes_next_voter	votes_diff
Roger		Rai			49		56					7
Meredith	Vance		56		73					17
Max			Ruiz		73		90					17
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
</> Accessing values from the previous row
Create a new column, showing the cocoa percentage of the chocolate bar that received a lower score, with cocoa coming from the same location (broad_bean_origin is the same).

Create a new column with the difference between the current bar’s cocoa percentage and the percentage of the previous bar.

SELECT 
	broad_bean_origin AS bean_origin,
	rating,
	cocoa_percent,
	LAG(cocoa_percent) 
		OVER(PARTITION BY broad_bean_origin ORDER BY rating) AS percent_lower_rating
FROM ratings
WHERE company = 'Fruition'
ORDER BY broad_bean_origin, rating ASC;

bean_origin	rating	cocoa_percent	percent_lower_rating
Bolivia		3.7500	0.7400			null
Costa Rica	1.5000	0.6600			null
Costa Rica	3.0000	0.7000			0.6600
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
</> Getting the first and last value
Boundary	Description
UNBOUNDED PRECEDING	first row in the partition
UNBOUNDED FOLLOWING	last row in the partition
CURRENT ROW	current row
PRECEDING	previous row
FOLLOWING	next row
Retrieve the birth date of the oldest voter from each country.

Retrieve the birth date of the youngest voter from each country.

SELECT 
	first_name + ' ' + last_name AS name,
	country,
	birthdate,
	FIRST_VALUE(birthdate) 
	OVER (PARTITION BY country ORDER BY birthdate) AS oldest_voter,
	LAST_VALUE(birthdate) 
		OVER (PARTITION BY country ORDER BY birthdate ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
				) AS youngest_voter
FROM voters
WHERE country IN ('Spain', 'USA');

name				country	birthdate	oldest_voter	youngest_voter
Caroline Griffin	Spain	1981-03-20	1981-03-20		1988-03-21
Christopher Jackson	Spain	1981-04-15	1981-03-20		1988-03-21
Raul Raji			Spain	1981-04-25	1981-03-20		1988-03-21
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
</> Extracting the sign and the absolute value
Calculate the absolute value of the result of the expression.

DECLARE @number1 DECIMAL(18,2) = -5.4;
DECLARE @number2 DECIMAL(18,2) = 7.89;
DECLARE @number3 DECIMAL(18,2) = 13.2;
DECLARE @number4 DECIMAL(18,2) = 0.003;

DECLARE @result DECIMAL(18,2) = @number1 * @number2 - @number3 - @number4;
SELECT 
	@result AS result,
	ABS(@result) AS abs_result;

result	abs_result
-55.81	55.81
1
2
3
4
5
6
7
8
9
10
11
12
Find out the sign of the result (positive or negative).

DECLARE @number1 DECIMAL(18,2) = -5.4;
DECLARE @number2 DECIMAL(18,2) = 7.89;
DECLARE @number3 DECIMAL(18,2) = 13.2;
DECLARE @number4 DECIMAL(18,2) = 0.003;

DECLARE @result DECIMAL(18,2) = @number1 * @number2 - @number3 - @number4;
SELECT 
	@result AS result,
	ABS(@result) AS abs_result,
	SIGN(@result) AS sign_result;

result	abs_result	sign_result
-55.81	55.81		-1.00
1
2
3
4
5
6
7
8
9
10
11
12
13
</> Rounding numbers
Round up the ratings to the nearest integer value.

SELECT
	rating,
	CEILING(rating) AS round_up
FROM ratings;

rating	round_up
2.7500	3
2.7500	3
2.7500	3
...
1
2
3
4
5
6
7
8
9
10
Round down the ratings to the nearest integer value.

SELECT
	rating,
	CEILING(rating) AS round_up,
	FLOOR(rating) AS round_down
FROM ratings;

rating		round_up	round_down
2.7500		3			2
2.7500		3			2
2.7500		3			2
...
1
2
3
4
5
6
7
8
9
10
11
Round the ratings to a decimal number with only 1 decimal.

SELECT
	rating,
	CEILING(rating) AS round_up,
	FLOOR(rating) AS round_down,
	ROUND(rating, 1) AS round_onedec
FROM ratings;

rating	round_up	round_down	round_onedec
2.7500	3			2			2.8000
2.7500	3			2			2.8000
2.7500	3			2			2.8000
...
1
2
3
4
5
6
7
8
9
10
11
12
Round the ratings to a decimal number with 2 decimals.

SELECT
	rating,
	CEILING(rating) AS round_up,
	FLOOR(rating) AS round_down,
	ROUND(rating, 1) AS round_onedec,
	ROUND(rating, 2) AS round_twodec
FROM ratings;

rating	round_up	round_down	round_onedec	round_twodec
2.7500	3			2			2.8000			2.7500
2.7500	3			2			2.8000			2.7500
2.7500	3			2			2.8000			2.7500
...
1
2
3
4
5
6
7
8
9
10
11
12
13
</> Working with exponential functions
Raise the number stored in the @number variable to the power from the @power variable.

Calculate the square of the @number variable (square means the power of 2).

Calculate the square root of the number stored in the @number variable.

DECLARE @number DECIMAL(4, 2) = 4.5;
DECLARE @power INT = 4;

SELECT
	@number AS number,
	@power AS power,
	POWER(@number, @power) AS number_to_power,
	SQUARE(@number) num_squared,
	SQRT(@number) num_square_root;

number	power	number_to_power	num_squared	num_square_root
4.50	4		410.06			20.25		2.1213203435596424
1
2
3
4
5
6
7
8
9
10
11
12
</> Manipulating numeric data
Select the number of cocoa flavors the company was rated on.

Select the lowest, highest and the average rating that each company received.

SELECT 
	company, 
	COUNT(*) AS flavors,
	MIN(rating) AS lowest_score,
	MAX(rating) AS highest_score,
	AVG(rating) AS avg_score	  
FROM ratings
GROUP BY company
ORDER BY flavors DESC;

company	flavors	lowest_score	highest_score	avg_score
Soma	47		2.0000			4.0000			3.015957
Bonnat	27		2.7500			5.0000			3.277777
Fresco	26		2.0000			4.0000			3.423076
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
Round the average rating to 1 decimal and show it as a different column.

SELECT 
	company, 
	COUNT(*) AS flavors,
	MIN(rating) AS lowest_score,  
	MAX(rating) AS highest_score,   
	AVG(rating) AS avg_score,
    ROUND(AVG(rating), 1) AS round_avg_score	
FROM ratings
GROUP BY company
ORDER BY flavors DESC;

company	flavors	lowest_score	highest_score	avg_score	round_avg_score
Soma	47		2.0000			4.0000			3.015957	3.000000
Bonnat	27		2.7500			5.0000			3.277777	3.300000
Fresco	26		2.0000			4.0000			3.423076	3.400000
...
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
Calculate the average rating received by each company and perform the following approximations:

a. round-up to the next integer value

b. round-down to the previous integer value.

SELECT 
	company, 
	COUNT(*) AS flavors,
	MIN(rating) AS lowest_score,   
	MAX(rating) AS highest_score,   
	AVG(rating) AS avg_score,
    ROUND(AVG(rating), 1) AS round_avg_score,
    CEILING(AVG(rating)) AS round_up_avg_score,   
	FLOOR(AVG(rating)) AS round_down_avg_score
FROM ratings
GROUP BY company
ORDER BY flavors DESC;

company	flavors	lowest_score	highest_score	avg_score	round_avg_score	round_up_avg_score	round_down_avg_score
Soma	47		2.0000			4.0000			3.015957	3.000000		4					3
Bonnat	27		2.7500			5.0000			3.277777	3.300000		4					3
Fresco	26		2.0000			4.0000			3.423076	3.400000		4					3
...
————————————————
版权声明：本文为CSDN博主「Daisy Lee」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_42871941/article/details/107307563

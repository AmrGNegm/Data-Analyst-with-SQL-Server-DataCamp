https://blog.csdn.net/weixin_42871941/article/details/107295648

Time Series Analysis in SQL Server
1. Working with Dates and Times
</> Break out a date into year, month, and day
Use the YEAR(), MONTH(), and DAY() functions to determine the year, month, and day for the current date and time.

DECLARE
	@SomeTime DATETIME2(7) = SYSUTCDATETIME();

SELECT
	YEAR(@SomeTime) AS TheYear,
	MONTH(@SomeTime) AS TheMonth,
	DAY(@SomeTime) AS TheDay;

TheYear	TheMonth	TheDay
2020	7			12
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
</> Break a date and time into component parts
Using the DATEPART() function, fill in the appropriate date parts. For a list of parts, review https://docs.microsoft.com/en-us/sql/t-sql/functions/datepart-transact-sql

DECLARE
	@BerlinWallFalls DATETIME2(7) = '1989-11-09 23:49:36.2294852';

-- Fill in each date part
SELECT
	DATEPART(YEAR, @BerlinWallFalls) AS TheYear,
	DATEPART(MONTH, @BerlinWallFalls) AS TheMonth,
	DATEPART(DAY, @BerlinWallFalls) AS TheDay,
	DATEPART(DAYOFYEAR, @BerlinWallFalls) AS TheDayOfYear,
	DATEPART(WEEKDAY, @BerlinWallFalls) AS TheDayOfWeek,
	DATEPART(WEEK, @BerlinWallFalls) AS TheWeek,
	DATEPART(SECOND, @BerlinWallFalls) AS TheSecond,
	DATEPART(NANOSECOND, @BerlinWallFalls) AS TheNanosecond;

TheYear	TheMonth	TheDay	TheDayOfYear	TheDayOfWeek	TheWeek	TheSecond	TheNanosecond
1989	11			9		313				5				45		36			229485200
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
Using the DATENAME() function, fill in the appropriate function calls.

DECLARE
	@BerlinWallFalls DATETIME2(7) = '1989-11-09 23:49:36.2294852';

SELECT
	DATENAME(YEAR, @BerlinWallFalls) AS TheYear,
	DATENAME(MONTH, @BerlinWallFalls) AS TheMonth,
	DATENAME(DAY, @BerlinWallFalls) AS TheDay,
	DATENAME(DAYOFYEAR, @BerlinWallFalls) AS TheDayOfYear,
	DATENAME(WEEKDAY, @BerlinWallFalls) AS TheDayOfWeek,
	DATENAME(WEEK, @BerlinWallFalls) AS TheWeek,
	DATENAME(SECOND, @BerlinWallFalls) AS TheSecond,
	DATENAME(NANOSECOND, @BerlinWallFalls) AS TheNanosecond;

TheYear	TheMonth	TheDay	TheDayOfYear	TheDayOfWeek	TheWeek	TheSecond	TheNanosecond
1989	November	9		313				Thursday		45		36			229485200
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
How many DATENAME() results differ from their DATEPART() counterparts?

 Zero
 One
 Two
 Three
 Four or more
</> Date math and leap years
Fill in the date parts and intervals needed to determine how SQL Server works on February 29th of a leap year.

2012 was a leap year. The leap year before it was 4 years earlier, and the leap year after it was 4 years later.

DECLARE
	@LeapDay DATETIME2(7) = '2012-02-29 18:00:00';

SELECT
	DATEADD(DAY, -1, @LeapDay) AS PriorDay,
	DATEADD(DAY, 1, @LeapDay) AS NextDay,
	DATEADD(YEAR, -4, @LeapDay) AS PriorLeapYear,
	DATEADD(YEAR, 4, @LeapDay) AS NextLeapYear,
	DATEADD(YEAR, -1, @LeapDay) AS PriorYear;

PriorDay			NextDay				PriorLeapYear		NextLeapYear		PriorYear
2012-02-28 18:00:00	2012-03-01 18:00:00	2008-02-29 18:00:00	2016-02-29 18:00:00	2011-02-28 18:00:00
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
Fill in the date parts and intervals needed to determine how SQL Server works on days next to a leap year.

DECLARE
	@PostLeapDay DATETIME2(7) = '2012-03-01 18:00:00';

SELECT
	DATEADD(DAY, -1, @PostLeapDay) AS PriorDay,
	DATEADD(DAY, 1, @PostLeapDay) AS NextDay,
	DATEADD(YEAR, -4, @PostLeapDay) AS PriorLeapYear,
	DATEADD(YEAR, 4, @PostLeapDay) AS NextLeapYear,
	DATEADD(YEAR, -1, @PostLeapDay) AS PriorYear,
	DATEADD(DAY, -1, DATEADD(YEAR, 4, @PostLeapDay)) AS NextLeapDay,
    DATEADD(DAY, -2, @PostLeapDay) AS TwoDaysAgo;

PriorDay			NextDay				PriorLeapYear		NextLeapYear		PriorYear			NextLeapDay			TwoDaysAgo
2012-02-29 18:00:00	2012-03-02 18:00:00	2008-03-01 18:00:00	2016-03-01 18:00:00	2011-03-01 18:00:00	2016-02-29 18:00:00	2012-02-28 18:00:00
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
Taking TwoDaysAgo from the prior step, use the DATEDIFF() function to test how it handles leap years.

DECLARE
	@PostLeapDay DATETIME2(7) = '2012-03-01 18:00:00',
    @TwoDaysAgo DATETIME2(7);

SELECT
	@TwoDaysAgo = DATEADD(DAY, -2, @PostLeapDay);

SELECT
	@TwoDaysAgo AS TwoDaysAgo,
	@PostLeapDay AS SomeTime,
	DATEDIFF(DAY, @TwoDaysAgo, @PostLeapDay) AS DaysDifference,
	DATEDIFF(HOUR, @TwoDaysAgo, @PostLeapDay) AS HoursDifference,
    DATEDIFF(MINUTE, @TwoDaysAgo, @PostLeapDay) AS MinutesDifference;

TwoDaysAgo			SomeTime			DaysDifference	HoursDifference	MinutesDifference
2012-02-28 18:00:00	2012-03-01 18:00:00	2				48				2880
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
</> Rounding dates
Use DATEADD() and DATEDIFF() in conjunction with date parts to round down our time to the day, hour, and minute.

DECLARE
	@SomeTime DATETIME2(7) = '2018-06-14 16:29:36.2248991';

SELECT
	DATEADD(DAY, DATEDIFF(DAY, 0, @SomeTime), 0) AS RoundedToDay,
	DATEADD(HOUR, DATEDIFF(HOUR, 0, @SomeTime), 0) AS RoundedToHour,
	DATEADD(MINUTE, DATEDIFF(MINUTE, 0, @SomeTime), 0) AS RoundedToMinute;

RoundedToDay		RoundedToHour		RoundedToMinute
2018-06-14 00:00:00	2018-06-14 16:00:00	2018-06-14 16:29:00
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
</> Formatting dates with CAST() and CONVERT()
Fill in the appropriate function, CAST(), for each example.

Using the aliases as a guide, fill in the appropriate variable for each example.

DECLARE
	@CubsWinWorldSeries DATETIME2(3) = '2016-11-03 00:30:29.245',
	@OlderDateType DATETIME = '2016-11-03 00:30:29.245';

SELECT
	CAST(@CubsWinWorldSeries AS DATE) AS CubsWinDateForm,
	CAST(@CubsWinWorldSeries AS NVARCHAR(30)) AS CubsWinStringForm,
	CAST(@OlderDateType AS DATE) AS OlderDateForm,
	CAST(@OlderDateType AS NVARCHAR(30)) AS OlderStringForm;

CubsWinDateForm	CubsWinStringForm		OlderDateForm	OlderStringForm
2016-11-03		2016-11-03 00:30:29.245	2016-11-03		Nov  3 2016 12:30AM
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
For the inner function, turn the date the Cubs won the World Series into a DATE data type using the CAST() function.

For the outer function, reshape this date as an NVARCHAR(30) using the CAST() function.

DECLARE
	@CubsWinWorldSeries DATETIME2(3) = '2016-11-03 00:30:29.245';

SELECT
	CAST(CAST(@CubsWinWorldSeries AS DATE) AS NVARCHAR(30)) AS DateStringForm;

DateStringForm
2016-11-03
1
2
3
4
5
6
7
8
Use the CONVERT() function to translate the date the Cubs won the world series into the DATE and NVARCHAR(30) data types.

The functional form for CONVERT() is CONVERT(DataType, SomeValue).

DECLARE
	@CubsWinWorldSeries DATETIME2(3) = '2016-11-03 00:30:29.245';

SELECT
	CONVERT(DATE, @CubsWinWorldSeries) AS CubsWinDateForm,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries) AS CubsWinStringForm;

CubsWinDateForm	CubsWinStringForm
2016-11-03		2016-11-03 00:30:29.245
1
2
3
4
5
6
7
8
9
Fill in the correct function call for conversion.

The UK date formats are 3 and 103, representing two-digit year (dmy) and four-digit year (dmyyyy), respectively.

The corresponding US date formats are 1 and 101.

DECLARE
	@CubsWinWorldSeries DATETIME2(3) = '2016-11-03 00:30:29.245';

SELECT
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 0) AS DefaultForm,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 3) AS UK_dmy,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 1) AS US_mdy,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 103) AS UK_dmyyyy,
	CONVERT(NVARCHAR(30), @CubsWinWorldSeries, 101) AS US_mdyyyy;

DefaultForm				UK_dmy		US_mdy		UK_dmyyyy	US_mdyyyy
Nov  3 2016 12:30AM		03/11/16	11/03/16	03/11/2016	11/03/2016
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
</> Formatting dates with FORMAT()
Fill in the function and use the ‘d’ format parameter (note that this is case sensitive!) to format as short dates. Also, fill in the culture for Japan, which in the .NET framework is jp-JP (this is not case sensitive).

DECLARE
	@Python3ReleaseDate DATETIME2(3) = '2008-12-03 19:45:00.033';

SELECT
	FORMAT(@Python3ReleaseDate, 'd', 'en-US') AS US_d,
	FORMAT(@Python3ReleaseDate, 'd', 'de-DE') AS DE_d,
	FORMAT(@Python3ReleaseDate, 'd', 'jp-JP') AS JP_d,
	FORMAT(@Python3ReleaseDate, 'd', 'zh-cn') AS CN_d;

US_d		DE_d		JP_d		CN_d
12/3/2008	03.12.2008	12/03/2008	2008/12/3
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
Use the ‘D’ format parameter (this is case sensitive!) to build long dates. Also, fill in the culture for Indonesia, which in the .NET framework is id-ID.

DECLARE
	@Python3ReleaseDate DATETIME2(3) = '2008-12-03 19:45:00.033';

SELECT
	FORMAT(@Python3ReleaseDate, 'D', 'en-US') AS US_D,
	FORMAT(@Python3ReleaseDate, 'D', 'de-DE') AS DE_D,
	FORMAT(@Python3ReleaseDate, 'D', 'id-ID') AS ID_D,
	FORMAT(@Python3ReleaseDate, 'D', 'zh-cn') AS CN_D;

US_D						DE_D						ID_D					CN_D
Wednesday, December 3, 2008	Mittwoch, 3. Dezember 2008	Rabu, 03 Desember 2008	2008年12月3日
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
Fill in the custom format strings needed to generate the results in preceding comments. Use date parts such as yyyy, MM, MMM, and dd. Capitalization is important for the FORMAT() function! See the full list at https://bit.ly/30SGA5a.

DECLARE
	@Python3ReleaseDate DATETIME2(3) = '2008-12-03 19:45:00.033';
    
SELECT
	FORMAT(@Python3ReleaseDate, 'yyyyMMdd') AS F1,
	FORMAT(@Python3ReleaseDate, 'yyyy-MM-dd') AS F2,
	FORMAT(@Python3ReleaseDate, 'MMM dd+yyyy') AS F3,
	FORMAT(@Python3ReleaseDate, 'MM yy dd') AS F4,
	FORMAT(@Python3ReleaseDate, 'dd HH:mm yyyy.ss') AS F5;

F1			F2			F3			F4			F5
20081203	2008-12-03	Dec 03+2008	12 08 03	03 19:45 2008.00
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
</> The benefits of calendar tables
Which of the following is not a benefit of using a calendar table?

 Calendar tables can simplify queries which perform complicated date math.
 Calendar tables can let you to perform actions you could not otherwise do in T-SQL.
 Calendar tables can improve performance when filtering on date conditions (such as finding all things which happened on the fifth Tuesday of a month).
 Calendar tables can ensure that different developers use the same sets of holidays in their queries.
</> Try out a calendar table
Find the dates of all Tuesdays in December covering the calendar years 2008 through 2010.

SELECT
	c.Date
FROM dbo.Calendar c
WHERE
	c.MonthName = 'December'
	AND c.DayName = 'Tuesday'
	AND c.CalendarYear BETWEEN 2008 AND 2010
ORDER BY
	c.Date;

Date
2008-12-02
2008-12-09
2008-12-16
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
Find the dates for fiscal week 29 of fiscal year 2019.

SELECT
	c.Date
FROM dbo.Calendar c
WHERE
	c.FiscalWeekOfYear = 29
	AND c.FiscalYear = 2019
ORDER BY
	c.Date ASC;

Date
2020-01-12
2020-01-13
2020-01-14
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
</> Joining to a calendar table
Fill in the blanks to determine which dates had type 3 incidents during the third fiscal quarter of FY2019.

SELECT
	ir.IncidentDate,
	c.FiscalDayOfYear,
	c.FiscalWeekOfYear
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	ir.IncidentTypeID = 3
	AND c.FiscalYear = 2019
	AND c.FiscalQuarter = 3;

IncidentDate	FiscalDayOfYear	FiscalWeekOfYear
2020-01-02		186				27
2020-01-05		189				28
2020-01-06		190				28
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
Fill in the gaps in to determine type 4 incidents which happened on weekends in FY2019 after fiscal week 30.

SELECT
	ir.IncidentDate,
	c.FiscalDayOfYear,
	c.FiscalWeekOfYear
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	ir.IncidentTypeID = 4
	AND c.FiscalYear = 2019
	AND c.FiscalWeekOfYear > 30
	AND c.IsWeekend = 1;

IncidentDate	FiscalDayOfYear	FiscalWeekOfYear
2020-02-16		231				34
2020-02-23		238				35
2020-03-21		265				38
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
2. Converting to Dates and Times
</> Build dates from parts
Create dates from component parts in the calendar table: calendar year, calendar month, and the day of the month.

SELECT TOP(10)
	DATEFROMPARTS(c.CalendarYear, c.CalendarMonth, c.Day) AS CalendarDate
FROM dbo.Calendar c
WHERE
	c.CalendarYear = 2017
ORDER BY
	c.FiscalDayOfYear ASC;

CalendarDate
2017-07-01
2017-07-02
2017-07-03
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
Create dates from the component parts of the calendar table. Use the calendar year, calendar month, and day of month.

SELECT TOP(10)
	c.CalendarQuarterName,
	c.MonthName,
	c.CalendarDayOfYear
FROM dbo.Calendar c
WHERE
	DATEFROMPARTS(c.CalendarYear, c.CalendarMonth, c.Day) >= '2018-06-01'
	AND c.DayName = 'Tuesday'
ORDER BY
	c.FiscalYear,
	c.FiscalDayOfYear ASC;

CalendarQuarterName	MonthName	CalendarDayOfYear
Q2					June		156
Q2					June		163
Q2					June		170
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
</> Build dates and times from parts
Build the date and time (using DATETIME2FROMPARTS()) that Neil and Buzz became the first people to land on the moon. Note the “2” in DATETIME2FROMPARTS(), meaning we want to build a DATETIME2 rather than a DATETIME.

Build the date and time (using DATETIMEFROMPARTS()) that Neil and Buzz took off from the moon. Note that this is for a DATETIME, not a DATETIME2.

SELECT
	DATETIME2FROMPARTS(1969, 07, 20, 20, 17, 00, 000, 0) AS TheEagleHasLanded,
	DATETIMEFROMPARTS(1969, 07, 21, 18, 54, 00, 000) AS MoonDeparture;

TheEagleHasLanded	MoonDeparture
1969-07-20 20:17:00	1969-07-21 18:54:00
1
2
3
4
5
6
</> Build dates and times with offsets from parts
Build a DATETIMEOFFSET which represents the last millisecond before the Y2.038K problem hits. The offset should be UTC.

Build a DATETIMEOFFSET which represents the moment devices hit the Y2.038K issue in UTC time. Then use the AT TIME ZONE operator to convert this to Eastern Standard Time.

SELECT
	DATETIMEOFFSETFROMPARTS(2038, 01, 19, 03, 14, 07, 999, 0, 0, 3) AS LastMoment,
	DATETIMEOFFSETFROMPARTS(2038, 01, 19, 03, 14, 08, 0, 0, 0, 3) AT TIME ZONE 'Eastern Standard Time' AS TimeForChaos;

LastMoment							TimeForChaos
2038-01-19 03:14:07.9990000 +00:00	2038-01-18 22:14:08.0000000 -05:00
1
2
3
4
5
6
</> Cast strings to dates
Cast the input string DateText in the dbo.Dates temp table to the DATE data type.

Cast the input string DateText in the dbo.Dates temp table to the DATETIME2(7) data type.

SELECT
	d.DateText AS String,
	CAST(d.DateText AS DATE) AS StringAsDate,
	CAST(d.DateText AS DATETIME2(7)) AS StringAsDateTime2
FROM dbo.Dates d;

String					StringAsDate	StringAsDateTime2
2019-04-01 18:08:19.290	2019-04-01		2019-04-01 18:08:19.290000
2019-04-07 06:14:30		2019-04-07		2019-04-07 06:14:30
2017-03-01				2017-03-01		2017-03-01 00:00:00
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
</> Convert strings to dates
Use the CONVERT() function to translate DateText into a date data type.

Then use the CONVERT() function to translate DateText into a DATETIME2(7) data type.

SET LANGUAGE 'GERMAN'

SELECT
	d.DateText AS String,
	CONVERT(DATE, d.DateText) AS StringAsDate,
	CONVERT(DATETIME2, d.DateText) AS StringAsDateTime2
FROM dbo.Dates d;

String					StringAsDate	StringAsDateTime2
2019-04-01 18:08:19.290	2019-04-01		2019-04-01 18:08:19.290000
2019-04-07 06:14:30		2019-04-07		2019-04-07 06:14:30
2017-03-01				2017-03-01		2017-03-01 00:00:00
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
</> Parse strings to dates
Parse DateText as dates using the German locale (de-de).

Then, parse DateText as the data type DATETIME2(7), still using the German locale.

SELECT
	d.DateText AS String,
	PARSE(d.DateText AS DATE USING 'de-de') AS StringAsDate,
	PARSE(d.DateText AS DATETIME2(7) USING 'de-de') AS StringAsDateTime2
FROM dbo.Dates d;

String					StringAsDate	StringAsDateTime2
2019-04-01 18:08:19.290	2019-04-01		2019-04-01 18:08:19.290000
2019-04-07 06:14:30		2019-04-07		2019-04-07 06:14:30
2017-03-01				2017-03-01		2017-03-01 00:00:00
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
</> Changing a date’s offset
Fill in the appropriate function call for Brasilia, Brazil.

Fill in the appropriate function call and time zone for Chicago, Illinois. In August, Chicago is 2 hours behind Brasilia Standard Time.

Fill in the appropriate function call and time zone for New Delhi, India. India does not observe Daylight Savings Time, so in August, New Delhi is 8 1/2 hours ahead of Brasilia Standard Time.

DECLARE
	@OlympicsUTC NVARCHAR(50) = N'2016-08-08 23:00:00';

SELECT
	SWITCHOFFSET(@OlympicsUTC, '-03:00') AS BrasiliaTime,
	SWITCHOFFSET(@OlympicsUTC, '-05:00') AS ChicagoTime,
	SWITCHOFFSET(@OlympicsUTC, '+05:30') AS NewDelhiTime;

BrasiliaTime						ChicagoTime							NewDelhiTime
2016-08-08 20:00:00.0000000 -03:00	2016-08-08 18:00:00.0000000 -05:00	2016-08-09 04:30:00.0000000 +05:30
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
</> Using the time zone DMV to look up times
Create a valid SQL query by dragging and dropping the items into the correct sequence.

DECLARE @OlympicsUTC NVARCHAR(50) = N'2016-08-08 23:00:00';
SELECT
SWITCHOFFSET(@OlympicsUTC, tzi.current_utc_offset) AS NDelhiTime
FROM sys.time_zone_info AS tzi
WHERE
tzi.name = N'India Standard Time';
1
2
3
4
5
6
</> Converting to a date offset
Fill in the time in Phoenix, Arizona, which, being Mountain Standard Time, was UTC -07:00.

Fill in the time for Tuvalu, which is 12 hours ahead of UTC.

DECLARE
	@OlympicsClosingUTC DATETIME2(0) = '2016-08-21 23:00:00';

SELECT
	TODATETIMEOFFSET(DATEADD(HOUR, -7, @OlympicsClosingUTC), '-07:00') AS PhoenixTime,
	TODATETIMEOFFSET(DATEADD(HOUR, 12, @OlympicsClosingUTC), '+12:00') AS TuvaluTime;

PhoenixTime							TuvaluTime
2016-08-21 16:00:00.0000000 -07:00	2016-08-22 11:00:00.0000000 +12:00
1
2
3
4
5
6
7
8
9
</> Try out type-safe date functions
Starting with the TRY_CONVERT() function, fill in the function name and input parameter for each example.

DECLARE
	@GoodDateINTL NVARCHAR(30) = '2019-03-01 18:23:27.920',
	@GoodDateDE NVARCHAR(30) = '13.4.2019',
	@GoodDateUS NVARCHAR(30) = '4/13/2019',
	@BadDate NVARCHAR(30) = N'SOME BAD DATE';

SELECT
	TRY_CONVERT(DATETIME2(3), @GoodDateINTL) AS GoodDateINTL,
	TRY_CONVERT(DATE, @GoodDateDE) AS GoodDateDE,
	TRY_CONVERT(DATE, @GoodDateUS) AS GoodDateUS,
	TRY_CONVERT(DATETIME2(3), @BadDate) AS BadDate;

GoodDateINTL				GoodDateDE	GoodDateUS	BadDate
2019-03-01 18:23:27.920000	null		2019-04-13	null
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
With the prior TRY_CONVERT() solution in mind, use TRY_CAST() to see how they compare.

DECLARE
	@GoodDateINTL NVARCHAR(30) = '2019-03-01 18:23:27.920',
	@GoodDateDE NVARCHAR(30) = '13.4.2019',
	@GoodDateUS NVARCHAR(30) = '4/13/2019',
	@BadDate NVARCHAR(30) = N'SOME BAD DATE';

SELECT
	TRY_CAST(@GoodDateINTL AS DATETIME2(3)) AS GoodDateINTL,
	TRY_CAST(@GoodDateDE AS DATE) AS GoodDateDE,
	TRY_CAST(@GoodDateUS AS DATE) AS GoodDateUS,
	TRY_CAST(@BadDate AS DATETIME2(3)) AS BadDate;

GoodDateINTL				GoodDateDE	GoodDateUS	BadDate
2019-03-01 18:23:27.920000	null		2019-04-13	null
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
One of our good dates returns NULL. Use TRY_PARSE() and specify de-de for the German date and en-us for the US date.

DECLARE
	@GoodDateINTL NVARCHAR(30) = '2019-03-01 18:23:27.920',
	@GoodDateDE NVARCHAR(30) = '13.4.2019',
	@GoodDateUS NVARCHAR(30) = '4/13/2019',
	@BadDate NVARCHAR(30) = N'SOME BAD DATE';

SELECT
	TRY_PARSE(@GoodDateINTL AS DATETIME2(3)) AS GoodDateINTL,
	TRY_PARSE(@GoodDateDE AS DATE USING 'de-de') AS GoodDateDE,
	TRY_PARSE(@GoodDateUS AS DATE USING 'en-us') AS GoodDateUS,
	TRY_PARSE(@BadDate AS DATETIME2(3) USING 'sk-sk') AS BadDate;

GoodDateINTL				GoodDateDE	GoodDateUS	BadDate
2019-03-01 18:23:27.920000	2019-04-13	2019-04-13	null
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
</> Convert imported data to dates with time zones
Fill in the missing TRY_XXX() function name inside the EventDates common table expression.

Convert the EventDateOffset event dates to ‘UTC’. Call this output EventDateUTC.

Convert the EventDateOffset event dates to ‘US Eastern Standard Time’ using AT TIME ZONE. Call this output EventDateUSEast.

WITH EventDates AS
(
    SELECT
        TRY_CONVERT(DATETIME2(3), it.EventDate) AT TIME ZONE it.TimeZone AS EventDateOffset,
        it.TimeZone
    FROM dbo.ImportedTime it
        INNER JOIN sys.time_zone_info tzi
			ON it.TimeZone = tzi.name
)
SELECT
	CONVERT(NVARCHAR(50), ed.EventDateOffset) AS EventDateOffsetString,
	CONVERT(DATETIME2(0), ed.EventDateOffset) AS EventDateLocal,
	ed.TimeZone,
	CAST(ed.EventDateOffset AT TIME ZONE 'UTC' AS DATETIME2(0)) AS EventDateUTC,
	CAST(ed.EventDateOffset AT TIME ZONE 'US Eastern Standard Time'  AS DATETIME2(0)) AS EventDateUSEast
FROM EventDates ed;

EventDateOffsetString			EventDateLocal		TimeZone						EventDateUTC		EventDateUSEast
2019-04-08 08:00:00.000 +09:30	2019-04-08 08:00:00	Cen. Australia Standard Time	2019-04-07 22:30:00	2019-04-07 18:30:00
2019-04-08 08:00:00.000 -04:00	2019-04-08 08:00:00	US Eastern Standard Time		2019-04-08 12:00:00	2019-04-08 08:00:00
2019-04-08 08:00:00.000 +08:00	2019-04-08 08:00:00	China Standard Time				2019-04-08 00:00:00	2019-04-07 20:00:00
2019-04-08 08:00:00.000 +01:00	2019-04-08 08:00:00	Morocco Standard Time			2019-04-08 07:00:00	2019-04-08 03:00:00
null							null				Morocco Standard Time			null				null
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
19
20
21
22
23
</> Test type-safe conversion function performance
Fill in the correct conversion function based on its parameter signature.

Write down or remember the amount of time returned in the DATEDIFF() call.

-- Try out how fast the TRY_CAST() function is
-- by try-casting each DateText value to DATE
DECLARE @StartTimeCast DATETIME2(7) = SYSUTCDATETIME();
SELECT TRY_CAST(DateText AS DATE) AS TestDate FROM #DateText;
DECLARE @EndTimeCast DATETIME2(7) = SYSUTCDATETIME();

SELECT
    DATEDIFF(MILLISECOND, @StartTimeCast, @EndTimeCast) AS ExecutionTimeCast;

ExecutionTimeCast
8
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
Fill in the correct conversion function based on its parameter signature.

Write down or remember the amount of time returned in the DATEDIFF() call.

-- Try out how fast the TRY_CONVERT() function is
-- by try-converting each DateText value to DATE
DECLARE @StartTimeConvert DATETIME2(7) = SYSUTCDATETIME();
SELECT TRY_CONVERT(DATE, DateText) AS TestDate FROM #DateText;
DECLARE @EndTimeConvert DATETIME2(7) = SYSUTCDATETIME();

SELECT
    DATEDIFF(MILLISECOND, @StartTimeConvert, @EndTimeConvert) AS ExecutionTimeConvert;

ExecutionTimeConvert
8
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
Fill in the correct conversion function based on its parameter signature.

Write down or remember the amount of time returned in the DATEDIFF() call.

-- Try out how fast the TRY_PARSE() function is
-- by try-parsing each DateText value to DATE
DECLARE @StartTimeParse DATETIME2(7) = SYSUTCDATETIME();
SELECT TRY_PARSE(DateText AS DATE) AS TestDate FROM #DateText;
DECLARE @EndTimeParse DATETIME2(7) = SYSUTCDATETIME();

SELECT
    DATEDIFF(MILLISECOND, @StartTimeParse, @EndTimeParse) AS ExecutionTimeParse;

ExecutionTimeParse
622
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
Based on what you have learned so far, how would you compare the performance of TRY_PARSE() versus TRY_CAST() and TRY_CONVERT()?

 TRY_PARSE() is faster than both TRY_CAST() and TRY_CONVERT().
 TRY_PARSE() is faster than TRY_CAST() but slower than TRY_CONVERT().
 TRY_PARSE() is faster than TRY_CONVERT() but slower than TRY_CAST().
 TRY_CAST() and TRY_CONVERT() are both faster than TRY_PARSE().
 All three functions take approximately the same amount of time to complete.
3. Aggregating Time Series Data
</> Summarize data over a time frame
Fill in the appropriate aggregate function based on the column name. Choose from COUNT(), SUM(), MIN(), and MAX() for each.

SELECT
	it.IncidentType,
	COUNT(1) AS NumberOfRows,
	SUM(ir.NumberOfIncidents) AS TotalNumberOfIncidents,
	MIN(ir.NumberOfIncidents) AS MinNumberOfIncidents,
	MAX(ir.NumberOfIncidents) AS MaxNumberOfIncidents,
	MIN(ir.IncidentDate) As MinIncidentDate,
	MAX(ir.IncidentDate) AS MaxIncidentDate
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.IncidentType it
		ON ir.IncidentTypeID = it.IncidentTypeID
WHERE
	ir.IncidentDate BETWEEN '2019-08-01' AND '2019-10-31'
GROUP BY
	it.IncidentType;

IncidentType					NumberOfRows	TotalNumberOfIncidents	MinNumberOfIncidents	MaxNumberOfIncidents	MinIncidentDate	MaxIncidentDate
Clicked malicious link			24				110						1						12						2019-08-01		2019-10-28
Forgot key card					52				216						1						10						2019-08-01		2019-10-31
Installed unapproved software	53				251						1						10						2019-08-01		2019-10-29
Left computer unlocked			70				366						1						11						2019-08-01		2019-10-31
Ransomware installed			12				79						3						11						2019-08-01		2019-10-23
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
19
20
21
22
</> Calculating distinct counts
Return the count of distinct incident type IDs as NumberOfIncidentTypes

Return the count of distinct incident dates as NumberOfDaysWithIncidents

Fill in the appropriate function call and input column to determine number of unique incident types and number of days with incidents in our rollup table.

SELECT
	COUNT(DISTINCT ir.IncidentTypeID) AS NumberOfIncidentTypes,
	COUNT(DISTINCT ir.IncidentDate) AS NumberOfDaysWithIncidents
FROM dbo.IncidentRollup ir
WHERE
ir.IncidentDate BETWEEN '2019-08-01' AND '2019-10-31';

NumberOfIncidentTypes	NumberOfDaysWithIncidents
5						91
1
2
3
4
5
6
7
8
9
</> Calculating filtered aggregates
Fill in a CASE expression which lets us use the SUM() function to calculate the number of big-incident and small-incident days.

In the CASE expression, you should return 1 if the appropriate filter criterion is met and 0 otherwise.

Be sure to specify the alias when referencing a column, like ir.IncidentDate or it.IncidentType!

SELECT
	it.IncidentType,
	SUM(CASE WHEN ir.NumberOfIncidents > 5 THEN 1 ELSE 0 END) AS NumberOfBigIncidentDays,
    SUM(CASE WHEN ir.NumberOfIncidents <= 5 THEN 1 ELSE 0 END) AS NumberOfSmallIncidentDays
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.IncidentType it
		ON ir.IncidentTypeID = it.IncidentTypeID
WHERE
	ir.IncidentDate BETWEEN '2019-08-01' AND '2019-10-31'
GROUP BY
it.IncidentType;

IncidentType					NumberOfBigIncidentDays	NumberOfSmallIncidentDays
Clicked malicious link			8						16
Forgot key card					15						37
Installed unapproved software	18						35
Left computer unlocked			32						38
Ransomware installed			9						3
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
</> Working with statistical aggregate functions
Fill in the missing aggregate functions. For standard deviation and variance, use the sample functions rather than population functions.

SELECT
	it.IncidentType,
	AVG(ir.NumberOfIncidents) AS MeanNumberOfIncidents,
	AVG(CAST(ir.NumberOfIncidents AS DECIMAL(4,2))) AS MeanNumberOfIncidents,
	STDEV(ir.NumberOfIncidents) AS NumberOfIncidentsStandardDeviation,
	VAR(ir.NumberOfIncidents) AS NumberOfIncidentsVariance,
    COUNT(1) AS NumberOfRows
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.IncidentType it
		ON ir.IncidentTypeID = it.IncidentTypeID
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	c.CalendarQuarter = 2
	AND c.CalendarYear = 2020
GROUP BY
it.IncidentType;

IncidentType					MeanNumberOfIncidents	MeanNumberOfIncidents	NumberOfIncidentsStandardDeviation	NumberOfIncidentsVariance	NumberOfRows
Clicked malicious link			4						4.666666				2.287647991569799					5.233333333333334			21
Forgot key card					4						4.888888				2.6753004886185243					7.157232704402515			54
Installed unapproved software	5						5.709677				2.979860716958616					8.879569892473118			31
Left computer unlocked			5						5.634920				3.0283690239487813					9.171018945212495			63
Ransomware installed			5						5.833333				1.8348478592697184					3.3666666666666685			6
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
19
20
21
22
23
24
</> Calculating median in SQL Server
Fill in the missing value for PERCENTILE_CONT().

Inside the WITHIN GROUP() clause, order by number of incidents descending.

In the OVER() clause, partition by IncidentType (the actual text value, not the ID).

SELECT DISTINCT
	it.IncidentType,
	AVG(CAST(ir.NumberOfIncidents AS DECIMAL(4,2)))
	    OVER(PARTITION BY it.IncidentType) AS MeanNumberOfIncidents,
	PERCENTILE_CONT(0.5)
    	WITHIN GROUP (ORDER BY ir.NumberOfIncidents DESC)
        OVER (PARTITION BY it.IncidentType) AS MedianNumberOfIncidents,
	COUNT(1) OVER (PARTITION BY it.IncidentType) AS NumberOfRows
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.IncidentType it
		ON ir.IncidentTypeID = it.IncidentTypeID
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	c.CalendarQuarter = 2
	AND c.CalendarYear = 2020;

IncidentType					MeanNumberOfIncidents	MedianNumberOfIncidents	NumberOfRows
Clicked malicious link			4.666666				4						21
Forgot key card					4.888888				4.5						54
Installed unapproved software	5.709677				5						31
Left computer unlocked			5.634920				6						63
Ransomware installed			5.833333				6						6
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
19
20
21
22
23
</> Downsample to a daily grain
Downsample customer visit start times to the daily grain and aggregate results.

Fill in the GROUP BY clause with any non-aggregated values in the SELECT clause (but without aliases like AS Day).

SELECT
	CAST(dsv.CustomerVisitStart AS DATE) AS Day,
	SUM(dsv.AmenityUseInMinutes) AS AmenityUseInMinutes,
	COUNT(1) AS NumberOfAttendees
FROM dbo.DaySpaVisit dsv
WHERE
	dsv.CustomerVisitStart >= '2020-06-11'
	AND dsv.CustomerVisitStart < '2020-06-23'
GROUP BY
	CAST(dsv.CustomerVisitStart AS DATE)
ORDER BY
	Day;

Day			AmenityUseInMinutes	NumberOfAttendees
2020-06-11	28796				328
2020-06-12	30102				337
2020-06-13	30585				341
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
</> Downsample to a weekly grain
Downsample the day spa visit data to a weekly grain using the DATEPART() function.

Find the customer with the largest customer ID for a given week.

Fill in the GROUP BY clause with any non-aggregated values in the SELECT clause (but without aliases like AS Week).

SELECT
	DATEPART(WEEK, dsv.CustomerVisitStart) AS Week,
	SUM(dsv.AmenityUseInMinutes) AS AmenityUseInMinutes,
	MAX(dsv.CustomerID) AS HighestCustomerID,
	COUNT(1) AS NumberOfAttendees
FROM dbo.DaySpaVisit dsv
WHERE
	dsv.CustomerVisitStart >= '2020-01-01'
	AND dsv.CustomerVisitStart < '2021-01-01'
GROUP BY
	DATEPART(WEEK, dsv.CustomerVisitStart)
ORDER BY
	Week;

Week	AmenityUseInMinutes	HighestCustomerID	NumberOfAttendees
1		117002				90					1334
2		206917				90					2348
3		204806				90					2289
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
19
</> Downsample using a calendar table
Find and include the week of the calendar year.

Include the minimum value of c.Date in each group as FirstDateOfWeek. This works because we are grouping by week.

Join the Calendar table to the DaySpaVisit table based on the calendar table’s date and each day spa customer’s date of visit. CustomerVisitStart is a DATETIME2 which includes time, so a direct join would only include visits starting at exactly midnight.

Group by the week of calendar year.

SELECT
	c.CalendarWeekOfYear,
	MIN(c.Date) AS FirstDateOfWeek,
	ISNULL(SUM(dsv.AmenityUseInMinutes), 0) AS AmenityUseInMinutes,
	ISNULL(MAX(dsv.CustomerID), 0) AS HighestCustomerID,
	COUNT(dsv.CustomerID) AS NumberOfAttendees
FROM dbo.Calendar c
	LEFT OUTER JOIN dbo.DaySpaVisit dsv
		ON c.Date = CAST(dsv.CustomerVisitStart AS Date)
WHERE
	c.CalendarYear = 2020
GROUP BY
	c.CalendarWeekOfYear
ORDER BY
	c.CalendarWeekOfYear;

CalendarWeekOfYear	FirstDateOfWeek	AmenityUseInMinutes	HighestCustomerID	NumberOfAttendees
1					2020-01-01		117002				90					1334
2					2020-01-05		206917				90					2348
3					2020-01-12		204806				90					2289
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
19
20
21
</> Generate a summary with ROLLUP
Complete the definition for NumberOfIncidents by adding up the number of incidents over each range.

Fill out the GROUP BY segment, including the WITH ROLLUP operator.

SELECT
	c.CalendarYear,
	c.CalendarQuarterName,
	c.CalendarMonth,
    -- Include the sum of incidents by day over each range
	SUM(ir.NumberOfIncidents) AS NumberOfIncidents
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	ir.IncidentTypeID = 2
GROUP BY
	-- GROUP BY needs to include all non-aggregated columns
	c.CalendarYear,
	c.CalendarQuarterName,
	c.CalendarMonth
-- Fill in your grouping operator
WITH ROLLUP
ORDER BY
	c.CalendarYear,
	c.CalendarQuarterName,
	c.CalendarMonth;
	
CalendarYear	CalendarQuarterName	CalendarMonth	NumberOfIncidents
null			null				null			957
2019			null				null			482
2019			Q3					null			221
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
19
20
21
22
23
24
25
26
27
28
</> View all aggregations with CUBE
Fill in the missing columns from dbo.Calendar in the SELECT clause.

Fill out the GROUP BY segment, including the CUBE operator.

SELECT
	ir.IncidentTypeID,
	c.CalendarQuarterName,
	c.WeekOfMonth,
	SUM(ir.NumberOfIncidents) AS NumberOfIncidents
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	ir.IncidentTypeID IN (3, 4)
GROUP BY
	ir.IncidentTypeID,
	c.CalendarQuarterName,
	c.WeekOfMonth
WITH CUBE
ORDER BY
	ir.IncidentTypeID,
	c.CalendarQuarterName,
	c.WeekOfMonth;

IncidentTypeID	CalendarQuarterName	WeekOfMonth	NumberOfIncidents
null			null				null		1241
null			null				1			218
null			null				2			295
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
19
20
21
22
23
24
25
In which quarter did we see the greatest number of incidents?

 Quarter 1
 Quarter 2
 Quarter 3
 Quarter 4
</> Generate custom groupings with GROUPING SETS
Fill out the GROUP BY segment using GROUPING SETS. We want to see:

One row for each combination of year, quarter, and month (in that hierarchical order)

One row for each year

One row with grand totals (that is, a blank group)

SELECT
	c.CalendarYear,
	c.CalendarQuarterName,
	c.CalendarMonth,
	SUM(ir.NumberOfIncidents) AS NumberOfIncidents
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	ir.IncidentTypeID = 2
GROUP BY GROUPING SETS
(
	(c.CalendarYear, c.CalendarQuarterName, c.CalendarMonth),
	(c.CalendarYear),
	()
)
ORDER BY
	c.CalendarYear,
	c.CalendarQuarterName,
	c.CalendarMonth;

CalendarYear	CalendarQuarterName	CalendarMonth	NumberOfIncidents
null			null				null			957
2019			null				null			482
2019			Q3					7				71
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
19
20
21
22
23
24
25
26
</> Combine multiple aggregations in one query
Fill out the grouping sets based on our conjectures above. We want to see the following grouping sets in addition to our grand total:

One set by calendar year and month
One set by the day of the week
One set by whether the date is a weekend or not
SELECT
	c.CalendarYear,
	c.CalendarMonth,
	c.DayOfWeek,
	c.IsWeekend,
	SUM(ir.NumberOfIncidents) AS NumberOfIncidents
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
GROUP BY GROUPING SETS
(
	(c.CalendarYear, c.CalendarMonth),
	(c.DayOfWeek),
	(c.IsWeekend),
	()
)
ORDER BY
	c.CalendarYear,
	c.CalendarMonth,
	c.DayOfWeek,
	c.IsWeekend;

CalendarYear	CalendarMonth	DayOfWeek	IsWeekend	NumberOfIncidents
null			null			null		null		3756
null			null			null		false		2677
null			null			null		true		1079
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
19
20
21
22
23
24
25
26
27
4. Answering Time Series Questions with Window Functions
</> Contrasting ROW_NUMBER(), RANK(), and DENSE_RANK()
Fill in each window function based on the column alias. You should include ROW_NUMBER(), RANK(), and DENSE_RANK() exactly once.

Fill in the OVER clause ordering by ir.NumberOfIncidents in descending order.

SELECT
	ir.IncidentDate,
	ir.NumberOfIncidents,
	ROW_NUMBER() OVER (ORDER BY ir.NumberOfIncidents DESC) AS rownum,
	RANK() OVER (ORDER BY ir.NumberOfIncidents DESC) AS rk,
	DENSE_RANK() OVER (ORDER BY ir.NumberOfIncidents DESC) AS dr
FROM dbo.IncidentRollup ir
WHERE
	ir.IncidentTypeID = 3
	AND ir.NumberOfIncidents >= 8
ORDER BY
	ir.NumberOfIncidents DESC;

IncidentDate	NumberOfIncidents	rownum	rk	dr
2020-06-26		12					1		1	1
2020-04-20		11					2		2	2
2020-05-24		10					3		3	3
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
</> Aggregate window functions
Fill in the correct aggregate function for each column in the result set.

SELECT
	ir.IncidentDate,
	ir.NumberOfIncidents,
	SUM(ir.NumberOfIncidents) OVER () AS SumOfIncidents,
	MIN(ir.NumberOfIncidents) OVER () AS LowestNumberOfIncidents,
	MAX(ir.NumberOfIncidents) OVER () AS HighestNumberOfIncidents,
	COUNT(ir.NumberOfIncidents) OVER () AS CountOfIncidents
FROM dbo.IncidentRollup ir
WHERE
	ir.IncidentDate BETWEEN '2019-07-01' AND '2019-07-31'
AND ir.IncidentTypeID = 3;

IncidentDate	NumberOfIncidents	SumOfIncidents	LowestNumberOfIncidents	HighestNumberOfIncidents	CountOfIncidents
2019-07-02		3					62				1						10							15
2019-07-05		10					62				1						10							15
2019-07-07		6					62				1						10							15
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
</> Running totals with SUM()
Fill in the correct window function.

Fill in the PARTITION BY clause in the window function, partitioning by incident type ID.

Fill in the ORDER BY clause in the window function, ordering by incident date (in its default, ascending order).

SELECT
	ir.IncidentDate,
	ir.IncidentTypeID,
	ir.NumberOfIncidents,
	SUM(ir.NumberOfIncidents) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	) AS NumberOfIncidents
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	c.CalendarYear = 2019
	AND c.CalendarMonth = 7
	AND ir.IncidentTypeID IN (1, 2)
ORDER BY
	ir.IncidentTypeID,
	ir.IncidentDate;

IncidentDate	IncidentTypeID	NumberOfIncidents	NumberOfIncidents
2019-07-01		1				2					2
2019-07-02		1				9					11
2019-07-03		1				3					14
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
19
20
21
22
23
24
</> Investigating window frames
Running Total

(Empty - - no frame written out)

RANGE BETWEEN
UNBOUNDED PRECEDING
AND CURRENT NOW

Sum Across the Entire Range

RANGE BETWEEN
UNBOUNDED PRECEDING
AND UNBOUNDED
FOLLOWING

Row-Based Frames

ROWS BETWEEN 3
PRECEDING AND CURRENT
ROW

ROWS BETWEEN 2
PRECEDING AND 2
FOLLOWING

</> Calculating moving averages
Fill in the correct window function to perform a moving average starting from 6 days ago through today.

Fill in the window frame, including the ROWS clause, window frame preceding, and window frame following.

SELECT
	ir.IncidentDate,
	ir.IncidentTypeID,
	ir.NumberOfIncidents,
	AVG(ir.NumberOfIncidents) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
		ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
	) AS MeanNumberOfIncidents
FROM dbo.IncidentRollup ir
	INNER JOIN dbo.Calendar c
		ON ir.IncidentDate = c.Date
WHERE
	c.CalendarYear = 2019
	AND c.CalendarMonth IN (7, 8)
	AND ir.IncidentTypeID = 1
ORDER BY
	ir.IncidentTypeID,
	ir.IncidentDate;

IncidentDate	IncidentTypeID	NumberOfIncidents	MeanNumberOfIncidents
2019-07-01		1				2					2
2019-07-02		1				9					5
2019-07-03		1				3					4
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
19
20
21
22
23
24
25
</> Seeing prior and future periods
Fill in the window function to return the prior day’s number of incidents, partitioned by incident type ID and ordered by the incident date.

Fill in the window function to return the next day’s number of incidents, partitioned by incident type ID and ordered by the incident date.

SELECT
	ir.IncidentDate,
	ir.IncidentTypeID,
    -- Get the prior day's number of incidents
	LAG(ir.NumberOfIncidents, 1) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	) AS PriorDayIncidents,
	ir.NumberOfIncidents AS CurrentDayIncidents,
    -- Get the next day's number of incidents
	LEAD(ir.NumberOfIncidents, 1) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	) AS NextDayIncidents
FROM dbo.IncidentRollup ir
WHERE
	ir.IncidentDate >= '2019-07-02'
	AND ir.IncidentDate <= '2019-07-31'
	AND ir.IncidentTypeID IN (1, 2)
ORDER BY
	ir.IncidentTypeID,
	ir.IncidentDate;

IncidentDate	IncidentTypeID	PriorDayIncidents	CurrentDayIncidents	NextDayIncidents
2019-07-02		1				null				9					3
2019-07-03		1				9					3					6
2019-07-04		1				3					6					7
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
19
20
21
22
23
24
25
26
27
28
</> Seeing the prior three periods
Fill in the SQL to return the number of incidents from two periods ago.

Fill in the SQL to return the number of incidents from the prior period.

Fill in the SQL to return the number of incidents from the next period.

SELECT
	ir.IncidentDate,
	ir.IncidentTypeID,
    -- Fill in two periods ago
	LAG(ir.NumberOfIncidents, 2) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	) AS Trailing2Day,
    -- Fill in one period ago
	LAG(ir.NumberOfIncidents, 1) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	) AS Trailing1Day,
	ir.NumberOfIncidents AS CurrentDayIncidents,
    -- Fill in next period
	LEAD(ir.NumberOfIncidents, 1) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	) AS NextDay
FROM dbo.IncidentRollup ir
WHERE
	ir.IncidentDate >= '2019-07-01'
	AND ir.IncidentDate <= '2019-07-31'
	AND ir.IncidentTypeID IN (1, 2)
ORDER BY
	ir.IncidentTypeID,
	ir.IncidentDate;

IncidentDate	IncidentTypeID	Trailing2Day	Trailing1Day	CurrentDayIncidents	NextDay
2019-07-01		1				null			null			2					9
2019-07-02		1				null			2				9					3
2019-07-03		1				2				9				3					6
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
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
</> Calculating days elapsed between incidents
Calculate the days since the last incident using a combination of DATEDIFF() and LAG() or LEAD().

Calculate the days until the next incident using a combination of DATEDIFF() and LAG() or LEAD().

NOTE: you will not need to use the NumberOfIncidents column in this exercise.

SELECT
	ir.IncidentDate,
	ir.IncidentTypeID,
    -- Fill in the days since last incident
	DATEDIFF(DAY, LAG(ir.IncidentDate, 1) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	), ir.IncidentDate) AS DaysSinceLastIncident,
    -- Fill in the days until next incident
	DATEDIFF(DAY, ir.IncidentDate, LEAD(ir.IncidentDate, 1) OVER (
		PARTITION BY ir.IncidentTypeID
		ORDER BY ir.IncidentDate
	)) AS DaysUntilNextIncident
FROM dbo.IncidentRollup ir
WHERE
	ir.IncidentDate >= '2019-07-02'
	AND ir.IncidentDate <= '2019-07-31'
	AND ir.IncidentTypeID IN (1, 2)
ORDER BY
	ir.IncidentTypeID,
	ir.IncidentDate;

IncidentDate	IncidentTypeID	DaysSinceLastIncident	DaysUntilNextIncident
2019-07-02		1				null					1
2019-07-03		1				1						1
2019-07-04		1				1						2
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
19
20
21
22
23
24
25
26
27
</> Analyze client data for potential fraud
Split out start events and end events.

Fill in the customer’s visit start date (dsv.CustomerVisitStart) as TimeUTC in the “entrances” part of the query.
Fill in the window function that we alias as StartStopPoints to give us the stream of check-ins for each customer, ordered by their visit start date.
Fill in the customer’s visit end date (dsv.CustomerVisitEnd) as TimeUTC in the “departures” part of the query.
SELECT
	dsv.CustomerID,
	dsv.CustomerVisitStart AS TimeUTC,
	1 AS EntryCount,
	ROW_NUMBER() OVER (
      PARTITION BY dsv.CustomerID
      ORDER BY dsv.CustomerVisitStart
    ) AS StartOrdinal
FROM dbo.DaySpaVisit dsv
UNION ALL
SELECT
	dsv.CustomerID,
	dsv.CustomerVisitEnd AS TimeUTC,
	-1 AS EntryCount,
	NULL AS StartOrdinal
FROM dbo.DaySpaVisit dsv;

CustomerID	TimeUTC				EntryCount	StartOrdinal
1			2018-12-18 15:00:00	1			1
1			2018-12-18 17:50:00	1			2
1			2018-12-19 02:34:00	1			3
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
19
20
21
22
</> Build a stream of events
Fill out the appropriate window function (ROW_NUMBER()) to create a stream of check-ins and check-outs in chronological order.

Partition by the customer ID to calculate a result per user.

Order by the event time and solve ties by using the start ordinal value.

SELECT s.*,
	ROW_NUMBER() OVER (
      PARTITION BY s.CustomerID
      ORDER BY s.TimeUTC, s.StartOrdinal
    ) AS StartOrEndOrdinal
FROM #StartStopPoints s;

CustomerID	TimeUTC					EntryCount	StartOrdinal	StartOrEndOrdinal
1			2018-12-18 15:00:00		1			1				1
1			2018-12-18 15:55:00		-1			null			2
1			2018-12-18 17:50:00		1			2				3
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
</> Complete the fraud analysis
Fill out the HAVING clause to determine cases with more than 2 concurrent visitors.

Fill out the ORDER BY clause to show management the worst offenders: those with the highest values for MaxConcurrentCustomerVisits.

SELECT
	s.CustomerID,
	MAX(2 * s.StartOrdinal - s.StartOrEndOrdinal) AS MaxConcurrentCustomerVisits
FROM #StartStopOrder s
WHERE s.EntryCount = 1
GROUP BY s.CustomerID
HAVING MAX(2 * s.StartOrdinal - s.StartOrEndOrdinal) > 2
ORDER BY MaxConcurrentCustomerVisits DESC;

CustomerID	MaxConcurrentCustomerVisits
79			4
19			3
25			3
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
Based on your analysis in the prior exercise, what is the extent of customer misuse of the guest pass policy?

 No customers used their guest passes at all: the maximum concurrency for any customer was 1.
 Customers used guest passes but nobody violated the policy: the maximum concurrency for any customer was 2.
 Some customers violated the policy in a minor way: the maximum concurrency for any customer was 3.
 Some customers violated the policy: the maximum concurrency for any customer was 4.
 Some customers were egregious in policy violations: the maximum concurrency for any customer was 5.
————————————————
版权声明：本文为CSDN博主「Daisy Lee」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_42871941/article/details/107295648

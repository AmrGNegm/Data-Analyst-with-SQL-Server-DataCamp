https://blog.csdn.net/weixin_42871941/article/details/107642687

1. Recursion and Common Table Expression (CTE)
</> A CTE is … Find the wrong fact!
Please find the statement that is NOT true about CTEs.

 A CTE could be applied to recursive and non-recursive functions.
 A CTE is a temporary result that could be referenced by SELECT, INSERT, UPDATE, or DELETE statements.
 A CTE never returns a result set.
 A CTE could be used for substituting a view.
</> A CTE for IT-positions
Create the CTE ITjobs.

Define the fields of the CTE as ID, Name, and Position.

Find the positions starting with IT and the name starting with A.

WITH ITjobs (ID, Name, Position) AS (
    SELECT 
  		ID, 
  		Name,
  		Position
    FROM employee
  	WHERE Position LIKE 'IT%' AND Name LIKE 'A%')
    
SELECT * 
FROM ITjobs;

ID	Name			Position
2	Andreas Sitter	IT Security Manager
10	Andrea Sternig	IT Architecture Manager
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
</> A CTE for high-paid IT-positions
Define the second CTE, ITSalary, with the fields ID and Salary.

Find salaries above 3000.

Combine the two CTEs by using a JOIN of matching IDs and select the name, the salary, and the position of the selected employees.

WITH ITjobs (ID, Name, Position) AS (
    SELECT 
  		ID, 
  		Name,
  		Position
    FROM employee
    WHERE Position like 'IT%'),
    
ITSalary (ID, Salary) AS (
    SELECT
        ID,
        Salary
    FROM Salary
    WHERE Salary > 3000)
    
SELECT 
	ITjobs.NAME,
	ITjobs.POSITION,
    ITsalary.Salary
FROM ITjobs
    INNER JOIN ITsalary
    ON ITjobs.ID = ITsalary.ID;

NAME			POSITION				Salary
Heinz Griesser	IT Director				6000
Andreas Sitter	IT Security Manager		3500
Thomas Bergmann	IT Innovation Manager	3250
Andrea Sternig	IT Architecture Manager	3500
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
</> Facts about recursion.
In this exercise, your job is to find the wrong fact about recursion.

 Complex functions can be simplified with recursion.
 Recursively defined functions cannot be solved iteratively.
 A termination condition is always necessary.
 The execution time can be slow because a recursion function is called many times.
</> Calculate the factorial of 5
Set the @target factorial, which will also serve as the termination condition, to 5.

Initialize the @factorial result.

Calculate the @factorial number by taking the product of the factorial result so far and the current iteration.

Reduce the termination condition by 1 at the end of the iteration.

DECLARE @target float = 5
DECLARE @factorial float = 1

WHILE @target > 0 
BEGIN
	SET @factorial = @factorial * @target
	SET @target = @target - 1
END

SELECT @factorial;

120
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
</> How to query the factorial of 6 recursively
Initialize the fields factorial and step to 1.

Calculate the recursive part with factorial * (step + 1).

Stop the recursion process when the current iteration value is smaller than the target factorial number.

WITH calculate_factorial AS (
	SELECT    
      	1 AS step,
        1 AS factorial
	UNION ALL
	SELECT 
	 	step + 1,
	    factorial * (step + 1)
	FROM calculate_factorial        
	WHERE step < 6)
    
SELECT factorial 
FROM calculate_factorial;

factorial
1
2
6
24
120
720
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
</> Counting numbers recursively
Limit the recursion step to 50 in the recursive query.

Define the CTE with the name counting_numbers.

Initialize number in the anchor query.

Add 1 to number each recursion step.

WITH counting_numbers AS ( 
	SELECT 
  		1 AS number
  	UNION ALL 
  	SELECT 
  		number + 1 
  	FROM counting_numbers
  	WHERE number < 50)

SELECT number
FROM counting_numbers;

number
1
2
...
50
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
</> Calculate the sum of potencies
Define the CTE calculate_potencies with the fields step and result.

Initialize step and result.

Add the next step to the POWER() step + 1 to result.

WITH calculate_potencies (step, result) AS (
    SELECT 
  		1 AS step,
  		1 AS result
    UNION ALL
    SELECT 
  		step + 1,
  		result + POWER(step + 1, step + 1)
    FROM calculate_potencies
    WHERE step < 9)
    
SELECT 
	step, 
    result
FROM calculate_potencies;

step	result
1	1
2	5
...
9	405071317
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
2. Hierarchical and Recursive Queries
</> Right or wrong?
Which of the statements below is true?

 A recursive CTE is used to create iterative queries.
 Using a CTE call can improve readability.
 The items GROUP BY, SELECT DISTINCT, and HAVING are allowed in a recursive query.
 A recursive CTE needs an anchor query and a recursive query.
 It is not possible to self-reference a table using a CTE.
</> Create the alphabet recursively
Initialize number_of_letter to the number representing the letter A.

Increase the value of number_of_letter by 1 in each step and set the limit to 90, the value of Z.

Select the recursive member from the defined CTE.

WITH alphabet AS (
	SELECT 
	    65 AS number_of_letter
  	UNION ALL
	SELECT 
	    number_of_letter + 1
	FROM alphabet
  	WHERE number_of_letter < 90)
    
SELECT char(number_of_letter)
FROM alphabet;

A
B
...
Z
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
</> Create a time series of a year
To get a series of days for a year you need 365 recursion steps. Therefore, increase the number of iterations by OPTION (MAXRECURSION n) where n represents the number of iterations.

Initialize the current time as time.

Select the CTE recursively and combine the anchor and recursive member with the correct statement.

Limit the number of iterations to days in a year minus 1

Increase the maximum number of iterations to the number of days in a year with OPTION (MAXRECURSION n)

WITH time_series AS (
	SELECT 
	    GETDATE() AS time
  	UNION ALL
	SELECT 
	    DATEADD(day, 1, time)
	FROM time_series
  	WHERE time < GETDATE() + 364)
    
SELECT time
FROM time_series
OPTION(MAXRECURSION 365)

time
2020-07-28 11:57:25.187000
2020-07-29 11:57:25.187000
2020-07-30 11:57:25.187000
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
</> Who is your manager?
Create a CTE with the name employee_hierarchy.

Select the information of the IT director as the initial step of the CTE by filtering on his Supervisor ID.

Perform a join with employee to get the name of the manager.

WITH employee_hierarchy AS (
	SELECT
		ID, 
  		NAME,
  		Supervisor
	FROM employee
	WHERE Supervisor = 0
	UNION ALL
	SELECT 
  		emp.ID,
  		emp.NAME,
  		emp.Supervisor
	FROM employee emp
  		JOIN employee_hierarchy
  		ON emp.Supervisor = employee_hierarchy.ID)
    
SELECT 
    cte.Name as EmployeeName,
    emp.Name as ManagerName
FROM employee_hierarchy as cte
	JOIN employee as emp
	ON cte.Supervisor = emp.ID;

EmployeeName	ManagerName
Andreas Sitter	Heinz Griesser
Thomas Bergmann	Heinz Griesser
Hannes Berg		Heinz Griesser
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
</> Get the hierarchy position
Initialize the field LEVEL to 1 at the start of the recursion.

Select the information of the IT director as the initial step of the CTE by filtering on Supervisor.

Set LEVEL to the current recursion step.

Perform a JOIN with the defined CTE on the IDs of the supervisor and the employee.

WITH employee_hierarchy AS (
	SELECT
		ID, 
  		NAME,
  		Supervisor,
  		1 as LEVEL
	FROM employee
	WHERE Supervisor = 0
	UNION ALL
	SELECT 
  		emp.ID,
  		emp.NAME,
  		emp.Supervisor,
  		LEVEL + 1
	FROM employee emp
		JOIN employee_hierarchy
  		ON emp.Supervisor = employee_hierarchy.ID)
    
SELECT 
	cte.Name, cte.Level,
    emp.Name as ManagerID
FROM employee_hierarchy as cte
	JOIN employee as emp
	ON cte.Supervisor = emp.ID 
ORDER BY Level;

Name			Level	ManagerID
Andreas Sitter	2		Heinz Griesser
Thomas Bergmann	2		Heinz Griesser
Hannes Berg		2		Heinz Griesser
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
</> Which supervisor do I have?
Initialize Path to the ID of the supervisor (0) and the start condition of the recursion.

UNION the anchor member and define the recursive member fields (ID, Name, Supervisor).

Add the the ID of the supervisor Supervisor to the Path in each step.

Select the IDs of employees Christian Feierabend and Jasmin Mentil in the CTE.

WITH employee_Hierarchy AS (
	SELECT
		ID, 
  		NAME,
  		Supervisor,
  		CAST('0' AS VARCHAR(MAX)) as Path
	FROM employee
	WHERE Supervisor = 0
  	UNION ALL
	SELECT 
  		emp.ID,
  		emp.NAME,
  		emp.Supervisor,
        Path + '->' + CAST(emp.Supervisor AS VARCHAR(MAX))
	FROM employee emp
		INNER JOIN employee_Hierarchy
  		ON emp.Supervisor = employee_Hierarchy.ID
)

SELECT Path
FROM employee_Hierarchy
WHERE ID = 16 OR ID = 18;

Path
0->1->7
0->1->4
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
</> Get the number of generations?
Initialize the recursion start by setting the ParentID to 101.

Set LEVEL to the current recursion step.

Join the anchor member to the CTE on the ID of the parent and the child.

COUNT() the number of generations.

WITH children AS (
    SELECT 
  		ID, 
  		Name,
  		ParentID,
  		0 as LEVEL
  	FROM family 
  	WHERE ParentID = 101
    UNION ALL
    SELECT 
  		child.ID,
  		child.NAME,
  		child.ParentID,
  		LEVEL + 1
  	FROM family child
  		INNER JOIN children 
  		ON child.ParentID = children.ID)
    
SELECT
	COUNT(DISTINCT LEVEL) as Generations
FROM children
OPTION(MAXRECURSION 300);

Generations
199
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
</> Get all possible parents in one field?
Set the ParentID of 290 as starting point.

If Parent.ID = ‘’ in the CASE operation, the Parent field needs to be set to the current ParentID.

If Parent.ID <> ‘’ in the CASE operation, the Parent ID needs to be added to the current Parent for each iteration.

Select the Name and Parent from the defined CTE.

WITH tree AS (
	SELECT 
  		ID,
  		Name, 
  		ParentID, 
  		CAST('0' AS VARCHAR(MAX)) as Parent
	FROM family
  	WHERE ParentID = 290    
    UNION ALL
    SELECT 
  		Next.ID, 
  		Next.Name, 
  		Parent.ID,
    	CAST(CASE WHEN Parent.ID = ''
                  THEN(CAST(Next.ParentID AS VARCHAR(MAX)))
             ELSE(Parent.Parent + ' -> ' + CAST(Next.ParentID AS VARCHAR(MAX)))
    		 END AS VARCHAR(MAX))
        FROM family AS Next
        	INNER JOIN tree AS Parent 
  			ON Next.ParentID = Parent.ID)
        
SELECT Name, Parent
FROM tree;

Name				Parent
Otoniel Tsosie		0
Danica Contreras	0 -> 291
Matias Clingan		0 -> 291 -> 292
Ignacio Galvan		0 -> 291 -> 292 -> 293
Konda Rea			0 -> 291 -> 292 -> 293 -> 294
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
3. Creating Data Models on Your Own
</> Creating a table
Define the table Person.

Define a field IndividualID.

Set Firstname and Lastname not to be NULL and of type VARCHAR(255).

Define Birthday as DATE.

CREATE TABLE Person (
  	IndividualID INT NOT NULL,
	Firstname VARCHAR(255) NOT NULL,
	Lastname VARCHAR(255) NOT NULL,
	Address VARCHAR(255) NOT NULL,
  	City CHAR(32) NOT NULL,
  	Birthday DATE
);

SELECT * 
FROM Person;
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
</> Inserting and updating a table
Insert the new values for ID = 1 into the Person table.

Insert the values Peter, Jackson, 342 Flushing st, New York, and 1986-12-30 in the corresponding fields of the table.

INSERT INTO Person 
VALUES ('1','Andrew','Anderson','Union Ave 10','New York','1986-12-30');
INSERT INTO Person 
VALUES ('2','Peter','Jackson','342 Flushing st','New York','1986-12-30');

SELECT * 
FROM Person;

ID	Firstname	Lastname	Adress				City				Birthday
1	Andrew		Anderson	Union Ave 10		New York            1986-12-30
2	Peter		Jackson		342 Flushing st		New York            1986-12-30
3	Jessica	 	Patterson	Pearson Ave 264	 	San Fransciso       1987-02-03
4	Michael	 	Knight	 	Center Ave 2	 	Torronte            1990-07-11
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
Change the first name of the person whose ID = 1 to Jones.

Update the birthday of the person with the last name Jackson to 1980-01-05.

INSERT INTO Person 
VALUES ('1','Andrew','Anderson','Union Ave 10','New York','1986-12-30');
INSERT INTO Person 
VALUES ('2','Peter','Jackson','342 Flushing st','New York','1986-12-30');

UPDATE Person
SET Firstname = 'Jones'
WHERE ID = 1;
UPDATE Person
SET Birthday = '1980-01-05'
WHERE Lastname = 'Jackson';

SELECT * 
FROM Person;

ID	Firstname	Lastname	Adress				City				Birthday
1	Jones		Anderson	Union Ave 10		New York            1986-12-30
2	Peter		Jackson		342 Flushing st		New York            1980-01-05
3	Jessica		Patterson	Pearson Ave 264	 	San Fransciso       1987-02-03
4	Michael	 	Knight	 	Center Ave 2	 	Torronte            1990-07-11
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
</> Deleting data and dropping table
Delete the person with an ID equal to 1 in the table.

Delete the person whose last name is Jackson.

INSERT INTO Person 
VALUES ('1','Andrew','Anderson','Adress 1','City 1','1986-12-30');
INSERT INTO Person 
VALUES ('2','Peter','Jackson','Adress 2','City 2','1986-12-30');
INSERT INTO Person 
VALUES ('5','Michaela','James','Adress 3','City 3','1976-03-07');

DELETE FROM Person 
WHERE ID = '1';
DELETE FROM Person 
WHERE Lastname = 'Jackson';

SELECT * 
FROM Person;

ID	Firstname	Lastname	Adress				City				Birthday
3	Jessica		Patterson	Pearson Ave 264	 	San Fransciso       1987-02-03
4	Michael	 	Knight	 	Center Ave 2	 	Torronte            1990-07-11
5	Michaela	James		Adress 3			City 3              1976-03-07
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
Delete the table Person.

INSERT INTO Person 
VALUES ( '1', 'Andrew', 'Anderson', 'Address 1', 'City 1', '1986-12-30');
INSERT INTO Person 
VALUES ( '2', 'Peter', 'Jackson', 'Address 2', 'City 2', '1986-12-30');
INSERT INTO Person 
VALUES ( '3', 'Michaela', 'James', 'Address 3', 'City 3', '1976-03-07');

DELETE FROM Person 
WHERE ID = 1;
DELETE FROM Person 
WHERE Lastname = 'Jackson';

DROP TABLE Person;

SELECT * 
FROM Person;
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
</> Changing a table structure
Add the column Email to Person.

Delete the column Birthday from Person.

ALTER TABLE Person
ADD Email VARCHAR(255);
ALTER TABLE Person
DROP COLUMN Birthday;

SELECT * 
FROM Person;

ID	Firstname	Lastname	Adress				City				Email
3	Jessica	 	Patterson	Pearson Ave 264	 	San Fransciso       null
4	Michael	 	Knight		Center Ave 2	 	Torronte            null
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
</> Defining primary and foreign keys
Define the primary key PersonID for Person of type INT.

Define the primary key OrderID for History.

Define the foreign key PersonID referencing the primary key of Person.

CREATE TABLE Person (
  	PersonID INT NOT NULL PRIMARY KEY,
	Firstname VARCHAR(255) NOT NULL,
	Lastname VARCHAR(255) NOT NULL,
);

CREATE TABLE History (   
  	OrderID INT NOT NULL PRIMARY KEY,
    Item VARCHAR(255) NOT NULL,
    Price INT NOT NULL,
    PersonID INT FOREIGN KEY REFERENCES Person(PersonID)    
);

SELECT * 
FROM History;
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
</> Inserting data to person and order history
Insert new data for the person with ID=1, Andrew Anderson.

Insert new data for the second person with ID=2, Sam Smith.

Insert a new order for Andrew Anderson: iPhone XS for 1000.

Insert a new order for Sam Smith: MacBook Pro for 1800.

INSERT INTO Person 
VALUES ('1','Andrew','Anderson','Union Ave 10','New York','1986-12-30');
INSERT INTO Person 
VALUES ('2','Sam','Smith','Flushing Ave 342','New York','1986-12-30');
INSERT INTO History 
VALUES ('1','IPhone XS','1000','1');
INSERT INTO History 
VALUES ('2','MacBook Pro','1800','2');

SELECT * 
FROM History;

OrderID	Item			Price	PersonID
1		IPhone XS		1000	1
2		MacBook Pro		1800	2
3		Samsung TV		2000	3
4		Bose Soundbar	600		3
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
</> Getting the number of orders & total costs
COUNT() the number of orders and alias it as Orders.

SUM() the total price of all orders and alias it as Costs.

Join the Person and History tables.

Aggregate the information on ID using GROUP BY.

INSERT INTO Person  
VALUES ('1', 'Andrew', 'Anderson','Union Ave 10','New York','1986-12-30');
INSERT INTO Person 
VALUES ('2', 'Sam', 'Smith','Flushing Ave 342','New York','1986-12-30');
INSERT INTO History VALUES ( '1', 'IPhone XS', '1000', '1');
INSERT INTO History VALUES ( '2', 'MacBook Pro', '1800', '1');
INSERT INTO History VALUES ( '5', 'IPhone XR', '600', '2');
INSERT INTO History VALUES ( '6', 'IWatch 4', '400', '1');

SELECT 
    Person.ID,
    COUNT(OrderID) as Orders,
    SUM(Price) as Costs
FROM Person
	JOIN History 
	ON Person.ID = History.PersonID
GROUP BY Person.ID;

ID	Orders	Costs
1	3		3200
2	1		600
3	2		2600
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
</> Creating a hierarchical data model
Define the fields ID and ParentID of type INT. ID should not be NULL, ParentID can be NULL.

Insert the equipment Software into the table with the correct IDs. The software is part of Asset.

Insert the equipment Monitor into the table with the correct IDs. The monitor is part of Hardware.

Insert the equipment Microsoft Office into the table with the correct IDs. This software is part of Application.

CREATE TABLE Equipment (   
	ID INT NOT NULL,
    Equipment VARCHAR(255) NOT NULL,
    ParentID INT 
);

INSERT INTO Equipment VALUES ('1','Asset',NULL);
INSERT INTO Equipment VALUES ('2','Hardware','1');
INSERT INTO Equipment VALUES ('3','Software','1');
INSERT INTO Equipment VALUES ('4','Application','3');
INSERT INTO Equipment VALUES ('5','Tool','3');
INSERT INTO Equipment VALUES ('6','PC','2');
INSERT INTO Equipment VALUES ('7','Monitor','2');
INSERT INTO Equipment VALUES ('8','Phone','2');
INSERT INTO Equipment VALUES ('9','IPhone','8');
INSERT INTO Equipment VALUES ('10','Microsoft Office','4');

SELECT * 
FROM Equipment;

ID	Equipment	ParentID
1	Asset		null
2	Hardware	1
3	Software	1
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
</> Networked and hierarchical models
Which statement is not true about networked and hierarchical data models?

 In hierarchical data models each child record needs to have only one parent record.
 In networked data models each child record can have only one parent record.
 The hierarchical data model can be represented by a tree.
 A networked data model has at least one parent record.
</> Creating a networked data model
Define the fields Departure and Destination, neither of which can be NULL.

Insert the route from San Francisco to New York for Bus 1.

Insert the route from Florida to San Francisco for Bus 9.

Select all possible departure locations.

CREATE TABLE Trip (   
  	Departure VARCHAR(255) NOT NULL,
    BusName VARCHAR(255) NOT NULL,
    Destination VARCHAR(255) NOT NULL
);

INSERT INTO Trip VALUES ( 'San Francisco' , 'Bus 1' , 'New York');
INSERT INTO Trip VALUES ( 'Florida', 'Bus 9' , 'San Francisco');
INSERT INTO Trip VALUES ( 'San Francisco', 'Bus 2','Texas');
INSERT INTO Trip VALUES ( 'San Francisco', 'Bus 3','Florida');
INSERT INTO Trip VALUES ( 'San Francisco', 'Bus 4','Washington');
INSERT INTO Trip VALUES ( 'New York', 'Bus 5','Texas');
INSERT INTO Trip VALUES ( 'New York', 'Bus 6','Washington');
INSERT INTO Trip VALUES ( 'Florida', 'Bus 7','New York');
INSERT INTO Trip VALUES ( 'Florida', 'Bus 8','Toronto');

SELECT DISTINCT Departure 
FROM Trip;

Departure
Florida
New York
San Francisco
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
4. Hierarchical Queries of Real-World Examples
</> Get the anchor & recursive member
Please find the correct combination of fields to create a flying route.

 Flight number and departure airport
 Destination airport and flight number
 Flight costs and departure airport
 Departure airport and the destination airport
</> Get all possible airports
Define the CTE table possible_Airports with the field Airports.

Select the airports by combining Departure and Arrival airports.

Combine the departure with the destination airports using the correct statement.

Select all possible Airports from possible_Airports.

WITH possible_Airports (Airports) AS(
  	SELECT Departure AS Airports
  	FROM flightPlan
  	UNION 
  	SELECT Arrival AS Airports
  	FROM flightPlan)

SELECT DISTINCT Airports
FROM possible_Airports;

Airports
Chicago
Franfurt
Frankfurt
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
</> All flight routes from Vienna
Initialize the number of stops, increment it in the recursive query, and limit it to less than 5.

WITH flight_route (Departure, Arrival, stops) AS(
  	SELECT 
  	  	f.Departure, f.Arrival,
  	  	0
  	FROM flightPlan f
  	WHERE Departure = 'Vienna'
  	UNION ALL
  	SELECT 
  	  	p.Departure, f.Arrival,
  	  	p.stops + 1
  	FROM flightPlan f, flight_route p
  	WHERE p.Arrival = f.Departure AND 
  	      p.stops < 5)

SELECT 
	DISTINCT Arrival, 
    Departure, 
    stops
FROM flight_route;

Arrival		Departure	stops
Chicago		Vienna		0
Chicago		Vienna		2
Chicago		Vienna		3
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
Define the field route describing the flight route (Departure to Arrival)

Track each recursion step with the Departure and Arrival airport.

WITH flight_route (Departure, Arrival, stops, route) AS(
	SELECT 
	  	f.Departure, f.Arrival, 
	  	0,
	  	CAST(f.Departure + ' -> ' + f.Arrival AS NVARCHAR(MAX))
	FROM flightPlan f
	WHERE Departure = 'Vienna'
	UNION ALL
	SELECT 
	  	p.Departure, f.Arrival, 
	  	p.stops + 1,
	  	p.route + ' -> ' + f.Arrival
	FROM flightPlan f, flight_route p
	WHERE p.Arrival = f.Departure AND 
	      p.stops < 5)

SELECT 
	DISTINCT Arrival, 
    Departure, 
    route, 
    stops
FROM flight_route;

Arrival	Departure	route																		stops
Chicago	Vienna		Vienna -> Chicago															0
Chicago	Vienna		Vienna -> New York -> Paris -> Frankfurt -> New York -> Vienna -> Chicago	5
Chicago	Vienna		Vienna -> New York -> Paris -> Frankfurt -> Paris -> Vienna -> Chicago		5
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
Add a totalCost field to the CTE and define it with the flight cost of the first flight.

Add the cost for each layover to the total costs.

Output the destinations where the totalCost is less than 500.

WITH flight_route (Departure, Arrival, stops, totalCost, route) AS(
	SELECT 
	  	f.Departure, f.Arrival, 
	  	0,
	  	f.Cost,
	  	CAST(Departure + ' -> ' + Arrival AS NVARCHAR(MAX))
	FROM flightPlan f
	WHERE Departure = 'Vienna'
	UNION ALL
	SELECT 
	  	p.Departure, f.Arrival, 
	  	p.stops + 1,
	  	p.totalCost + f.Cost,
	  	p.route + ' -> ' + f.Arrival
	FROM flightPlan f, flight_route p
	WHERE p.Arrival = f.Departure AND 
	      p.stops < 5)

SELECT 
	DISTINCT Arrival, 
    totalCost
FROM flight_route
WHERE totalCost < 500;

Arrival	totalCost
Chicago	391.23
New York	482.42
Paris	74.88
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
</> Create the parts list
Define PartID as PRIMARY KEY of type INT.

Define Cost of type INT and not to be NULL.

Insert the root element SUV as described in the context section.

Insert the entry Wheels as described in the context section.

CREATE TABLE Bill_Of_Material (
  	PartID INT NOT NULL PRIMARY KEY,
	SubPartID INT,
	Component VARCHAR(255) NOT NULL,
	Title  VARCHAR(255) NOT NULL,
	Vendor VARCHAR(255) NOT NULL,
  	ProductKey CHAR(32) NOT NULL,
  	Cost INT NOT NULL,
	Quantity INT NOT NULL);

INSERT INTO Bill_Of_Material
VALUES ('1',NULL,'SUV','BMW X1','BMW','F48',50000,1);
INSERT INTO Bill_Of_Material
VALUES ('2','1','Engine','V6BiTurbro','BMW','EV3891ASF',3000,1);
INSERT INTO Bill_Of_Material
VALUES ('3','1','Body','AL_Race_Body','BMW','BD39281PUO',5000,1);
INSERT INTO Bill_Of_Material
VALUES ('4','1','Interior Decoration','All_Leather_Brown','BMW','ZEU198292',2500,1);
INSERT INTO Bill_Of_Material
VALUES ('5','1','Wheels','M-Performance 19/255','BMW','MKQ134098URZ',400,4);

SELECT * 
FROM Bill_Of_Material;

PartID	SubPartID	Component			Title					Vendor	ProductKey			Cost	Quantity
1		null		SUV					BMW X1					BMW		F48                 50000	1
2		1			Engine				V6BiTurbro				BMW		EV3891ASF           3000	1
3		1			Body				AL_Race_Body			BMW		BD39281PUO          5000	1
4		1			Interior Decoration	All_Leather_Brown		BMW		ZEU198292           2500	1
5		1			Wheels				M-Performance 19/255	BMW		MKQ134098URZ        400		4
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
</> Create a car’s bill of material
Define construction_Plan with the fields: PartID, SubPartID, Title, Component and Level.

Initialize the field Level to 1.

Increase Level by 1 in every recursion step.

Limit the number of steps to Level = 2.

WITH construction_Plan (PartID, SubPartID, Title, Component, Level) AS (
	SELECT 
  		PartID,
  		SubPartID,
  		Title,
  		Component,
  		1
	FROM partList
	WHERE PartID = '1'
	UNION ALL
	SELECT 
		CHILD.PartID, 
  		CHILD.SubPartID,
  		CHILD.Title,
  		CHILD.Component,
  		PARENT.Level + 1
	FROM construction_Plan PARENT, partList CHILD
  	WHERE CHILD.SubPartID = PARENT.PartID
	  AND PARENT.Level < 2)

SELECT DISTINCT PartID, SubPartID, Title, Component, Level
FROM construction_Plan
ORDER BY PartID, SubPartID, Level;

PartID	SubPartID	Title					Component			Level
1		null		BMW X1	 				SUV					1
2		1		 	V6BiTurbro			 	Engine				2
3		1	 		AL_Race_Body	 		Body				2
4		1	 		All_Leather_Brown	 	Interior Decoration	2
5		1	 		M-Performance 19/255	Wheels				2
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
</> Build up a BMW?
Define construction_Plan with the necessary fields.

Initialize Total with the Quantity in the anchor element of CTE.

Increase Total with the Quantity of the child element in the recursion element.

Use SUM() to create the sum of Total on the aggregated information on IDs of the hierarchy.

WITH construction_Plan (PartID, SubPartID, Level, Component, Total) AS (
	SELECT 
  		PartID,SubPartID,
  		0,
  		Component,
  		Quantity
	FROM partList
	WHERE PartID = '1'
	UNION ALL
	SELECT 
		CHILD.PartID, CHILD.SubPartID,
  		PARENT.Level + 1,
  		CHILD.Component,
  		PARENT.Total + CHILD.Quantity
	FROM construction_Plan PARENT, partList CHILD
  	WHERE CHILD.SubPartID = PARENT.PartID
	  AND PARENT.Level < 3)
      
SELECT 
    PartID, SubPartID,Component,
    SUM(Total)
FROM construction_Plan
GROUP BY PartID, SubPartID, Component
ORDER BY PartID, SubPartID;

PartID	SubPartID	Component	
1		null	 	SUV			1
2		1	 		Engine		2
3		1	 		Body		2
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
</> Create a power grid
CREATE the structure table.

Define the EquipmentID field as a PRIMARY KEY of type INT.

Insert the record for line 1: 1, 2, , ‘HV’, ‘Cable’, 2000, 2016, 'good’

Insert the record for line 14: - 14, 15, 3, ‘MV’, ‘Cable’, 1976, 2002, 'bad’

CREATE TABLE structure (
  	EquipmentID INT NOT NULL PRIMARY KEY,
    EquipmentID_To INT ,
    EquipmentID_From INT, 
    VoltageLevel TEXT NOT NULL,
    Description TEXT NOT NULL,
    ConstructionYear INT NOT NULL,
    InspectionYear INT NOT NULL,
    ConditionAssessment TEXT NOT NULL
);

INSERT INTO structure
VALUES ( 1, 2, NULL, 'HV', 'Cable', 2000, 2016, 'good');
INSERT INTO Structure
VALUES ( 2, 3 , 1, 'HV', 'Overhead Line', 1968, 2016, 'bad');
INSERT INTO Structure
VALUES ( 3, 14, 2, 'HV', 'TRANSFORMER', 1972, 2001, 'good');
INSERT INTO Structure
VALUES ( 14, 15, 3 , 'MV', 'Cable', 1976, 2002, 'bad');

SELECT * 
FROM structure;

EquipmentID	EquipmentID_To	EquipmentID_From	VoltageLevel	Description		ConstructionYear	InspectionYear	ConditionAssessment
1			2				null				HV				Cable			2000				2016			good
2			3				1					HV				Overhead Line	1968				2016			bad
3			14				2					HV				TRANSFORMER		1972				2001			good
14			15				3					MV				Cable			1976				2002			bad
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
</> Get power lines to maintain
Define the CTE maintenance_List.

Start the evaluation for line 3.

Join GridStructure with maintenance_List on the corresponding endpoints.

Use LIKE to filter the power lines with ConditionAssessment of either exchange or repair, and a VoltageLevel of HV.

WITH maintenance_List (Line, Destination, Source, Description, ConditionAssessment, VoltageLevel) AS (
	SELECT 
  		EquipmentID,
  		EquipmentID_To,
  		EquipmentID_From,
  		Description,
  		ConditionAssessment,
  		VoltageLevel
  	FROM GridStructure
	WHERE EquipmentID = 3
	UNION ALL
	SELECT 
		Child.EquipmentID, 
  		Child.EquipmentID_To,
  		Child.EquipmentID_FROM,
  		Child.Description,
  		Child.ConditionAssessment,
  		Child.VoltageLevel
	FROM GridStructure Child
  		JOIN maintenance_List 
    	ON maintenance_List.Line = Child.EquipmentID_From)
SELECT Line, Description, ConditionAssessment 
FROM maintenance_List
WHERE 
    (ConditionAssessment LIKE '%exchange%' OR ConditionAssessment LIKE '%repair%') AND 
     VoltageLevel LIKE '%HV%'

Line	Description		ConditionAssessment
3		Cabel			repair
6		OverheadLine	exchange
8		OverheadLine	exchange
————————————————
版权声明：本文为CSDN博主「Daisy Lee」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_42871941/article/details/107642687

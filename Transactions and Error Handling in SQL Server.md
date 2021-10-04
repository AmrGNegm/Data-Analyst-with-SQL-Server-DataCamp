1. Starting with error handling


The TRY…CATCH syntax

BEGIN TRY
    INSERT INTO products (product_name, stock, price)
        VALUES ('Trek Powerfly 5 - 2018', 10, 3499.99);
    SELECT 'Product inserted correctly!';

    BEGIN CATCH
        SELECT 'An error occurred! You are in the CATCH block';   
    END CATCH
END TRY


Which of the following is true about the syntax?

 This script is correct because the error is handled within the CATCH block, and everything must be enclosed by the TRY block.
<b> This script isn’t correct because the CATCH block must start after the end of the TRY block. <\b> >> TRUE
 This script isn’t correct because the error should be handled in the TRY block.
 
 

Your first error-handling script
Surround the constraint with a TRY block.

Add the constraint to the products table.

Surround the error message with a CATCH block.

BEGIN TRY
	ALTER TABLE products
		ADD CONSTRAINT CHK_Stock CHECK (stock >= 0);
END TRY
BEGIN CATCH
	SELECT 'An error occurred!';
END CATCH

  
</> Nesting TRY…CATCH constructs
Surround the INSERT INTO buyers statement with a TRY block.

Surround the error handling with a CATCH block.

Surround the INSERT INTO errors statement with another TRY block.

Surround the nested error handling with another CATCH block.

BEGIN TRY
	INSERT INTO buyers (first_name, last_name, email, phone)
		VALUES ('Peter', 'Thompson', 'peterthomson@mail.com', '555000100');
END TRY
BEGIN CATCH
	SELECT 'An error occurred inserting the buyer! You are in the first CATCH block';
    BEGIN TRY
    	INSERT INTO errors 
        	VALUES ('Error inserting a buyer');
        SELECT 'Error inserted correctly!';
	END TRY
    BEGIN CATCH
    	SELECT 'An error occurred inserting the error! You are in the nested CATCH block';
    END CATCH 
END CATCH

  

Anatomy review
When you execute the following script:

INSERT INTO products (product_name, stock, price)
    VALUES ('Trek Powerfly 5 - 2018', 10, 3499.99);

  
  
The console of your SQL Server shows this:

Msg 2627, Level 14, State 1, Line 1
Violation of UNIQUE KEY constraint 'unique_name'. 
Cannot insert duplicate key in object 'dbo.products'. 
The duplicate key value is (Trek Powerfly 5 - 2018).

  
  
What are the different parts of the error you get, from left to right?

 Message level, severity level, state, line, and message text.
 Error number, line, state, severity level, and message text.
 Error number, severity level, state, line, and message text.

Correcting compilation errors
Note: Error messages in DataCamp have different anatomy than in SQL Server, but as they show the error message, you won’t have any problem.

Run the code to verify there are compilation errors.
Correct every compilation error.
Run the code to get the final output: An error occurred inserting the product!
START TRY
	INSERT INTO products (product_name, stock, price)
		VALUES ('Sun Bicycles ElectroLite - 2017', 10, 1559.99);
FINISH TRY
START CACH
	SELECT 'An error occurred inserting the product!';
    START TRY
    	INSERT INTO errors
        	VALUES ('Error inserting a product');
    FINISH TRY    
    BEGIN CACH
    	SELECT 'An error occurred inserting the error!';
    FINISH CACH    
FINISH CACH

('42000', "[42000] [Microsoft][ODBC Driver 17 for SQL Server][SQL Server]Incorrect syntax near 'FINISH'. (102) (SQLExecDirectW)")


BEGIN TRY
	INSERT INTO products (product_name, stock, price)
		VALUES ('Sun Bicycles ElectroLite - 2017', 10, 1559.99);
END TRY
BEGIN CATCH
	SELECT 'An error occurred inserting the product!';
    BEGIN TRY
    	INSERT INTO errors
        	VALUES ('Error inserting a product');
    END TRY    
    BEGIN CATCH
    	SELECT 'An error occurred inserting the error!';
    END CATCH    
END CATCH

An error occurred inserting the product!



Error function syntax
Which of the following is true about the functions ERROR_NUMBER(), ERROR_SEVERITY(), ERROR_STATE(), ERROR_PROCEDURE(), ERROR_LINE(), and ERROR_MESSAGE()?

 These functions must be placed within the TRY block, just after the statement which may cause an error. If an error occurs, they return information about the error.
 These functions must be placed within the CATCH block. If an error occurs within the TRY block, they return information about the error.
 These functions must be placed within the CATCH block. They will return NULL values if there are no errors.


Using error functions
Surround the operation with a TRY block.

Surround the functions with a CATCH block.

Select the error information.

BEGIN TRY  	
	SELECT 'Total: ' + SUM(price * quantity) AS total
	FROM orders  
END TRY
BEGIN CATCH  
	SELECT  ERROR_NUMBER() AS number,  
        	ERROR_SEVERITY() AS severity_level,  
        	ERROR_STATE() AS state,
        	ERROR_LINE() AS line,  
        	ERROR_MESSAGE() AS message; 	
END CATCH 

number	severity_level	state	line	message
8114	16				5		3		Error converting data type varchar to numeric.




Using error functions in a nested TRY…CATCH
Surround the error handling with a CATCH block.

Insert ‘Error inserting a product’ in the errors table and surround this insertion with another TRY block.

Surround the nested error handling with another CATCH block.

Select the error line and the error message in the inner CATCH block.

BEGIN TRY
    INSERT INTO products (product_name, stock, price) 
    VALUES	('Trek Powerfly 5 - 2018', 2, 3499.99),   		
    		('New Power K- 2018', 3, 1999.99)		
END TRY
BEGIN CATCH
	SELECT 'An error occurred inserting the product!';
    BEGIN TRY
    	INSERT INTO errors 
        	VALUES ('Error inserting a product');
    END TRY    
    BEGIN CATCH
    	SELECT 
        	ERROR_LINE() AS line,	   
			ERROR_MESSAGE() AS message; 
    END CATCH    
END CATCH

An error occurred inserting the product!



2. Raising, throwing and customizing your errors
</> RAISERROR syntax
Given this RAISERROR statement

RAISERROR(‘You cannot apply a 50%% discount on %s number %d’, 6, 1, ‘product’, 5);

Which of the following outputs will you get if you execute this code?

 “You cannot apply a 6% discount on 1 product number 5”
 “You cannot apply a 50% discount on product number 5”
 “You cannot apply a 50%% discount on product number 5”
 “You cannot apply a 50% discount on 5 number product”
</> CATCHING the RAISERROR
Set @product_id to 5.

Use the RAISERROR statement with a severity of 11, a state of 1 and the given @product_id.

DECLARE @product_id INT = 5;

IF NOT EXISTS (SELECT * FROM products WHERE product_id = @product_id)
	RAISERROR('No product with id %d.', 11, 1, @product_id);
ELSE 
	SELECT * FROM products WHERE product_id = @product_id;

product_id	product_name			stock	price
5			Trek CrossRip+ - 2018	12		4499.99


Catch the error generated by the RAISERROR statement you coded.

Select the error message using the appropriate function.

BEGIN TRY
    DECLARE @product_id INT = 5;
    IF NOT EXISTS (SELECT * FROM products WHERE product_id = @product_id)
        RAISERROR('No product with id %d.', 11, 1, @product_id);
    ELSE 
        SELECT * FROM products WHERE product_id = @product_id;
END TRY
BEGIN CATCH
	SELECT ERROR_MESSAGE();
END CATCH  

product_id	product_name			stock	price
5			Trek CrossRip+ - 2018	12		4499.99



</> THROW with or without parameters
Which of the following is true about the THROW statement?

 The THROW statement without parameters should be placed within a CATCH block.
 The THROW statement with parameters can only be placed within a CATCH block.
 The THROW statement without parameters can’t re-throw an original error.
</> THROW without parameters
Surround the error handling with a CATCH block.

Insert the error in the errors table.

End the insert statement with a semicolon (😉.

Re-throw the original error.

CREATE PROCEDURE insert_product
  @product_name VARCHAR(50),
  @stock INT,
  @price DECIMAL

AS

BEGIN TRY
	INSERT INTO products (product_name, stock, price)
		VALUES (@product_name, @stock, @price);
END TRY
BEGIN CATCH
    INSERT INTO errors VALUES ('Error inserting a product');
	THROW; 
END CATCH


</> Executing a stored procedure that throws an error
Execute the stored procedure called insert_product.

Set the appropriate values for the parameters of the stored procedure.

Surround the error handling with a CATCH block.

Select the error message.

BEGIN TRY
	EXEC insert_product
    	@product_name = 'Super bike',
        @stock = 10,
        @price = 4999.99;
END TRY
BEGIN CATCH
	SELECT ERROR_MESSAGE();
END CATCH



</> THROW with parameters
Set @staff_id to 4.

Use the THROW statement, with 50001 as the error number, ‘No staff member with such id’ as the message text, and 1 as the state.

DECLARE @staff_id INT = 4;

IF NOT EXISTS (SELECT * FROM staff WHERE staff_id = @staff_id)
	THROW 50001, 'No staff member with such id', 1;
ELSE
   	SELECT * FROM staff WHERE staff_id = @staff_id

staff_id	first_name	last_name	email					phone
4			Mateo		Casanovas	mateocasanovas@mail.com	555110996



</> Ways of customizing error messages
You want to use the THROW statement to throw an error with a custom message. Which of the following is a possible option to do so?

 You use the CONCATMESSAGE function and save the result into a variable that you pass to the THROW statement.
 You use the CONCATMESSAGE function and save the result into a variable that you pass to the THROW statement.
 You use the FORMATMESSAGE function inside the THROW statement.
</> Concatenating the message
Set the @first_name variable to ‘Pedro’.

Assign to the @my_message variable the concatenation of the text 'There is no staff member with ‘, with the value of the @first_name variable and with the text ’ as the first name.’.

Use the THROW statement with 50000 as the error number, @my_message variable as the message parameter, and 1 as the state.

DECLARE @first_name NVARCHAR(20) = 'Pedro';
DECLARE @my_message NVARCHAR(500) =
	CONCAT('There is no staff member with ', @first_name, ' as the first name.');
	
IF NOT EXISTS (SELECT * FROM staff WHERE first_name = @first_name)
	THROW 50000, @my_message, 1;



</> FORMATMESSAGE with message string
Save into the @current_stock variable the value of the stock of the product.

Use the FORMATMESSAGE function with parameter placeholders (%s, %d, … ) to customize the error message. The message has to be 'There are not enough (the given product name) bikes. You only have (the stock of the product) in stock.'

Pass to the THROW statement the variable of the custom message.

DECLARE @product_name AS NVARCHAR(50) = 'Trek CrossRip+ - 2018';
DECLARE @number_of_sold_bikes AS INT = 10;
DECLARE @current_stock INT;
SELECT @current_stock = stock FROM products WHERE product_name = @product_name;

DECLARE @my_message NVARCHAR(500) =
	FORMATMESSAGE('There are not enough %s bikes. You only have %d in stock.', @product_name, @current_stock);

IF (@current_stock - @number_of_sold_bikes < 0)
	THROW 50000, @my_message, 1;



</> FORMATMESSAGE with message number
Pass to the sp_addmessage stored procedure 50002 as the message id, 16 as the severity, and ‘There are not enough %s bikes. You only have %d in stock.’ as the message text.

Use FORMATMESSAGE, setting the first parameter (message number) to be 50002. Complete the second and the third parameters to replace the parameter placeholders of the message (%s and %d) with the appropriate variables.

Pass to the THROW statement the custom message.

EXEC sp_addmessage @msgnum = 50002, @severity = 16, @msgtext = 'There are not enough %s bikes. You only have %d in stock.', @lang = N'us_english';

DECLARE @product_name AS NVARCHAR(50) = 'Trek CrossRip+ - 2018';
DECLARE @number_of_sold_bikes AS INT = 10;
DECLARE @current_stock INT;
SELECT @current_stock = stock FROM products WHERE product_name = @product_name;
DECLARE @my_message NVARCHAR(500) =
	FORMATMESSAGE('There are not enough %s bikes. You only have %d in stock.', @product_name, @current_stock);

IF (@current_stock - @number_of_sold_bikes < 0)
	THROW 50000, @my_message, 1;



3. Transactions in SQL Server
</> Transaction statements
Which of the following is not correct about transaction statements?

 The BEGIN TRAN|TRANSACTION statement marks the starting point of a transaction.
 The COMMIT TRAN|TRANSACTION statement marks the end of a successful transaction.
 The COMMIT TRAN|TRANSACTION statement reverts a transaction to the beginning or a savepoint inside the transaction.
 The ROLLBACK TRAN|TRANSACTION statement reverts a transaction to the beginning or a savepoint inside the transaction.
</> Correcting a transaction
Run the code to verify there are errors.

Correct every error.

BEGIN TRY  
	START TRAN;
		UPDATE accounts SET current_balance = current_balance - 100 WHERE account_id = 1;
		INSERT INTO transactions VALUES (1, -100, GETDATE());
        
		UPDATE accounts SET current_balance = current_balance + 100 WHERE account_id = 5;
		INSERT INTO transactions VALUES (5, 100, GETDATE());
	FINISH TRAN;
END TRY
BEGIN CATCH  
	UNDO TRAN;
END CATCH

('42000', "[42000] [Microsoft][ODBC Driver 17 for SQL Server][SQL Server]Incorrect syntax near 'START'. (102) (SQLExecDirectW)")



BEGIN TRY  
	BEGIN TRAN;
		UPDATE accounts SET current_balance = current_balance - 100 WHERE account_id = 1;
		INSERT INTO transactions VALUES (1, -100, GETDATE());
        
		UPDATE accounts SET current_balance = current_balance + 100 WHERE account_id = 5;
		INSERT INTO transactions VALUES (5, 100, GETDATE());
	COMMIT TRAN;
END TRY
BEGIN CATCH  
	ROLLBACK TRAN;
END CATCH



</> Rolling back a transaction if there is an error
Begin the transaction.

Correct the mistake in the operation.

Commit the transaction if there are no errors.

Inside the CATCH block, roll back the transaction.

BEGIN TRY  
	BEGIN TRAN;
		UPDATE accounts SET current_balance = current_balance - 100 WHERE account_id = 1;
		INSERT INTO transactions VALUES (1, -100, GETDATE());
        
		UPDATE accounts SET current_balance = current_balance + 100 WHERE account_id = 5;
		INSERT INTO transactions VALUES (5, 100, GETDATE());
	COMMIT TRAN;    
END TRY
BEGIN CATCH  
	SELECT 'Rolling back the transaction';
	ROLLBACK TRAN;
END CATCH


</> Choosing when to commit or rollback a transaction
Begin the transaction.

Check if the number of affected rows is bigger than 200.

Rollback the transaction if the number of affected rows is more than 200.

Commit the transaction if the number of affected rows is less than or equal to 200.

BEGIN TRAN; 
	UPDATE accounts set current_balance = current_balance + 100
		WHERE current_balance < 5000;
	IF @@ROWCOUNT > 200 
		BEGIN 
			ROLLBACK TRAN; 
			SELECT 'More accounts than expected. Rolling back'; 
		END
	ELSE
		BEGIN 
			COMMIT TRAN; 
			SELECT 'Updates commited'; 
		END

Updates commited



</> Modifiers of the @@TRANCOUNT value
Which of the following is false about @@TRANCOUNT?

 The COMMIT TRAN|TRANSACTION statement decrements the value of @@TRANCOUNT by 1.
 The COMMIT TRAN|TRANSACTION statement decrements the value of @@TRANCOUNT to 0, except if there is a savepoint.
 The ROLLBACK TRAN|TRANSACTION statement decrements the value of @@TRANCOUNT to 0, except if there is a savepoint.
 The BEGIN TRAN|TRANSACTION statement increments the value of @@TRANCOUNT by 1.
</> Checking @@TRANCOUNT in a TRY…CATCH construct
Begin the transaction.

Correct the mistake in the operation.

Inside the TRY block, check if there is a transaction and commit it.

Inside the CATCH block, check if there is a transaction and roll it back.

BEGIN TRY
	BEGIN TRAN;
		UPDATE accounts SET current_balance = current_balance + 200
			WHERE account_id = 10;
		IF @@TRANCOUNT > 0     
			COMMIT TRAN;
     
	SELECT * FROM accounts
    	WHERE account_id = 10;      
END TRY
BEGIN CATCH  
    SELECT 'Rolling back the transaction'; 
    IF @@TRANCOUNT > 0   	
        ROLLBACK TRAN;
END CATCH

account_id	account_number			customer_id	current_balance
10			55555555555050505050	8			9200.0000



</> Using savepoints
Run the code to verify there are errors.

Correct every error.

BEGIN TRAN;
	savepoint1 SAVE TRAN;
	INSERT INTO customers VALUES ('Mark', 'Davis', 'markdavis@mail.com', '555909090');

    savepoint2 SAVE TRAN;
	INSERT INTO customers VALUES ('Zack', 'Roberts', 'zackroberts@mail.com', '555919191');

	savepoint2 ROLLBACK TRAN;
	savepoint1 ROLLBACK TRAN;

	savepoint3 SAVE TRAN;
	INSERT INTO customers VALUES ('Jeremy', 'Johnsson', 'jeremyjohnsson@mail.com', '555929292');
COMMIT TRAN;

('42000', "[42000] [Microsoft][ODBC Driver 17 for SQL Server][SQL Server]Incorrect syntax near 'savepoint1'. (102) (SQLExecDirectW)")


BEGIN TRAN;
	SAVE TRAN savepoint1;
	INSERT INTO customers VALUES ('Mark', 'Davis', 'markdavis@mail.com', '555909090');

    SAVE TRAN savepoint2;
	INSERT INTO customers VALUES ('Zack', 'Roberts', 'zackroberts@mail.com', '555919191');

	ROLLBACK TRAN savepoint2;
	ROLLBACK TRAN savepoint1;

	SAVE TRAN savepoint3;
	INSERT INTO customers VALUES ('Jeremy', 'Johnsson', 'jeremyjohnsson@mail.com', '555929292');
COMMIT TRAN;


</> XACT_ABORT behavior
If there is an error and XACT_ABORT is set to…

 OFF, the transaction will always be rollbacked.
 ON, the transaction will always be rollbacked.
 ON, the transaction can be rollbacked or not, depending on the error.
</> XACT_ABORT and THROW
Use the appropriate setting of XACT_ABORT.

Begin the transaction.

If the number of affected rows is less than or equal to 10, throw the error using the THROW statement, with a number of 55000.

Commit the transaction if the number of affected rows is more than 10.

SET XACT_ABORT ON;
BEGIN TRAN; 
	UPDATE accounts set current_balance = current_balance - current_balance * 0.01 / 100
		WHERE current_balance > 5000000;
	IF @@ROWCOUNT <= 10	
		THROW 5000000, 'Not enough wealthy customers!', 1;
	ELSE		
		COMMIT TRAN; 


</> Doomed transactions
Use the appropriate setting of XACT_ABORT.

Check if there is an open transaction.

Rollback the transaction.

Select the error message.

SET XACT_ABORT ON;
BEGIN TRY
	BEGIN TRAN;
		INSERT INTO customers VALUES ('Mark', 'Davis', 'markdavis@mail.com', '555909090');
		INSERT INTO customers VALUES ('Dylan', 'Smith', 'dylansmith@mail.com', '555888999');
	COMMIT TRAN;
END TRY
BEGIN CATCH
	IF XACT_STATE() <> 0
		ROLLBACK TRAN;
    SELECT ERROR_MESSAGE() AS Error_message;
END CATCH

Error_message
Violation of UNIQUE KEY constraint 'unique_email'. Cannot insert duplicate key in object 'dbo.customers'. The duplicate key value is (dylansmith@mail.com).


4. Controlling the concurrency: Transaction isolation levels
</> Concurrency phenomena
Which of the following is true about these concurrency phenomena?

 Non-repeatable reads occur when a transaction reads data that has been modified by another transaction without been yet committed.
You have to think a little bit more. It is dirty reads that occur when a transaction reads data that has been modified by another transaction without been yet committed.

 Dirty reads occur when a transaction reads a record twice, but the first result is different from the second result as a consequence of another committed transaction altered this data.
You have to think a little bit more. It is non-repeatable reads that occur when a transaction reads a record twice, but the first result is different from the second result as a consequence of another committed transaction altered this data.

 Phantom reads occur when a transaction reads some records twice, but the first result it gets is different from the second result as a consequence of another committed transaction having inserted a row.
 Non-repeatable reads occur when a transaction reads some records twice, but the first result it gets is different from the second result as a consequence of another committed transaction having inserted a row.
You have to think a little bit more. It is phantom reads that occur when a transaction reads some records twice, but the first result it gets is different from the second result as a consequence of another committed transaction having inserted a row.

</> Using the READ UNCOMMITTED isolation level
Set the READ UNCOMMITTED isolation level.

Select first_name, last_name, email and phone from customers table.

SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED

	SELECT
    	first_name, 
        last_name, 
        email,
        phone
    FROM customers;

first_name	last_name	email					phone
Dylan		Smith		dylansmith@mail.com		555888999
John		Antona		johnantona@mail.com		555111222
Astrid		Harper		astridharper@mail.com	555000999
...



</> Choosing the correct isolation level
From all the isolation levels you have studied so far, which isolation level prevents non-repeatable reads?

 The READ UNCOMMITTED isolation level.
 The REPEATABLE READ isolation level.
 The READ COMMITTED isolation level.
</> Prevent dirty reads
Set the appropriate isolation level to prevent dirty reads.

Select the count of accounts that match the criteria.

SET TRANSACTION ISOLATION LEVEL READ COMMITTED

SELECT COUNT(*) AS number_of_accounts
FROM accounts
WHERE current_balance >= 50000;

number_of_accounts



</> Preventing non-repeatable reads
Set the appropriate isolation level to prevent non-repeatable reads.

Begin a transaction.

Commit the transaction.

SET TRANSACTION ISOLATION LEVEL REPEATABLE READ

BEGIN TRAN;
SELECT * FROM customers;
SELECT * FROM customers;
COMMIT TRAN;

customer_id	first_name	last_name	email					phone
1			Dylan		Smith		dylansmith@mail.com		555888999
2			John		Antona		johnantona@mail.com		555111222
3			Astrid		Harper		astridharper@mail.com	555000999
...


</> Prevent phantom reads in a table
Set the appropriate isolation level to prevent phantom reads.

Begin the transaction.

Commit the transaction.

SET TRANSACTION ISOLATION LEVEL SERIALIZABLE
BEGIN TRAN;
SELECT * FROM customers;
SELECT * FROM customers;
COMMIT TRAN;

customer_id	first_name	last_name	email					phone
1			Dylan		Smith		dylansmith@mail.com		555888999
2			John		Antona		johnantona@mail.com		555111222
3			Astrid		Harper		astridharper@mail.com	555000999
...




</> Prevent phantom reads just in some rows
Set the appropriate isolation level to prevent phantom reads.

Begin a transaction.

Select those customers you want to lock.

Commit the transaction.

SET TRANSACTION ISOLATION LEVEL SERIALIZABLE
BEGIN TRAN;
SELECT * 
FROM customers
WHERE customer_id BETWEEN 1 AND 10;

SELECT * 
FROM customers
WHERE customer_id BETWEEN 1 AND 10;
COMMIT TRAN;

customer_id	first_name	last_name	email					phone
1			Dylan		Smith		dylansmith@mail.com		555888999
2			John		Antona		johnantona@mail.com		555111222
3			Astrid		Harper		astridharper@mail.com	555000999
...



</> Setting READ COMMITTED SNAPSHOT to ON
Which options do you need to set in your database?

 ALTER DATABASE myDatabaseName SET ALLOW_SNAPSHOT_ISOLATION ON and ALTER DATABASE myDatabaseName SET READ_COMMITTED_SNAPSHOT ON.
 ALTER DATABASE myDatabaseName SET ALLOW_SNAPSHOT_ISOLATION OFF and ALTER DATABASE myDatabaseName SET READ_COMMITTED_SNAPSHOT ON.
 ALTER DATABASE myDatabaseName SET TRANSACTION ISOLATION LEVEL SNAPSHOT and ALTER DATABASE myDatabaseName SET READ_COMMITTED_SNAPSHOT ON.
 ALTER DATABASE myDatabaseName SET ALLOW_SNAPSHOT_ISOLATION ON, ALTER DATABASE myDatabaseName SET TRANSACTION ISOLATION LEVEL SNAPSHOT,and ALTER DATABASE myDatabaseName SET READ_COMMITTED_SNAPSHOT ON.
</> Comparing WITH (NOLOCK) & READ UNCOMMITTED
Can you help him to clarify the differences between using WITH (NOLOCK) option and the READ UNCOMMITTED isolation level?

 The WITH (NOLOCK) option behaves like the READ UNCOMMITTED isolation level. But, whereas the isolation level applies to a specific table, the WITH (NOLOCK) option applies for the entire connection.
 The WITH (NOLOCK) option doesn’t behave like the READ UNCOMMITTED isolation level because the first one can’t read dirty reads.
 The WITH (NOLOCK) option behaves like the READ UNCOMMITTED isolation level. But whereas the isolation level applies for the entire connection, WITH NOLOCK applies to a specific table.
 The WITH (NOLOCK) option behaves like the READ UNCOMMITTED isolation level, because the first one can’t read dirty reads.
</> Avoid being blocked
Change your script to avoid being blocked.

SELECT *
	FROM transactions WITH (NOLOCK)
WHERE account_id = 1

transaction_id	account_id	amount		transaction_date
1				1			-100.0000	2019-03-18 19:12:36.810000
3				1			-9000.0000	2019-02-18 20:20:36.410000




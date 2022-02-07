

# Basic Syntax

## Structure

```plsql
DECLARE
	<declarations>
BEGIN
	<executable commands>
EXCEPTION
	<exception handling>
END;
```

## Example - Printing String

```plsql
DECLARE 
   message  varchar2(20):= 'Hello, World!'; 
BEGIN 
   dbms_output.put_line(message); 
END; 
/ 
```

# Data Types

## Scalar

Single values such as NUMBER, DATE, or BOOLEAN.

### Numeric

Arithmetic operations can be performed on these.

1. PLS_INTEGER
   Represented in 32 bits.
2. BINARY_INTEGER
   Represented in 32 bits.
3. BINARY_FLOAT
   Single precision floating point number.
4. BINARY_DOUBLE
   Double precision floating point number.
5. NUMBER(precision, scale)
   Fixed/Floating point number.
6. DEC(precision, scale)
   ANSI specific fixed point type with maximum precision of 38 decimal digits.
7. DECIMAL(precision, scale)
   IBM specific fixed point type with maximum precision of 38 decimal digits.
8. NUMERIC(precision, scale)
   Floating type with maximum precision of 38 decimal digits.
9. DOUBLE PRECISION
   ANSI specific floating-point type with max precision of 126 binary digits.
10. INTEGER
    ANSI and IBM specific floating-point type with maximum precision of 126 binary digits.
11. INT
    ANSI specific integer type with maximum precision of 38 decimal digits.
12. INTEGER
    ANSI and IBM specific integer type with maximum precision of 38 decimal digits.
13. SMALLINT
    ANSI and IBM specific integer type with maximum precision of 38 decimal digits.
14. REAL
    Floating point type with maximum precision of 63 binary digits (approximately 18 decimal digits).

#### Example

```plsql
DECLARE
    num1 INTEGER;
	num2 REAL;
	num3 DOUBLE PRECISION
BEGIN
	null
END;
/
```

### Character

Represent single characters or strings of characters.

1. CHAR
   Fixel length character strings with max 32,767 bytes.
2. VARCHAR2
   Variable length character string with maximum size of 32,767 bytes.
3. RAW
   Variable length binary or byte string with maximum size of 32,767 bytes.
4. NCHAR
   Fixed length national character string with maximum size of 32,768 bytes.
5. NVARCHAR
   Variable length national character string with maximum of 32,767 bytes.
6. LONG
7. LONG RAW
8. ROWID
   Physical row identifier, the address of a row in an ordinary table.
9. UROWID
   Universal row identifier.

### Boolean

Logical values on which logical operations are performed.

TRUE or FALSE or NULL.

### Datetime

Dates and times.

1. YEAR
2. MONTH
3. DAY
4. HOUR
5. MINUTE
6. SECOND
7. TIMEZONE_HOUR
8. TIMEZONE_MINUTE
9. TIMEZONE_REGION
10. TIMEZONE_ABBR

## Large Object

Pointers to large objects that are stored separately from other data such as text, graphics images...

### BFILE

Large binary objects in OS File System outside the DB. Cannot exceed 4 GB.

### BLOB

Used to store large binary objects in the DB. 8 to 128 TB.

### CLOB

Used to store large blocks of character data in the database. 8 to 128 TB.

NCLOB

Used to store large blocks of NCHAR data in DB. 8 to 128 TB.

## Composite

Data items that have internal components that can be accessed individually. Example: Collections and records.

## Reference

Pointers to other data items.

## User Defined Subtypes

```plsql
DECLARE
	SUBTYPE name IS char(20);
	SUBTYPE message IS varchar2(100);
	salutation name;
	greetings message;
BEGIN
	salutation := 'READER';
	greetings := 'WELCOME TO PLSQL';
	dbms_output.put_line('Hello' || salutation || greetings);
END;
/
```

# Variables

## Declaration

```plsql
variable_name [CONSTANT] datatype [NOT NULL] [:= | DEFAULT initial_value]
```

Example

```plsql
sales number(10, 2);
pi CONSTANT double precision := 3.1415
name varchar2(25);
address varchar2(100);
```

### Constrained Declaration

When we provide a size, scale or precision limit.

```plsql
sales number(10, 2);
name varchar2(25);
```

## Initialization

```plsql
counter binary_integer := 0;
greetings varchar2(20) DEFAULT 'Default Value';
```

Nested Scopes

```java
DECLARE
    -- Global Variables
    num1 number := 14;
	num2 number := 87;
BEGIN
    dbms_output.put_line('Outer Variable num1: ' || num1);
	dbms_output.put_line('Outer Variable num2: ' || num2);
	DECLARE
        -- Local Variables
        num1 number :=123;
	BEGIN
        dbms_output.put_line('Outer Variable num1: ' || num1);
		dbms_output.put_line('Outer Variable num2: ' || num2);
	END;
END;
/
```

```plsql

> Outer Variable num1: 14
> Outer Variable num2: 87
> Outer Variable num1: 123
> Outer Variable num2: 87
```

## Assigning SQL Results to PLSQL Variables

```plsql
DECLARE
	c_id customers.id%type=1;
	c_name customers.name%type;
	c_addr customers.address%type;
	c_sal customers.salary%type;
BEGIN
	SELECT name, address, salary
	INTO c_name, c_addr, c_sal
	WHERE id = c_id;
	dbms_output.putline
	(c_name || 'from' || c_addr || 'earns' || c_sal);
END;
/
```

## Constants

```plsql
PI CONSTANT NUMBER := 3.141592654
```

# Conditions

## IF-THEN

```plsql
IF condition THEN
	Statements;
END IF;	
```

Example

```plsql
IF (a <= 20) THEN
	c := c+1;
END IF;
```

## IF-THEN-ELSE

```plsql
IF condition THEN
	Statements;
ELSE
	Statements;
END IF;
```

Example

```plsql
IF color = red THEN
	dbms_output.put_line('...')
ELSE
	dbms_output.put_line('...');
END IF;
```

## IF-THEN-ELSIF

```plsql
IF condition THEN
	Statements;
ELSIF condition THEN
	Statements;
ELSIF condition THEN
	Statements;
ELSE
	Statements;
END IF;
```

## CASE

```plsql
CASE selector
	WHEN 'Value1' THEN Statement1;
	WHEN 'Value2' THEN Statement2;
	ELSE StatementN;
END CASE;
```

Example

```plsql
DECLARE
	grade char(1) := 'A';
BEGIN
	CASE grade
		when 'A' then dbms_output.put_line('Excellent');
		when 'B' then dbms_output.put_line('Good');
		when 'C' then dbms_output.put_line('Passed');
		when 'P' then dbms_output.put_line('Barely Passed');
		else dbms_output.put_line('No such grade');
	END CASE;
END;
```

## Searched CASE

CASE with no selectors and WHEN clause contains search conditions that give boolean values.

```plsql
CASE 
   WHEN selector = 'value1' THEN S1; 
   WHEN selector = 'value2' THEN S2; 
   WHEN selector = 'value3' THEN S3; 
   ... 
   ELSE Sn;  -- default case 
END CASE; 
```

Example

```plsql
DECLARE 
   grade char(1) := 'B'; 
BEGIN 
   CASE  
      when grade = 'A' then dbms_output.put_line('Excellent'); 
      when grade = 'B' then dbms_output.put_line('Very good'); 
      when grade = 'C' then dbms_output.put_line('Well done'); 
      when grade = 'D' then dbms_output.put_line('You passed'); 
      when grade = 'F' then dbms_output.put_line('Better try again'); 
      else dbms_output.put_line('No such grade'); 
   END case; 
END; 
```

# Loops

## LOOP

```plsql
LOOP
	Statements;
END LOOP;
```

```plsql
DECLARE
	x number := 10;
BEGIN 
   LOOP 
      dbms_output.put_line(x); 
      x := x + 10; 
      IF x > 50 THEN 
         exit; 
      END IF; 
   END LOOP; 
   -- after exit, control resumes here  
   dbms_output.put_line('After Exit x is: ' || x); 
END; 
/
```

## WHILE

```plsql
WHILE condition LOOP
	statements;
END LOOP;
```

## FOR

```plsql
FOR counter IN initial_value .. final_value LOOP
	statements;
END FOR;
```

```plsql
DECLARE 
   a number(2); 
BEGIN 
   FOR a in 10 .. 20 LOOP 
      dbms_output.put_line('value of a: ' || a); 
  END LOOP; 
END; 
/
```

### Reverse FOR Loop

```plsql
FOR counter IN REVERSE 10 .. 20 LOOP
	statements;
END LOOP;
```



## Labeling a PL/SQL Loop

```plsql
<< outer_loop >> 
FOR i IN 1..3 LOOP 
     << inner_loop >> 
	FOR j IN 1..3 LOOP 
		dbms_output.put_line('i is: '|| i || ' and j is: ' || j); 
	END loop inner_loop; 
END loop outer_loop;
```

## Control Statements

- EXIT
- CONTINUE
- GOTO - Transfer control to a labeled statement.

# Strings

- Enclosed in single quotes.
- Single quote is used to escape single quote.
  'This isn''t what it looks like.'

# Arrays

```plsql
TYPE varray_type_name IS VARRAY(n) of <ELEMENT_TYPE>
```

```plsql
DECLARE
	type namesarray IS VARRAY(5) of VARCHAR2(10);
	names namesarray;
BEGIN
	names := namesarray('Kav', 'Pri', 'Ayan', 'Rish', 'Az');
	FOR i in 1 .. names.count LOOP
		dbms_output.put_line(names(i));
	END LOOP;
END;
```

```plsql
> Statement processed.
Kav
Pri
Ayan
Rish
Az
```

# Procedures

Procedure means to perform an action.

## Creating Procedure

```plsql
CREATE [OR REPLACE] PROCEDURE procedure_name
[(parameter_name [IN | OUT | IN OUT] type, [, ...])]
{IS | AS}
BEGIN
	< procedure_body >
END procedure_name;
```

```plsql
CREATE PROCEDURE greetings
AS
BEGIN
	dbms_output.put_line('Hello World');
END;
/
...
EXECUTE greetings;
```

## Deleting Procedure

```plsql
DROP PROCEDURE greetings;
```

## Parameter Modes

1. IN
   - Read only parameter. 
   - Assignment is not allowed.
   - Default mode of parameter passing.
   - Passed by reference.
2. OUT
   - Actual parameter must be variable.
   - Passed by value.
3. IN OUT
   - Passes an initial value to subprogram and returns the updated value.
   - Passed by value.

```plsql
DECLARE
	a number;
	b number;
	c number;
PROCEDURE findMin(x IN number, y IN number, z OUT number) IS
BEGIN
	IF x < y THEN
		z := x;
	ELSE
		z := y;
	END IF;
END;
BEGIN
	a := 23;
	b := 45;
	findMin(a, b, c)
	dbms_output.put_line('Minimum of 23 and 45 is ' || c)
END;
/
```

## Calling a procedure

1. Positional Notation

   ```plsql
   findMin(a, b, c, d);
   ```

2. Named Notation

   ```plsql
   findMin(x => a, y => b, z => c, m =>d);
   ```

3. Mixed Notation
   Positional notation should precede the named notation.

   ```plsql
   findMin(a, b, c, m => d)
   
   findMin(x => a, b, c, d); // NOT_LEGAL
   ```

# Function

## Creating function

```plsql
CREATE FUNCTION function_name
[(parameter_name [IN | OUT | IN OUT] type [, ...])]
RETURN return_datatype
{IS | AS}
BEGIN
	< function body >
END [function_name];
```

Example

```plsql
CREATE OR REPLACE FUNCTION totalCustomers
RETURN number IS
	total number(2) := 0;
BEGIN
	SELECT count(*) into total
	FROM CUSTOMERS;
	RETURN total;
END;
/
```

## Calling Function

```plsql
DECLARE
	c number(2);
BEGIN
	c := totalCustomers();
	dbms_output.put_line('Total number of customers: ' || c);
END;
/
```

## Recursive Functions

```plsql
DECLARE 
   num number; 
   factorial number;  
   
FUNCTION fact(x number) 
RETURN number  
IS 
   f number; 
BEGIN 
   IF x=0 THEN 
      f := 1; 
   ELSE 
      f := x * fact(x-1); 
   END IF; 
RETURN f; 
END;  

BEGIN 
   num:= 6; 
   factorial := fact(num); 
   dbms_output.put_line(' Factorial '|| num || ' is ' || factorial); 
END; 
/
```

# Cursors

Oracle create a memory area, known as context area, for processing an SQL statement, which contains all the information for processing the statement. Example: Number of rows processed.

Cursor is a pointer to this context area. PLSQL controls the context area through a cursor. A cursor holds the rows returned by a SQL statement. The set of rows the cursor holds is referred to as the active set.

## Implicit Cursors

Automatically created by Oracle whenever an SQL statement is executed, when there is no explicit cursor for the statement. Programmers cannot control the implicit cursor and the information in it.

### Attributes and Description

- `%FOUND`
  Returns TRUE if the statement affected or returned one or more rows.
- `%NOTFOUND`
  Logical opposite of `%FOUND`.
- `%ISOPEN`
  Always returns FALSE for implicit cursors because Oracle closes the SQL cursor automatically after executing its associated SQL statement.
- `%ROWCOUNT`
  Returns the number of rows affected/returned by the statement.

Any SQL cursor is accessed using `sql%attribute_name` as sh

```plsql
DECLARE
	total_rows number(2);
BEGIN
	UPDATE customers
	SET salary = salary + 500;
	IF sql%notfound THEN
		dbms_output.put_line('no customers selected')
	ELSIF sql%found THEN
		total_rows := sql%rowcount;
		dbms_output.put_line(total_rows || ' customers selected');
	END IF;
END;
/
```

## Explicit Cursors

- Programmer defined for gaining more control over the context area.
- Created on SELECT statement which returns more than one row.

```plsql
CURSOR cursor_name IS select_statement;
```

### Declaring Cursor

Defines the cursor with a name and associated SELECT statement.

```plsql
CURSOR c_customers IS
	SELECT id, name, address FROM customers;
```

### Opening Cursor

Allocates the memory for cursor and make it ready for fetching.

```plsql
OPEN c_customers;
```

### Fetching Cursor

```plsql
FETCH c_customers INTO c_id, c_name, c_addr;
```

### Closing Cursor

Release the allocated memory.

```plsql
CLOSE c_customers;
```

### Example

```plsql
DECLARE
	c_id customers.id%type;
	c_name customers.name%type;
	c_addr customers.address%type;
	CURSOR c_customers IS
		SELECT id, name, address FROM customers;
BEGIN
	OPEN c_customers;
	LOOP
	FETCH c_customers into c_id, c_name, c_addr;
		EXIT WHEN c_customers%notfound;
		dbms_output.put_line(c_id || ', ' || c_name || ', ' || c_addr);
	END LOOP;
	CLOSE c_customers;
END;
/
```

# Records

Data structure that can hold data items of different kinds.

## Table based records

`%ROWTYPE` enables a programmer to create table-based and cursor-based records.

```plsql
DECLARE
	customer_record customers%rowtype
BEGIN
	SELECT * INTO customer_record
	FROM customers
	WHERE id=5;
	dbms_output.put_line('Customer ID ' || customer_record.id)
	dbms_output.put_line('Customer Name ' || customer_record.name)
	dbms_output.put_line('Customer Address ' || customer_record.address)
	dbms_output.put_line('Customer Salary ' || customer_record.salary)
END;
/	
```

## Cursor based records

`%ROWTYPE` enables a programmer to create table-based and cursor-based records.

```plsql
DECLARE
	CURSOR customer_cursor IS
		SELECT id, name, address
		FROM customers;
	customer_record customer_cursor%rowtype;
BEGIN
	OPEN customer_cursor;
	LOOP
		FETCH customer_cursor INTO customer_record;
		EXIT WHEN customer_cursor%notfound;
		dbms_output.put_line(customer_record.id || ' ' || customer_record.name);
	END LOOP;
END;
/
```

## User defined records

### Defining Record

```plsql
TYPE type_name IS RECORD
	(field_name1 data_type1 [NOT NULL] [:= DEFAULT EXPRESSION],
     ...)
record_name type_name;
```

```plsql
DECLARE
TYPE books IS RECORD
(title varchar(50),
	author varchar(50));
book1 books;
book2 books;
```

### Accessing fields

```plsql
DECLARE
	TYPE books IS RECORD
		(title varchar(50),
         	author varchar(50),
         	subject varchar(100));
	book1 books;
	book2 books;
BEGIN
	-- Book 1 Specification
	book1.title := "C Programming";
	book1.author := 'Nuha Ali';
	book1.subject := 'Computer Science';
	
	-- Book 2 Specifications
	book2.title := 'Introduction to Software Engineering'
	book2.author := 'Ian Sommerville';
	book2.subject := 'Software Engineering';
	
	-- Print book 1 record 
    dbms_output.put_line('Book 1 title : '|| book1.title); 
    dbms_output.put_line('Book 1 author : '|| book1.author); 
    dbms_output.put_line('Book 1 subject : '|| book1.subject); 
    
    -- Print book 2 record 
    dbms_output.put_line('Book 2 title : '|| book2.title); 
    dbms_output.put_line('Book 2 author : '|| book2.author); 
    dbms_output.put_line('Book 2 subject : '|| book2.subject);
END;
/
```

# Exceptions

```plsql
DECLARE
...
BEGIN
...
EXCEPTION
	<exception handling goes here>
	WHEN exception1 THEN
		exception-handling
	WHEN exception THEN
		exception-handling
	...
	WHEN others THEN
		exception-handling
END;
```

## Example

```plsql
DECLARE
	c_id customers.id%type := 8;
	c_name customers.Name%type;
BEGIN
	SELECT name into c_name;
	FROM customer
	WHERE id = c_id;
	dbms_output.put_line('Name: ' || c_name);
	
EXCEPTION
	WHEN no_data_found THEN
		dbms_output.put_line('No customer found');
	WHEN others THEN
		dbms_output.put_line('ERROR!!!');
END;
/
```

## Raising Exceptions

```plsql
DECLARE
	exception_name EXCEPTION;
BEGIN
	IF condition THEN
		RAISE exception_name;
	END IF;
EXCEPTION
	WHEN exception_name THEN
		statements;
END;
/
```

## Pre-defined Exceptions

1. `ACCESS_INTO_NULL`
   When null object is automatically assigned a value.
2. `CASE_NOT_FOUND`
   Raised when none of the choices in the WHEN clause of the CASE statement is selected, and there is no ELSE clause.
3. `COLLECTION_IS_NULL`
   When the nested table or varray is uninitialized and program attempts to apply collection method or assign values to it.
4. `DUP_VAL_ON_INDEX`
   When duplicate values are attempted to be stored in a column with unique index.
5. `INVALID_CURSOR`
   Invalid cursor operations like closing an unopened cursor.
6. `INVALID_NUMBER`
   When conversion from string to number is not valid/possible.
7. `LOGIN_DENIED`
   Invalid username or password.
8. `NO_DATA_FOUND`
   When SELECT INTO returns no rows.
9. `NOT_LOGGED_ON`
   When database call is made without connecting to DB.
10. `PROGRAM_ERROR`
    Internal PLSQL problem.
11. `ROWTYPE_MISMATCH`
    Cursor fetches value in a variable having incompatible data type.
12. `SELF_IS_NULL`
    Member method is invoked but the instance of the object type was not initialized. 
13. `STORAGE_ERROR`
    Running out of memory.
    OR Corrupted memory.
14. `TOO_MANY_ROWS`
    When `SELECT INTO` returns more than one statements.
15. `VALUE_ERROR`
    Arithmetic, conversion, truncation, or size constraint error occurs.
16. `ZERO_DIVIDE`
    Division by zero

# Triggers

Stored programs automatically executed in response to some events. These are the triggers written to be executed in response to any events.

1. Database Manipulation - DELETE, INSERT, or UPDATE
2. Database Definition - CREATE, DROP, or ALTER
3. Database Operation - LOGON, SERVERERROR, STARTUP, or SHUTDOWN

```plsql
CREATE [OR REPLACE] TRIGGER trigger_name   -- Creating Trigger
{BEFORE | AFTER | INSTEAD OF}        -- When it should be executed
{INSERT [OR] | UDPATE [OR] | DELETE} -- Specifies the DML operation
[OF col_name]                        -- Specifies the column name to be updated
ON table_name					   -- Table associated with trigger
[REFERENCING OLD AS o NEW AS n]      -- Allows to refer new and old values for DML statements
[FOR EACH ROW]
WHEN (condition)
DECLARE
	Declare-Statements;
BEGIN
	Executable-Statements;
EXCEPTION
	Exception-Handling-Statement;
END;
```

Example

```plsql
CREATE OR REPLACE TRIGGER display_salary_changes
BEFORE DELETE OR INSERT OR UPDATE on customers
FOR EACH ROW
WHEN (NEW.ID > 0)
DECLARE
	sal_diff number;
BEGIN
	sal_diff := :NEW.salary - :OLD.salary;
	dbms_output.put_line('Old Salary: ' || :OLD.salary);
	dbms_output.put_line('New Salary: ' || :NEW.salary);
	dbms_output.put_line('Salary Difference: ' || sal_diff);
END;
/	
```

# Packages

Used to group logically related PLSQL types, variables, and subprograms.

## Specification

Specification is the interface to the package and includes the types, variables, constants, exceptions, cursors, and subprograms that can be referenced from outside the package. It contains all the information about the package except the code for the subprograms.

Only public objects are placed in the specification.

```plsql
CREATE PACKAGE customer_salary AS
	PROCEDURE find_salary(c_id customers.id%type);
END customer_salary;
/
```

## Body

```plsql
CREATE OR REPLACE PACKAGE BODY customer_salary AS
	PROCEDURE find_salary(c_id customers.id%TYPE) IS
	c_sal customers.salary%TYPE;
	BEGIN
		SELECT salary INTO c_sal
		FROM customers
		WHERE id = c_id;
		FROM customers;
		dbms_output.put_line('Salary' || c_sal);
	END find_salary;
END customer_salary;
/
```

## Using package elements

```plsql
package_name.element_name;
```

```plsql
DECLARE
	code customers.id%type := &cc_id;
BEGIN
	customer_salary.find_salary(code);
END;
/
```

# Collections

Ordered group of elements having the same data type.

1. Index-by tables or Associative Array
   - Unbounded
   - String or Integer based Index
   - Good for both dense and sparse collections.
   - Created in PLSQL Block
   - Cannot be object type attribute.
2. Nested table
   - Unbounded
   - Integer based Index
   - Good for ones that are dense but can become sparse.
   - Created in PLSQL or schema level.
   - Can be object type attribute.
3. Variable size array or Varray
   - Bounded
   - Integer based Index
   - Always dense
   - Created in PLSQL or schema level.
   - Can be object type attribute.

## Index-by Table

Also called associative arrays, is a set of key-value pairs. Each key is unique and used to locate the corresponding value. Key can be string or integer.

```plsql
TYPE type_name IS TABLE OF element_type [NOT NULL] INDEX BY index_type;
table_name type_name;
```

Example

```plsql
DECLARE
	TYPE salary IS TABLE OF number INDEX BY varchar2(20);
	salary_list salary;
	name varchar2(20);
BEGIN
	salary_list('Meh') := 62000;
	salary_list('Martin') := 1204;
	
	name := salary_list.FIRST;
	WHILE name IS NOT null LOOP
		dbms_output.put_line
		('Salary of ' || name || TO_CHAR(salary_list(name)));
		name := salary_list.NEXT(name);
	END LOOP;
END;
/
```

## Nested Table

Nested table is one-dimensional array with an arbitrary number of elements.

- An array has declared number of elements but a nested table does not. Size of nested table can be increased dynamically.
- Array is always dense. A nested table can be dense initially but can become sparse when elements are deleted from it.

```plsql
TYPE type_name IS TABLE OF element_type [NOT NULL];
table_name type_name;
```

```plsql
DECLARE
	TYPE names_table IS TABLE OF varchar2(10);
	names names_table;
	total integer;
BEGIN
	names := names_table('Meh', 'Martin', 'Jon');
	total := names.count;
	dbms_output.put_line('Total' || total);
	FOR i IN 1 .. total LOOP
		dbms_output.put_line('Student' || names(i));
	END LOOP;
END;
/
```

## Collection Methods

- `EXISTS(element)`
- `COUNT`
- `LIMIT`
- `FIRST`
- `LAST`
- `PRIOR(n)`
  Index number that precedes index `n`.
- `NEXT(n)`
  Index number that succeeds index `n`.
- `EXTEND`
  Appends one `null` element to the collection.
- `EXTEND(n)`
  Appends `n` `null` elements to the collection.
- `EXTEND(n, i)`
  Appends `n` copies of the $n^{th}$ element to the collection.
- `TRIM`
  Removes one element from the end of the collection.
- `TRIM(n)`
  Removes `n` elements.
- `DELETE`
  Removes all elements from a collection, setting `COUNT` to zero.
- `DELETE(n)`
  Removes $n^{th}$ element from the collection.
- `DELETE(m, n)`
  Removes all elements in the range `m..n` from the associative array or nested table. If `m` is larger than `n` or if `m` or `n` is null, `DELETE(m, n)` does nothing.

## Collection Exceptions

- `COLLECTION_IS_NULL`
  Null Collection
- `NO_DATA_FOUND`
  Operating on non-existing element.
- `SUBSCRIPT_BEYOND_COUNT`
  Index exceeds the total number of elements.
- `SUBSCRIPT_OUTSIDE_LIMIT`
  Index outside of the allowed range.
- `VALUE_ERROR`
  Index is null or not convertible to the key type.

# DateTime

```PLSQL
SELECT SYSDATE FROM DUAL;

> 7/28/2021 11:04:23 AM
```

```plsql
SELECT TO_CHAR(CURRENT_DATE, 'DD-MM-YYYY HH:MI:SS') FROM DUAL;

> 7-28-2021 11:04:23
```

```plsql
SELECT ADD_MONTHS(SYSDATE, 5) FROM DUAL;

> 12-28-2021 11:04:23
```

# DBMS Output

- `DBMS_OUTPUT.DISABLE`
- `DBMS_OUTPUT.ENABLE(buffer_size IN INTEGER DEFAULT 20000);`
  Enables message output. A NULL value of `buffer_size` represents unlimited buffer size.
- `DBMS_OUTPUT.GETLINE(line OUT VARCHAR2, status OUT INTEGER);`
  Single line of buffered information.
- `DBMS_OUTPUT.GET_LINES(lines OUT CHARARR, numlines IN OUT INTEGER);`
  Array of lines from the buffer.
- `DBMS_OUTPUT.NEW_LINE`
  Puts an end-of-line marker.
- `DBMS_OUTPUT.PUT(item IN VARCHAR2);`
  Places a partial line in the buffer.
- `DBMS_OUTPUT.PUT_LINE(item IN VARCHAR2);`
  Places a line in the buffer.

# Object Oriented PLSQL

```plsql
CREATE OR REPLACE TYPE address AS OBJECT
house_number varchar2(10),
street varchar2(30),
city varchar2(20)
);
/
```

## Instantiating an Object

```plsql
DECLARE
	residence address;
BEGIN
	residence := address('103A', 'XYZ Road', 'Jaipur');
	dbms_output.put_line('House No: '|| residence.house_no);
	dbms_output.put_line('Street: '|| residence.street); 
	dbms_output.put_line('City: '|| residence.city);
END;
/
```

## Member Functions

### Creating Body

```plsql
CREATE OR REPLACE TYPE BODY rectangle AS
	MEMBER FUNCTION enlarge(inc number) return rectangle IS
	BEGIN
		return rectangle(self.length + inc, self.width + inc);
	END enlarge;
	MEMBER PROCEDURE display IS
	BEGIN
		dbms_output.put_line('Length: ' || length);
		dbms_output.put_line('Width: ' || width);
	END display;
	MAP MEMBER FUNCTION measure return number IS
	BEGIN
		return(sqrt(length*length + width*width));
	END MEASURE;
END;
/
```

### Map Method

Used for performing comparisons between single attributes of an object to a single attribute of another object instance. 

```plsql
CREATE OR REPLACE TYPE rectangle AS OBJECT
(length number, 
 width number,
 member function enlarge(inc number) return rectangle,
 member procedure display,
 map member function measure return number
);
/
```

#### Using Map

```plsql
DECLARE
	r1 rectangle;
	r2 rectangle;
	inc_factor number := 5;
BEGIN
	r1 := rectangle(3, 4);
	r2 := rectangle(5, 7);
	IF(r1 > r2) THEN			-- This calls the map function
		r1.display
	ELSE
		r2.display
	END IF;
END;
/
```

### Order Method

It allows to compare all the attributes of the two object instances being compared. The return value can range between -1 to +1. 

```plsql
CREATE OR REPLACE TYPE BODY rectangle AS
	MEMBER PROCEDURE display IS
	BEGIN
		dbms_output.put_line('Length' || length);
		dbms_output.put_line('Width' || width);
	END display;
	ORDER MEMBER FUNCTION measure(r rectangle) return number IS
	BEGIN
		IF(sqrt(self.length * self.length + self.width * self.width) >
			sqrt(r.length*r.length + r.width*r.width)) then 
    	     return(1); 
	    ELSE 
    		return(-1); 
		END IF; 
	END measure; 
END; 
/ 
```

# Inheritance

To implement inheritance, base objects should be declared as `NOT FINAL`. The default is `FINAL`.

## Creating base object

```plsql
CREATE OR REPLACE TYPE rectangle AS OBJECT
(
    length number,
	width number,
	member function enlarge(inc number) return rectangle,
	NOT FINAL member procedure display
) NOT FINAL
/
```

```plsql
CREATE OR REPLACE TYPE BODY rectangle AS
	MEMBER FUNCTION enlarge(inc number) return rectangle IS
	BEGIN
		return rectangle(self.length + inc, self.width + inc);
	END enlarge;
	MEMBER PROCEDURE display IS
	BEGIN
		dbms_output.put_line('Length: ' || length);
		dbms_output.put_line('Width: ' || width);
	END display;
END;
/
```

## Creating child object `tabletop`

```plsql
CREATE OR REPLACE TYPE tabletop UNDER rectangle
(
    material varchar2(20)
    OVERRIDING member procedure display
)
/
```

```plsql
CREATE OR REPLACE TYPE BODY tabletop AS
OVERRIDING MEMBER PROCEDURE display IS
BEGIN
	dbms_output.put_line('Length: ' || length);
    dbms_output.put_line('Width: ' || width);
    dbms_output.put_line('Material: ' || material);
END display;
/
```

## Using `tabletop` object and member functions

```plsql
DECLARE
	t1 tabletop;
	t2 tabletop;
BEGIN
	t1 := tabletop(20, 10, 'Wood');
	t2 := tabletop(50, 30, 'Steel');
	t1.display;
	t2.display;
END;
/
```

# Abstract Object in PLSQL

`NOT INSTANTIABLE` allows to declare an abstract object. Subtype or child type of such objects is required to use the functionalities.

```plsql
CREATE OR REPLACE TYPE rectangle AS OBJECT
(
    length number,
    width number,
    NOT INSTANTIABLE NOT FINAL MEMBER PROCEDURE display
) NOT INSTANTIABLE NOT FINAL
/
```


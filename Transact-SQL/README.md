# T-SQL
T-SQL, or Transact-SQL, is a set of programming extensions from Sybase and Microsoft that add several features to the Structured Query Language (SQL). It is an extension of the SQL language and is used to interact with relational databases. T-SQL expands on the SQL standard to include procedural programming, local variables, various support functions for string processing, date processing, mathematics, etc., and changes to the DELETE and UPDATE statements.

## Table of Contents
- [Create Table](#Tables)
- [Date Time Object and Functions](#Datetime)
- [Views](#Views)
- [Synonym](#Synonym)
- [Json](#Json)

## [Create Table](#Tables)

### Syntax for creating a Table
	CREATE TABLE [TableName]
	(
		[ColumnName1] DataType1 [ConstrainsIfAny],
		[ColumnName2] DataType2 [ConstrainsIfAny]
	)

Example:

    CREATE TABLE [tblExample]
    (
    	[Id] int not null identity(1,1),
    	[Name] varchar(20) not null,
      	[TimeStamp] date not null
    );

### Syntax for creating a Table Primary Key Constraints
	CREATE TABLE [TableName]
	(
		[ColumnName1] DataType1 [ConstrainsIfAny],
		CONSTRAINT ConstraintName PRIMARY KEY([ColumnName1])
	)

Example:

    CREATE TABLE [tblExample]
    (
    	[Id] int not null identity(1,1),
      	CONSTRAINT PK_tblExample_Id PRIMARY KEY([Id])
    );

### Syntax for creating a Table Unique Key Constraints
	CREATE TABLE [TableName]
	(
		[ColumnName1] DataType1 [ConstrainsIfAny],
		CONSTRAINT ConstraintName UNIQUE([ColumnName1])
	)

Example:

    CREATE TABLE [tblExample]
    (
    	[Id] int not null identity(1,1),
      	CONSTRAINT UQ_tblExample_Id UNIQUE([Id])
    );

### Syntax for creating a Table Check Constraints
	CREATE TABLE [TableName]
	(
		[ColumnName1] DataType1 [ConstrainsIfAny],
		CONSTRAINT ConstraintName CHECK(Condition)
	)

Example:

	CREATE TABLE [tblExample]
	(
		[Id] int not null identity(1,1),
		[Name] varchar(50) not null,
		CONSTRAINT CK_tblExample_Name CHECK(LEN([Name]) = 10)
	);

### Syntax for creating a Table Foreign Key Constraints
	CREATE TABLE [TableName]
	(
		[ColumnName1] DataType1 [ConstrainsIfAny],
		CONSTRAINT ConstraintName FOREIGN KEY([ColumnName1]) REFERENCES [tblReference] ([ReferencedColumn])
	)

Example:

    CREATE TABLE [tblExample]
    (
    	[Id] int not null identity(1,1),
     	CONSTRAINT FK_tblExample_Id FOREIGN KEY([Id]) REFERENCES [tblReference] ([RId])
    );

## [Date Time Object and Functions](#Datetime)
### Data Type for Date & Time
![DateTime Data Types](https://github.com/rahul-jha-official/Query-Language/assets/138975150/56c92c62-edde-425a-9889-5be49ca8fdb7)
### Getting Current Date Time
- SELECT CURRENT_TIMESTAMP
- SELECT GETDATE()
- SELECT SYSDATETIME()
- SELECT GETUTCDATE()
- SELECT SYSUTCDATETIME()
- SELECT SYSDATETIMEOFFSET()

### Create Date Time From Parts
- SELECT DATEFROMPARTS(2022,10,24) --DATEFROMPARTS(yyyy,MM,dd)
- SELECT DATETIMEFROMPARTS(2015,2,28,10,20,30,124) --DATETIMEFROMPARTS(yyyy,MM,dd,hh,mm,ss,ms)
- SELECT DATETIME2FROMPARTS(2015,2,28,10,20,30,124,5) --DATETIME2FROMPARTS(yyyy,MM,dd,hh,mm,ss,ms,NumberOfDigitsInMS)

### Getting Date Object Parts
- SELECT YEAR(GETDATE()) AS Year
- SELECT MONTH(GETDATE()) AS Month
- SELECT DAY(GETDATE()) AS Day

### Date Time Functions
- **DIFFERENCE OF 2 DATE - DATEDIFF(INTERVAL, STARTDATE,ENDDATE)**
  - SELECT DATEDIFF(YEAR,'1996-10-17','2024-06-01')
  - SELECT DATEDIFF(MONTH,'1996-10-17','2024-06-01')
  - SELECT DATEDIFF(DAY,'1996-10-17','2024-06-01')
- **ADD INTERVAL IN DATE - DATEADD(INTERVAL,INCREAMENT,DATE)**
  - SELECT DATEADD(DAY,-1,GETDATE())
- **PART OF DATE - DATEPART(INTERVAL,DATE)**
  - SELECT DATEPART(DAY,GETDATE())
  - SELECT DATEPART(WEEK,GETDATE())
  - SELECT DATEPART(MONTH,GETDATE())
  - SELECT DATEPART(SECOND,GETDATE())
- **NAME FROM DATE - DATENAME(INTERVAL,DATE)**
  - SELECT DATENAME(MONTH,DATEFROMPARTS(2022,10,24)) --October
  - SELECT DATENAME(WEEKDAY,DATEFROMPARTS(2022,10,24)) --Monday
 
### FORMAT DATETIME - FORMAT(DATE, FORMAT, COUNTRY-CODE)
DECLARE @DATE DATETIME = '2024-06-01 14:55:08.070';
- SELECT FORMAT(@DATE, 'yyyy-MM');--2024-06
- SELECT FORMAT(@DATE, 'yyyyMMdd');--20240601
- SELECT FORMAT(@DATE, 'D'); --Saturday, June 1, 2024
- SELECT FORMAT(@DATE, 'd');--6/1/2024
- SELECT FORMAT(@DATE, 'D', 'zh-CN');--2024年6月1日
- SELECT FORMAT(@DATE, 'D', 'es-ES');--sábado, 1 de junio de 2024

### PARSE DATE
- SELECT PARSE('2024-06-01' AS DATETIME)
- SELECT PARSE('sábado, 1 de junio de 2024' AS DATE USING 'es-ES')
- SELECT CONVERT(DATETIME, '2024-06-01')
- SELECT CAST('2024-06-01' AS DATETIME)

### DATE TIME OFFSET
DECLARE @MYDATE1 AS DATETIMEOFFSET = '2024-06-01 15:06:51.910';
DECLARE @MYDATE2 AS DATETIMEOFFSET = '2024-06-01 15:06:51.910 +05:30';
- SELECT @MYDATE1, @MYDATE2
- SELECT TODATETIMEOFFSET(@MYDATE1,'+05:30')
- SELECT DATETIMEOFFSETFROMPARTS(2015,2,28,10,20,30,124,5,30,5) --DATETIMEOFFSETFROMPARTS(yyyy,MM,dd,hh,mm,ss,ms,Time-Hour,Time-Minute,NumberOfDigitsInMS)
- SELECT SWITCHOFFSET(@MYDATE2,'+00:00')

## Views
A view is a virtual table whose contents are defined by a query. Like a table, a view consists of a set of named columns and rows of data. A view acts as a filter on the underlying tables referenced in the view. The query that defines the view can be from one or more tables or from other views in the current or other databases. Distributed queries can also be used to define views that use data from multiple heterogeneous sources. This is useful, for example, if you want to combine similarly structured data from different servers, each of which stores data for a different region of your organization.

Views can be used as security mechanisms by letting users access data through the view, without granting the users permissions to directly access the underlying base tables of the view. Views can be used to provide a backward compatible interface to emulate a table that used to exist but whose schema has changed. Views can also be used when you copy data to and from SQL Server to improve performance and to partition data.

A view can be created only in the current database. A view can have a maximum of 1,024 columns.

**Note: You cannot use ORDER BY clause in Views.**

For More Info Visit: https://learn.microsoft.com/en-us/sql/relational-databases/views/views?view=sql-server-ver16

### Syntax for creating view
> 
    CREATE VIEW AllEmployeeView AS
    SELECT	e.Name as FullName, 
    		CASE WHEN e.Sex in ('F','f') THEN 'Female' ELSE 'Male' END AS Gender, 
    		d.DepartmentName 
    FROM tblEmployee e INNER JOIN tblDepartment d 
    ON e.DepartmentId = d.DId;
    GO

### Syntax for getting data from view
>
    SELECT * FROM dbo.[AllEmployeeView]

### Syntax for Dropping View
>
    IF EXISTS(SELECT * FROM SYS.VIEWS WHERE NAME = 'AllEmployeeView')
    	DROP VIEW dbo.[AllEmployeeView];
    GO

>
    DROP VIEW IF EXISTS AllEmployeeView;

>
    --Drop View Dynamically
    DECLARE @ViewName VARCHAR(20) = 'AllEmployeeView';
    IF OBJECT_ID(@ViewName,'V') IS NOT NULL
    	DECLARE @Query NVARCHAR(50) = 'DROP VIEW ' + @ViewName;
    	EXEC sp_executesql @Query


### Upate existing View
>
    ALTER VIEW AllEmployeeView AS 
      SELECT	e.Name as FullName, 
      		CASE WHEN e.Sex in ('F','f') THEN 'Female' ELSE 'Male' END AS Gender, 
      		d.DepartmentName,
      		e.DateOfJoining
      FROM tblEmployee e INNER JOIN tblDepartment d 
      ON e.DepartmentId = d.DId;

## [Synonym](#Synonym)
Synonym helps us to provide simple & small name for the table. We can also define synonym for the table which not exist into the system now.

Syntax: CREATE SYNONYM ShortName FOR FullTableName

Example: 

	IF OBJECT_ID(N't_dpt', N'SN') IS NOT NULL
		DROP SYNONYM t_dpt
	
	CREATE SYNONYM t_dpt FOR dbo.tblDepartment;
	
	SELECT * FROM t_dpt

## [Json](#Json)
There is no separate datatype for storing json value. To store Json Data in column of a table we uses NVARCHAR datatype.

### FUNCTION: ISJSON
To check whether a string is in valid JSON format. It returns 1/0. 1 if data is valid JSON else 0.

Example:

	DECLARE @data NVARCHAR(200) = '{"name":"Rahul", "mobile": ["123","456"], "addresses": [{"line1":"39","line2":"hazaryana","city":"jhansi"}]}';
	SELECT ISJSON(@data) AS IsValidJson

### FUNCTION: JSON_QUERY
When using JSON with SQL Server, you can use the JSON_QUERY() function to extract an object or an array from a JSON string. JSON_QUERY always returns an object or an array.

Example:

	DECLARE @data NVARCHAR(200) = '{"name":"Rahul", "mobile": ["123","456"], "addresses": [{"line1":"39","line2":"hazaryana","city":"jhansi"}]}';
	SELECT JSON_QUERY(@data, '$') AS WholeJson
	-- $ sign signifies root path
	SELECT JSON_QUERY(@data, '$.name') AS Name
	-- As Name is a scalar value JSON_QUERY will return null
	SELECT JSON_QUERY(@data, '$.addresses') AS AllAddresses
	-- Return the array of Addresses

### FUNCTION: JSON_VALUE
Extracts a scalar value from a JSON string.

Example:

	DECLARE @data NVARCHAR(200) = '{"name":"Rahul", "mobile": ["123","456"], "addresses": [{"line1":"39","line2":"hazaryana","city":"jhansi"}]}';
	SELECT JSON_VALUE(@data, '$') AS WholeJson
	-- Since whole json is object thus null is returned
	SELECT JSON_VALUE(@data, '$.name') AS Name
	-- As Name is a scalar value JSON_VALUE will return name value: Rahul
	SELECT JSON_VALUE(@data, '$.addresses[0].city') AS AllAddresses
	-- Return the City of 0th index Address

### Strict in Path
if we add strict in path then if correct path is not specified exception will be thrown.

Example:

	DECLARE @data NVARCHAR(200) = '{"name":"Rahul", "mobile": ["123","456"], "addresses": [{"line1":"39","line2":"hazaryana","city":"jhansi"}]}';
	SELECT JSON_QUERY(@data, 'strict $.mobile') AS MobileObject
	SELECT JSON_VALUE(@data, 'strict $.mobile[0]') AS MobileAt0
	
	-- Exception will be thrown for below as column does not exist in JSON
	SELECT JSON_QUERY(@data, 'strict $.address') AS AddressObject
	SELECT JSON_VALUE(@data, 'strict $.address[0]') AS AddressAt0

### FUNCTION: JSON_MODIFY
Used to modify JSON Data.

Example:

	DECLARE @data NVARCHAR(200) = '{"name":"Rahul", "mobile": ["123","456"], "addresses": [{"line1":"39","line2":"hazaryana","city":"jhansi"}]}';
	-- Modify Mobile Array's 1st Index
	select JSON_MODIFY(@data, 'strict  $.mobile[1]', '888')
	-- Modify whole Mobile Array
	select JSON_MODIFY(@data, 'strict  $.mobile', JSON_QUERY('["111","222"]'))
	-- Add new column date
	select JSON_MODIFY(@data, '$.date', cast(getutcdate() as varchar(10)))

### Convert a table to JSON Object
- Return an array of rows: SELECT * FROM tblExample FOR JSON PATH
- Return a object which has root as 'example': SELECT * FROM tblExample FOR JSON PATH, ROOT('employee')

### Convert a JSON Object to Table

	DECLARE @data NVARCHAR(200) = '{"name":"Rahul", "DOB": "1997-10-02", "mobile": ["123","456"], "addresses": [{"line1":"39","line2":"hazaryana","city":"jhansi"}]}';
	SELECT * 
	FROM OPENJSON(@data, '$')
	WITH 
	(
		FirstName VARCHAR(30) '$.name',
		DOB DATE,
		FirstContact VARCHAR(10) '$.mobile[0]',
		Address NVARCHAR(MAX) '$.addresses[0]' AS JSON
	)

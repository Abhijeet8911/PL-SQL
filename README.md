# Oracle PL-SQL
## [UPDATE IN PROGRESS]
Overview of PL/SQL

--PLSQL -----------------------------------------------------------
Table1 name = Employees
Row name = First_name, Last_name, Salary, Emp_id, deptid
Table2 name = Departments
Row name = deptid, dept_name, manager_id, location_id

-- Basic Syntax of PL/SQL: -----------------------------------------------------------

````
set serveroutput on;
DECLARE
BEGIN
EXCEPTION
END
/
````
-----------------------------------------------------------
````
BEGIN 
	dbms_output.put_line('Welcome')
end;
/
````
-- To display this on output use -----------------------------------------------------------
set serveroutput ON
BEGIN 
	dbms_output.put_line('Welcome')
end;
/

-- To define and declare variable -----------------------------------------------------------
DECLARE
	var1 VARCHAR2(20);
	num1 number(3);
begin
	var1 := 'Data';
	num1 := 100;
	dbms_output.put_line('Val1 : '|| var1);
	dbms_output.put_line('Num1 : '|| num1);
end;
/

-- Get values from table then assign to a variable -----------------------------------------------------------
DECLARE
	name Employees.First_name%TYPE; --to get the type attribute
	sal Employees.Salary%TYPE;
	--name VARCHAR2(10);
	--sal number(10,2);
begin
	SELECT First_name,Salary into name,sal from Employees
	where Emp_id = 100;
	dbms_output.put_line('Name : '|| name);
	dbms_output.put_line('Salary : '|| sal);
end;
/

--To get the complete row in a particular variable use ROWTYPE attribute -----------------------------------------------------------
DECLARE
	record Employees%ROWTYPE;
BEGIN
	select * into record from Employees
	WHERE Emp_id = 100;
	dbms_output.put_line(record.First_name ||' | '||record.Last_name ||' | '|| record.Salary);
end;
/

--Contional Statement -----------------------------------------------------------
-- IF THEN END IF
-- IF THEN ELSE END IF
-- IF THEN ELSIF END IF
-- CASE

DECLARE
	deptid NUMBER(2);
	sal NUMBER(10,2);
begin
	select deptid, Salary into deptid, sal from Employees 
	where Emp_id = 101;
	dbms_output.put_line(sal || ' : ' || deptid);
	
	if deptid = 30 THEN
		sal := sal + sal*.10;
		
	elsif deptid = 60 THEN
		sal := sal + sal*.20;
	
	ELSE
		sal := sal + sal*.30;
	end if;
	
	update Employees set Salary = sal where Emp_id = 101;
	dbms_output.put_line(sal || ' : ' || deptid);
end;
/

--Case 
DECLARE
	num number(1):= &weekday;
	dayname varchar2(10);
BEGIN
	dayname := Case num 
		when 1 then 'Monday'
		when 2 then 'Tuesday'
		when 3 then 'Wednesday'
		when 4 then 'Thursday'
		when 5 then 'Friday'
		when 6 THEN 'Saturday'
		when 7 then 'Sunday'
		else 'Invalid Input'
	end;
	dbms_output.put_line(dayname);
END;
/	
 
--LOOPS -----------------------------------------------------------
set serveroutput on;
DECLARE
	i number(2);
begin 
	i := 1;
	loop
		dbms_output.put_line(i);
		i := i+1;
		exit when i > 10;
	end loop;
end;
/

-- WHILE loop
set serveroutput on;
DECLARE
	i number(2);
begin 
	i := 1;
	WHILE i<=10 
	loop
		dbms_output.put_line('i = '||i);
		i := i+1;
	end loop;
end;
/

-- FOR LOOP
SET serveroutput on;
begin 
	for i in 1..10 LOOP
		dbms_output.put_line('i = '||i);
	
	for i in reverse 1..10 LOOP
		dbms_output.put_line('reverse i = '||i);	
	end loop;
end;

-- CURSOR : -----------------------------------------------------------
-- A work area is used by the oracle engine for its internal processing and sorting the information.
-- Work area is private to SQL operation. Cursor is the PL/SQL construct that allows the use to name the work area and access the stored information in it.
-- The major function of a cursor is to retrieve data, one row at a time, from a result set, unlike the sql command which operate on all rows in the result set at one time. 
-- The Data that is stored in the Cursor is called the Active Data Set. Oracle DBMS has another predefined area in the main memory Set, within which the cursors are opened. 
-- Hence the size of the cursor is limited by the size of this pre-defined area.

-- Implicit Cursor: 
	Whenever a DML statement (INSERT, UPDATE and DELETE) is issued, an implicit cursor is associated with this statement. 
	For INSERT operations, the cursor holds the data that needs to be inserted. 
	For UPDATE and DELETE operations, the cursor identifies the rows that would be affected

	-- %FOUND
		Returns TRUE if an INSERT, UPDATE, or DELETE statement affected one or more rows or a 
		SELECT INTO statement returned one or more rows. Otherwise, it returns FALSE.
	
	-- %NOTFOUND
		The logical opposite of %FOUND. It returns TRUE if an INSERT, UPDATE, or DELETE statement affected no rows, 
		or a SELECT INTO statement returned no rows. Otherwise, it returns FALSE.
		
	-- %ISOPEN
		Always returns FALSE for implicit cursors, because Oracle closes the SQL cursor automatically after executing 
		its associated SQL statement.
		
	-- %ROWCOUNT
		Returns the number of rows affected by an INSERT, UPDATE, or DELETE statement, 
		or returned by a SELECT INTO statement.
		
SET serveroutput on;
DECLARE
	cnt number(3);
begin 
	update Employees set Salary = Salary + 2 
	where deptid = 102;
	cnt := SQL%ROWCOUNT;
	dbms_output.put_line(cnt || ' Rows updated');
end;
/

-- Explicit CURSOR
	Explicit cursors are programmer-defined cursors for gaining more control over the context area.
	It is created on a SELECT Statement which returns more than one row. A suitable name for the cursor.
	DECLARE
	OPEN
	FETCH
	CLOSE

SET serveroutput on;
DECLARE
	v_empno Employees.Emp_id%TYPE;
	v_ename Employees.Last_name%TYPE;
	CURSOR emp_cursor IS
		SELECT Emp_id,Last_name 
		from Employees;
BEGIN
	OPEN emp_cursor;
	LOOP
		FETCH emp_cursor into v_empno,v_ename;
		EXIT when emp_cursor%ROWCOUNT > 10 or emp_cursor%NOTFOUND;
		dbms_output.put_line(v_empno|| ' | '|| v_ename);
	end	LOOP
	CLOSE emp_cursor;
END;
/

-- Parameterized CURSOR

SET serveroutput on;
DECLARE
	v_empno Employees.Emp_id%TYPE;
	v_ename Employees.Last_name%TYPE;
	CURSOR emp_cursor(p_deptid NUMBER) IS
		SELECT Emp_id,Last_name 
		from Employees 
		WHERE deptid = p_deptid;
BEGIN
	OPEN emp_cursor(10);
	LOOP
		FETCH emp_cursor into v_empno,v_ename;
		EXIT when emp_cursor%ROWCOUNT > 10 or emp_cursor%NOTFOUND;
		dbms_output.put_line(v_empno|| ' | '|| v_ename);
	end	LOOP
	CLOSE emp_cursor;
	
	dbms_output.put_line('----------------------------------------------------');
	open emp_cursor(20);
	LOOP
		FETCH emp_cursor into v_empno,v_ename;
		EXIT when emp_cursor%ROWCOUNT > 10 or emp_cursor%NOTFOUND;
		dbms_output.put_line(v_empno|| ' | '|| v_ename);
	end	LOOP
	CLOSE emp_cursor;
	dbms_output.put_line('----------------------------------------------------');
END;
/


-- FOR UPDATE CLAUSE : whole record will get updated. 

SET serveroutput on;
DECLARE
	CURSOR emp_cursor IS
		SELECT Emp_id,Last_name,Salary
		from Employees 
		WHERE deptid = 50;
		FOR UPDATE OF Salary NOWAIT;
BEGIN
	UPDATE Employees SET salary = salary +10 where deptid = 50;
END;
/

-- WHERE CURRENT OF CLAUSE : current record will get updated.

SET serveroutput on;
DECLARE
	CURSOR sal_cursor IS
		SELECT Emp_id,Last_name,Salary
		from Employees 
		WHERE deptid = 50;
		FOR UPDATE OF Salary NOWAIT;
BEGIN
	FOR emp_record IN sal_cursor
	LOOP
		IF emp_record.salary < 5000 THEN
			UPDATE Employees SET salary = salary + 500 
			where CURRENT OF sal_cursor;
END;
/

-- Exception Handling -----------------------------------------------------------
--Predefined exception - Implicit
SET serveroutput on;
DECLARE
	v_lastname Employees.Last_name%TYPE;
	v_salary Employees.Salary%TYPE;
BEGIN
	select Last_name,Salary
	into v_lastname, v_salary
	from Employees
	where Emp_id = 300;
	
	dbms_output.put_line(v_lastname|| ' earns '|| v_salary);
EXCEPTION
	WHEN NO_DATA_FOUND THEN
		dbms_output.put_line('No Records');
	WHEN TOO_MANY_ROWS THEN
		dbms_output.put_line('More than 1 Records Found');
	WHEN OTHERS THEN
		dbms_output.put_line('Some Erros found');
END;
/

--User-defined exception - Explicit
SET serveroutput on;
DECLARE
	v_lastname Employees.Last_name%TYPE;
	v_salary Employees.Salary%TYPE;
	e_invalid_dept EXCEPTION;
BEGIN
	UPDATE Employees
	set Salary = Salary + 200
	where deptid = 200;
	
	IF SQL%NOTFOUND THEN
		RAISE e_invalid_dept;
	end if;
	commit;
	
EXCEPTION
	WHEN NO_DATA_FOUND THEN
		dbms_output.put_line('No Records');
	WHEN TOO_MANY_ROWS THEN
		dbms_output.put_line('More than 1 Records Found');
	WHEN e_invalid_dept THEN
		dbms_output.put_line('Not Such Dept. EXISTS');
	WHEN OTHERS THEN
		dbms_output.put_line('Some Erros found');
END;
/



-- PROCEDURE -----------------------------------------------------------

	A subprogram is a program unit/module that performs a particular task.
	--Anonymous PL/SQL block = unnamed block || No Reusability
	--Named PL/SQL block = act as a DB object so that whenever that code is req. we can use it.
	PROCEDURE = A type of Subprogram, Is a DB OBJECT used to perform repeated execution
				Value can be supplied through paarmeters
				Formal parameter is passed whe we define procedure while Actual parameter is passed when we invoke
			Performanace increased since compiled copy is already there

--SYNTAX : 
	CREATE [OR REPLACE] PROCEDURE procedure_name 
	[(parameter_name [IN | OUT | IN OUT] type [, ...])] 
	{IS | AS} 
	BEGIN 
	  < procedure_body > 
	END procedure_name; 
--Eg :
CREATE or REPLACE PROCEDURE test_procedure IS
BEGIN
	dbms_output.put_line('Test Procedure');
END;
/
	
	IN parameters: The IN parameter can be referenced by the procedure or function. 
					The value of the parameter cannot be overwritten by the procedure or the function.
	OUT parameters: The OUT parameter cannot be referenced by the procedure or function, 
					but the value of the parameter can be overwritten by the procedure or function.
	INOUT parameters: The INOUT parameter can be referenced by the procedure or function 
					and the value of the parameter can be overwritten by the procedure or function.


-- IN MODE
CREATE OR REPLACE PROCEDURE add_dept
(
	pdid IN Departments.deptid%TYPE,
	p_dname IN Departments.dept_name%TYPE,
	p_mid IN Departments.manager_id%TYPE,
	p_lid IN Departments.location_id%TYPE
) IS
BEGIN
	INSERT INTO Departments VALUES ( p_did, p_dname, p_mid, p_lid);
	dbms_output.put_line('Dept. Added.');
End;
/
--Run--
	Execute  add_dept(500, 'New Dept', 105, 1000);



-- OUT MODE
CREATE OR REPLACE PROCEDURE add_dept
(
	pdid IN Departments.deptid%TYPE,
	p_dname OUT Departments.dept_name%TYPE,
) IS
BEGIN
	SELECT dept_name 
	into p_dname
	FROM Departments 
	where deptid = p_did;
End;
/
--Run 
DECLARE 
	dname varchar2(20);
BEGIN
	add_dept(600, dname);
	dbms_output.put_line(dname);
END;
/


-- IN OUT MODE
CREATE or REPLACE PROCEDURE format_phone_number( p_phone_no IN OUT varchar2 ) IS
BEGIN
	p_phone_no := '(' || SUBSTR(p_phone_no,1,3) || ')' ||
					SUBSTR(p_phone_no,4,3)|| '-' || 
					SUBSTR(p_phone_no,7,4);
END;
/

--Run
DECLARE
	v_p_no VARCHAR2(15);
BEGIN
	v_p_no := '1234567890';
	format_phone_number(v_p_no);
	dbms_output.put_line(v_p_no);
END;
/


-- FUNCTION -----------------------------------------------------------

IS a named PL/SQL block which returns a VALUE
provides reusability
can be invoked by a select clause, where clause

--Syntax
CREATE [OR REPLACE] FUNCTION function_name 
[(parameter_name  type [, ...])] 

// this statement  is must for functions 
RETURN return_datatype  
{IS | AS} 

BEGIN 
    // program code

[EXCEPTION
   exception_section;

END [function_name];

--Eg: *****************************
 
CREATE or REPLACE FUNCTION get_tax_amount(p_sal number) RETURN number IS 
BEGIN
	return (p_sal * 10/100);
END;
/
--run
select First_name, Last_name, Salary - get_tax_amount(Salary) as Inhand_sal
from Employees;

--Eg: *****************************

DECLARE  
   a number;  
   b number;  
   c number;  
FUNCTION findMax(x IN number, y IN number)   
RETURN number  
IS  
    z number;  
BEGIN  
   IF x > y THEN  
      z:= x;  
   ELSE  
      Z:= y;  
   END IF;  
  
   RETURN z;  
END;   
BEGIN  
   a:= 23;  
   b:= 45;  
  
   c := findMax(a, b);  
   dbms_output.put_line(' Maximum of (23,45): ' || c);  
END;  
/  

-- PACKAGE -----------------------------------------------------------

 It group logically related subprograms. Divided into 2 parts i.e. Specification and BODY
 PUBLIC package constructs are declared in SPECIFICATION
 PRIVATE PACKAGE construct are defined in BODY
 
 PACKAGE itself cannot be invoked
 On the FIrst call of packaged block, whole package is loaded in memory
 
--Creating a Package: 
CREATE OR REPLACE PACKAGE manage_emp IS
	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2);
	PROCEDURE edit_emp(p_id NUMBER, p_name VARCHAR2);
END manage_emp;
/

-- Create a Body:
CREATE OR REPLACE PACKAGE BODY manage_emp IS
	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2) IS
	BEGIN
		dbms_output.put_line('Employee added');
	END add_emp;
	
	PROCEDURE edit_emp(p_id NUMBER, p_name VARCHAR2) IS
	BEGIN
		dbms_output.put_line('Employee Updated');
	END edit_emp;

END;
/

-- Execute PACKAGE

EXECUTE manage_emp.add_emp(201,'Andy');

EXECUTE manage_emp.edit_emp(201,'Ron');
	
	
---Advanced PACKAGE
1.Creating Bodiless PACKAGE in specfication - Just to define some CONSTANT
2.Working with Overloading - Creating Function/Procedure with Same name but different parameter
3.Working with Forward Declaration -  Eg. you have a multiple package or function and within a particular package you are calling some other PACKAGE
				So this particular package has to be atleast declared first, if we have defined the particular package before its fine but if you going to define it later at least you will have to give a forward Declaration
4.Creating One Time Only procedure = invoked when perticular session has started Eg= wanted to set some value which dont want to change later

1. Creating Bodiless PACKAGE

CREATE or REPLACE Package global_constants IS
	mile_to_km CONSTANT NUMBER := 1.609;
	km_to _mile CONSTANT NUMBER := 0.621;
END;
/

execute dbms_output.put_line( 20 * global_constants.mile_to_km );

2.Working with Overloading

CREATE OR REPLACE PACKAGE BODY manage_emp IS
	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2);
	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2, p_sal NUMBER);
	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2, p_sal NUMBER, p_dept NUMBER);

End manage_emp;
/

-- Create a Body:
CREATE OR REPLACE PACKAGE BODY manage_emp IS
	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2) IS
	BEGIN
		dbms_output.put_line('Employee added : 1');
	END add_emp;
	
	PROCEDURE edit_emp(p_id NUMBER, p_name VARCHAR2, p_sal NUMBER) IS
	BEGIN
		dbms_output.put_line('Employee Updated : 2');
	END add_emp;

	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2, p_sal NUMBER, p_dept NUMBER) is 
	BEGIN
		dbms_output.put_line('Employee Updated : 3');
	END add_emp;	

END;
/
-- Execute PACKAGE

EXECUTE manage_emp.add_emp(201,'Andy');

EXECUTE manage_emp.edit_emp(201,'Andy',50000);

EXECUTE manage_emp.edit_emp(201,'Andy',50000,20);

4.Creating One Time Only procedure

--Creating a Package: 
CREATE OR REPLACE PACKAGE manage_emp IS
	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2);
	PROCEDURE edit_emp(p_id NUMBER, p_name VARCHAR2);
END manage_emp;
/

-- Create a Body:
CREATE OR REPLACE PACKAGE BODY manage_emp IS
	PROCEDURE search_emp(p_id NUMBER);

	PROCEDURE add_emp(p_id NUMBER, p_name VARCHAR2) IS
	BEGIN
		dbms_output.put_line('Employee added');
	END add_emp;
	
	PROCEDURE edit_emp(p_id NUMBER, p_name VARCHAR2) IS
	BEGIN
		search_emp(p_id);
		dbms_output.put_line('Employee Updated');
	END edit_emp;

	PROCEDURE search_emp(p_id NUMBER) IS
	BEGIN
		dbms_output.put_line('Employee Found');
	END search_emp;
END;
/


-- Database Triggers -----------------------------------------------------------

It is a named PL/SQL block and planned for particular event and timing
IS executed implicit on that event 
It is designed to perform related actions or to centralize global actions
Excessive use of triggers may result complex interdependencies

 Application TRIGGER
 Database TRIGGER 
	- DML TRIGGER - INSERT/UPDATE/DELETE
	- INSTEAD of TRIGGER - DML operation with the VIEW
	- DDL TRIGGER - restrict to Drop table
	
1. DML TRIGGER

CREATE OR REPLACE TRIGGER emp_insert
AFTER INSERT ON Employees
BEGIN
	dbms_output.put_line('Record Inserted');
end;
/


CREATE OR REPLACE TRIGGER restricted_insert
AFTER INSERT OR UPDATE OR DELETE ON Employees
BEGIN
	IF (TO_CHAR(SYSDATE, 'HH24:MI') NOT BETWEEN '9:00' AND '18:00') THEN
		RAISE_APPLICATION_ERROR(-20123, 'You can manipulate Employee only between 9:00 AM to 6:00 PM.');
	End IF;
end;
/

--Salary CHECK: NEW and OLD keyword can be use with FOR EACH ROW only.
CREATE OR REPLACE TRIGGER salary_update_check
BEFORE UPDATE OF salary ON Employees
FOR EACH ROW
BEGIN
	IF : NEW.salary < OLD.salary THEN
				RAISE_APPLICATION_ERROR(-20125, 'Update salary cannot be less than current salary');
	END IF;
end;
/


2. INSTEAD of TRIGGER  - DML operation with the VIEW
-- new table created tbl_employees
empid	not null	number(4)
firstname			varchar2(20)
lastname			varchar2(20)
salary				number(10,2)
dept				number(3)

tbl_departments:
deptid				number(3)
deptname			varchar2(20)

VIEW created empdata:	empid	firstname	lastname	daptname	salary 

Agenda- when we insert any data in this view(empdata) then record must entered into tbl_employees table

CREATE OR REPLACE TRIGGER emp_dept_insert
INSTEAD OF INSERT on empdata
FOR EACH ROW
DECLARE
	v_did NUMBER;
BEGIN 
	SELECT deptid into v_did from Departments where LOWER(dept_name) = LOWER(:NEW.dept_name);
	
	IF INSERTING THEN
			INSERT INTO tbl_employees VALUES (:NEW.empid, :NEW.firstname, :new.lastname, NEW.salary, v_did);
	END IF;
end;
/

3.DDL TRIGGER

CREATE or REPLACE TRIGGER restrict_drop_table
BEFORE drop on DATABASE
BEGIN
	RAISE_APPLICATION_ERROR(-20125,'Cannot drop table from this database');
END:
/


LOGON and LOGOFF TRIGGER

TABLE NAME - login_details
loginid		varchar2(20)
logintime	DATE
action		varchar2(20)

CREATE OR REPLACE TRIGGER login_trigger
AFTER LOGON ON SCHEMA
BEGIN
	INSERT INTO login_details VALUES(USER, TO_CHAR(SYSDATE, 'DD-MM-YYY HH24:MI:SS'),'Login');
END;
/

CREATE OR REPLACE TRIGGER logoff_trigger
AFTER LOGOFF ON SCHEMA
BEGIN
	INSERT INTO login_details VALUES(USER, TO_CHAR(SYSDATE, 'DD-MM-YYY HH24:MI:SS'),'Logout');
END;
/

testing- 
disc
connect 
Footer

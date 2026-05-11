# SQL Complete Syntax Reference Guide

## 1. SUBQUERIES

### WHERE Clause Subquery
```sql
SELECT column1, column2 
FROM table1 
WHERE column1 = (SELECT column1 FROM table2 WHERE condition);
```

### IN Subquery
```sql
SELECT column1, column2 
FROM table1 
WHERE column1 IN (SELECT column1 FROM table2 WHERE condition);
```

### NOT IN Subquery
```sql
SELECT column1, column2 
FROM table1 
WHERE column1 NOT IN (SELECT column1 FROM table2 WHERE condition);
```

### EXISTS Subquery
```sql
SELECT column1, column2 
FROM table1 t1
WHERE EXISTS (SELECT 1 FROM table2 t2 WHERE t1.column = t2.column);
```

### NOT EXISTS Subquery
```sql
SELECT column1, column2 
FROM table1 t1
WHERE NOT EXISTS (SELECT 1 FROM table2 t2 WHERE t1.column = t2.column);
```

### FROM Clause Subquery
```sql
SELECT column1, column2 
FROM (SELECT column1, column2 FROM table WHERE condition) AS alias
WHERE condition;
```

### SELECT Clause Subquery
```sql
SELECT column1, (SELECT column2 FROM table2 WHERE condition) AS alias
FROM table1;
```

### ANY Subquery
```sql
SELECT column1, column2 
FROM table1 
WHERE column1 > ANY (SELECT column1 FROM table2 WHERE condition);
```

### ALL Subquery
```sql
SELECT column1, column2 
FROM table1 
WHERE column1 > ALL (SELECT column1 FROM table2 WHERE condition);
```

## 2. CASE STATEMENT

### Simple CASE
```sql
SELECT column1,
    CASE column1
        WHEN 'value1' THEN 'result1'
        WHEN 'value2' THEN 'result2'
        ELSE 'other'
    END AS alias
FROM table;
```

### Searched CASE
```sql
SELECT column1,
    CASE 
        WHEN condition1 THEN 'result1'
        WHEN condition2 THEN 'result2'
        ELSE 'result3'
    END AS alias
FROM table;
```

### CASE in WHERE
```sql
SELECT * FROM table
WHERE 
    CASE 
        WHEN condition1 THEN column1 = 'value'
        ELSE column2 = 'value'
    END;
```

### CASE in ORDER BY
```sql
SELECT * FROM table
ORDER BY 
    CASE 
        WHEN column1 = 'value' THEN 1
        ELSE 2
    END;
```

### CASE in UPDATE
```sql
UPDATE table
SET column1 = 
    CASE 
        WHEN condition1 THEN 'value1'
        WHEN condition2 THEN 'value2'
        ELSE column1
    END;
```

## 3. CTE (Common Table Expression)

### Basic CTE
```sql
WITH cte_name AS (
    SELECT column1, column2 FROM table WHERE condition
)
SELECT * FROM cte_name;
```

### Multiple CTEs
```sql
WITH cte1 AS (
    SELECT column1 FROM table1
),
cte2 AS (
    SELECT column2 FROM table2
)
SELECT * FROM cte1 JOIN cte2 ON cte1.column1 = cte2.column2;
```

### Recursive CTE
```sql
WITH RECURSIVE cte_name AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM cte_name WHERE n < 10
)
SELECT * FROM cte_name;
```

### CTE with UPDATE
```sql
WITH cte_name AS (
    SELECT column1, column2 FROM table WHERE condition
)
UPDATE cte_name SET column1 = 'new_value';
```

### CTE with DELETE
```sql
WITH cte_name AS (
    SELECT column1 FROM table WHERE condition
)
DELETE FROM cte_name;
```

## 4. WINDOW FUNCTIONS

### ROW_NUMBER()
```sql
SELECT column1, column2,
    ROW_NUMBER() OVER (ORDER BY column1) AS row_num
FROM table;
```

### RANK()
```sql
SELECT column1, column2,
    RANK() OVER (ORDER BY column1 DESC) AS rank_num
FROM table;
```

### DENSE_RANK()
```sql
SELECT column1, column2,
    DENSE_RANK() OVER (ORDER BY column1 DESC) AS dense_rank_num
FROM table;
```

### SUM() OVER() - Running Total
```sql
SELECT column1, column2,
    SUM(column1) OVER (ORDER BY column2) AS running_total
FROM table;
```

### SUM() OVER() - Partition By
```sql
SELECT column1, column2,
    SUM(column1) OVER (PARTITION BY column2 ORDER BY column3) AS group_total
FROM table;
```

### AVG() OVER()
```sql
SELECT column1, column2,
    AVG(column1) OVER (PARTITION BY column2) AS group_avg
FROM table;
```

### LAG()
```sql
SELECT column1, column2,
    LAG(column1, 1) OVER (ORDER BY column2) AS previous_value
FROM table;
```

### LEAD()
```sql
SELECT column1, column2,
    LEAD(column1, 1) OVER (ORDER BY column2) AS next_value
FROM table;
```

### NTILE()
```sql
SELECT column1, column2,
    NTILE(4) OVER (ORDER BY column1) AS quartile
FROM table;
```

### FIRST_VALUE()
```sql
SELECT column1, column2,
    FIRST_VALUE(column1) OVER (ORDER BY column2) AS first_value
FROM table;
```

### LAST_VALUE()
```sql
SELECT column1, column2,
    LAST_VALUE(column1) OVER (ORDER BY column2 ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_value
FROM table;
```

## 5. STORED PROCEDURE

### Basic Procedure
```sql
DELIMITER //
CREATE PROCEDURE procedure_name()
BEGIN
    SELECT * FROM table;
END //
DELIMITER ;

CALL procedure_name();
```

### Procedure with IN Parameter
```sql
DELIMITER //
CREATE PROCEDURE procedure_name(IN param_name INT)
BEGIN
    SELECT * FROM table WHERE column = param_name;
END //
DELIMITER ;

CALL procedure_name(100);
```

### Procedure with OUT Parameter
```sql
DELIMITER //
CREATE PROCEDURE procedure_name(IN id INT, OUT total INT)
BEGIN
    SELECT SUM(column) INTO total FROM table WHERE column = id;
END //
DELIMITER ;

CALL procedure_name(101, @result);
SELECT @result;
```

### Procedure with INOUT Parameter
```sql
DELIMITER //
CREATE PROCEDURE procedure_name(INOUT value INT)
BEGIN
    SET value = value * 2;
END //
DELIMITER ;

SET @x = 10;
CALL procedure_name(@x);
SELECT @x;
```

### Procedure with IF Statement
```sql
DELIMITER //
CREATE PROCEDURE procedure_name()
BEGIN
    DECLARE var_name INT DEFAULT 0;
    SELECT COUNT(*) INTO var_name FROM table;
    IF var_name > 0 THEN
        SELECT 'Records found' AS message;
    ELSE
        SELECT 'No records' AS message;
    END IF;
END //
DELIMITER ;
```

### Procedure with WHILE Loop
```sql
DELIMITER //
CREATE PROCEDURE procedure_name()
BEGIN
    DECLARE i INT DEFAULT 1;
    WHILE i <= 10 DO
        INSERT INTO table VALUES (i);
        SET i = i + 1;
    END WHILE;
END //
DELIMITER ;
```

### Drop Procedure
```sql
DROP PROCEDURE IF EXISTS procedure_name;
```

## 6. TRIGGER

### BEFORE INSERT Trigger
```sql
DELIMITER //
CREATE TRIGGER trigger_name
BEFORE INSERT ON table_name
FOR EACH ROW
BEGIN
    SET NEW.column = UPPER(NEW.column);
END //
DELIMITER ;
```

### AFTER INSERT Trigger
```sql
DELIMITER //
CREATE TRIGGER trigger_name
AFTER INSERT ON table_name
FOR EACH ROW
BEGIN
    INSERT INTO audit_table VALUES (NEW.column, NOW());
END //
DELIMITER ;
```

### BEFORE UPDATE Trigger
```sql
DELIMITER //
CREATE TRIGGER trigger_name
BEFORE UPDATE ON table_name
FOR EACH ROW
BEGIN
    SET NEW.updated_date = NOW();
END //
DELIMITER ;
```

### AFTER UPDATE Trigger
```sql
DELIMITER //
CREATE TRIGGER trigger_name
AFTER UPDATE ON table_name
FOR EACH ROW
BEGIN
    INSERT INTO log_table VALUES (OLD.column, NEW.column, NOW());
END //
DELIMITER ;
```

### BEFORE DELETE Trigger
```sql
DELIMITER //
CREATE TRIGGER trigger_name
BEFORE DELETE ON table_name
FOR EACH ROW
BEGIN
    INSERT INTO backup_table VALUES (OLD.column, OLD.column2, NOW());
END //
DELIMITER ;
```

### AFTER DELETE Trigger
```sql
DELIMITER //
CREATE TRIGGER trigger_name
AFTER DELETE ON table_name
FOR EACH ROW
BEGIN
    DELETE FROM related_table WHERE foreign_key = OLD.id;
END //
DELIMITER ;
```

### Drop Trigger
```sql
DROP TRIGGER IF EXISTS trigger_name;
```

### Show Triggers
```sql
SHOW TRIGGERS LIKE 'table_name';
```

## 7. REAL EXAMPLES (XYZ_COMPANY Database)

### Subquery Example
```sql
SELECT * FROM Orders 
WHERE TotalAmount > (SELECT AVG(TotalAmount) FROM Orders);
```

### CASE Example
```sql
SELECT OrderID, TotalAmount,
    CASE 
        WHEN TotalAmount < 200 THEN 'Small'
        WHEN TotalAmount BETWEEN 200 AND 500 THEN 'Medium'
        ELSE 'Large'
    END AS OrderSize
FROM Orders;
```

### CTE Example
```sql
WITH CustomerTotal AS (
    SELECT CustomerID, SUM(TotalAmount) AS TotalSpent
    FROM Orders
    GROUP BY CustomerID
)
SELECT c.FirstName, c.LastName, ct.TotalSpent
FROM Customer c
JOIN CustomerTotal ct ON c.CustomerID = ct.CustomerID
WHERE ct.TotalSpent > 600;
```

### Window Function Example
```sql
SELECT CustomerID, SUM(TotalAmount) AS TotalSpent,
    RANK() OVER (ORDER BY SUM(TotalAmount) DESC) AS SpendingRank
FROM Orders
GROUP BY CustomerID;
```

### Stored Procedure Example
```sql
DELIMITER //
CREATE PROCEDURE GetCustomerOrders(IN custID INT)
BEGIN
    SELECT * FROM Orders WHERE CustomerID = custID;
END //
DELIMITER ;

CALL GetCustomerOrders(1);
```

### Trigger Example
```sql
DELIMITER //
CREATE TRIGGER UpdateMembership
AFTER INSERT ON Orders
FOR EACH ROW
BEGIN
    DECLARE total DECIMAL(10,2);
    SELECT SUM(TotalAmount) INTO total 
    FROM Orders WHERE CustomerID = NEW.CustomerID;
    
    UPDATE Customer 
    SET MembershipLevel = CASE
        WHEN total >= 1000 THEN 'Platinum'
        WHEN total >= 500 THEN 'Gold'
        ELSE 'Silver'
    END
    WHERE CustomerID = NEW.CustomerID;
END //
DELIMITER ;

**Use this as a quick reference guide for all SQL syntax needs!**

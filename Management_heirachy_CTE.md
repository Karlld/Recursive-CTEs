**Using a Recursive CTE to show management hierarchy**

Firstly I created a sample table and populated it with several employees, including their ID number, manager ID number and work role.

```sql
CREATE TABLE emp_records (employee_id INT, 
                          emp_name VARCHAR(255), 
                          manager_id INT, 
                          work_role VARCHAR(255));

INSERT INTO emp_records (employee_id, emp_name, manager_id, work_role)  
   VALUES  (1, 'Bob', NULL, 'CEO'),
	 (2, 'Sarah', 1, 'Senior Manager'),
	 (3, 'Tim', 2, 'Customer Manager'),
	 (4, 'Susan', 2, 'Engineer'),
	 (5, 'Clive', 3, 'CA'),
	 (6, 'Thomas', 3, 'CA'),
	 (7, 'Barbara', 3, ‘CA');

SELECT * FROM emp_records;
```

| employee_id | emp_name | manager_id | work_role |
|-------------|----------|------------|-----------|
| 1	 | Bob		| null | CEO | 
| 2	| Sarah |	1	| Senior | Manager |
| 3	| Tim	| 2	| Customer Manager |
| 4	| Susan | 2	| Engineer |
| 5	| Clive 	| 3	| CA | 
| 6	| Thomas 	| 3	| CA |
| 7	| Barbara 	| 3	| CA |


Using this table I created a recursive CTE to return the management hierarchy from the employee named Sarah downwards.

```sql
WITH RECURSIVE emp_heirachy AS (
    
    SELECT employee_id, emp_name, work_role, manager_id
    FROM emp_records
    WHERE emp_name = 'Sarah'

    UNION ALL

    SELECT r.employee_id, r.emp_name, r.work_role, r.manager_id
    FROM emp_heirachy h
    JOIN emp_records r ON r.manager_id = h.employee_id
)

SELECT h2.employee_id, h2.emp_name, e2.emp_name as manager_name 
FROM emp_heirachy AS h2
JOIN emp_records AS e2 ON e2.employee_id = h2.manager_id;

```


| employee_id | emp_name | manager_name |
|-------------|----------|--------------|
| 2	| Sarah | Bob |
| 3	| Tim | Sarah |
| 4	 | Susan | Sarah |
| 5	| Clive | Tim |
| 6	| Thomas | Tim |
| 7	| Barbara |Tim |


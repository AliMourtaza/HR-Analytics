# HR-Analytics
The HR department monitors and manages various aspects of employee data to ensure the organization maintains a healthy workforce. However, clear performance indicators are lacking to track and analyze key HR metrics. Therefore, there is a need to design and implement a set of KPIs to address the following points:

## Create table

```
--Create Table

CREATE TABLE hrdata
(
	emp_no int8 PRIMARY KEY,
	gender varchar(50) NOT NULL,
	marital_status varchar(50),
	age_band varchar(50),
	age int8,
	department varchar(50),
	education varchar(50),
	education_field varchar(50),
	job_role varchar(50),
	business_travel varchar(50),
	employee_count int8,
	attrition varchar(50),
	attrition_label varchar(50),
	job_satisfaction int8,
	active_employee int8
)
```

## Employee Count:

```
SELECT COUNT(employee_count) AS total_employee
FROM hrdata
```
![image](https://github.com/user-attachments/assets/411abdee-042d-416f-9e74-d4195c7ad402)

## Attrition Count:

```
SELECT COUNT(attrition) AS total_attrition
FROM hrdata
WHERE attrition = 'Yes'
```
![image](https://github.com/user-attachments/assets/c7bb1871-947b-4503-b47c-6272eb58d4ce)

## Attrition Rate:

```
SELECT
    ROUND((
        (SELECT COUNT(attrition)
         FROM hrdata
         WHERE attrition = 'Yes')::NUMERIC
        / COUNT(employee_count)::NUMERIC
    )*100,2) AS attrition_rate
FROM hrdata;
```
![image](https://github.com/user-attachments/assets/d0b44c01-6e31-4c67-be88-f7169d2e1144)
## Active Employee:
```
SELECT
	(COUNT(employee_count) - (SELECT COUNT(attrition)
							 FROM hrdata
							 WHERE attrition='Yes')) AS active_employee
FROM hrdata;
```
![image](https://github.com/user-attachments/assets/6bd2a98d-5c3f-4bd4-a188-593d4ffed14e)

## Average Age:
```
SELECT
	ROUND(AVG(age),2) AS average_age
FROM hrdata;
```
![image](https://github.com/user-attachments/assets/0ea058cf-495e-47b1-a04a-63ab3c5546c9)

## Attrition by Gender:
```
SELECT
	gender,
	COUNT(attrition) AS attrition_count
FROM hrdata
WHERE attrition='Yes'
GROUP BY 1
ORDER BY 2 desc;
```
![image](https://github.com/user-attachments/assets/1a7af47f-43be-4359-be63-1c5f09cebe88)

## No of Employee by Age Group:
```
SELECT
	gender,
	COUNT(employee_count)
FROM hrdata
GROUP BY 1
```
![image](https://github.com/user-attachments/assets/b7c79636-330f-4432-be59-332771e33a37)
## Education Field wise Attrition:
```
SELECT
    education_field,
    COUNT(attrition) AS attrition_count,
    ROUND(
        COUNT(attrition)::NUMERIC / 
        (SELECT COUNT(attrition) 
         FROM hrdata 
         WHERE attrition = 'Yes')::NUMERIC * 100, 2
    ) AS pct
FROM hrdata
WHERE attrition = 'Yes'
GROUP BY 1
ORDER BY 2 DESC;
```
![image](https://github.com/user-attachments/assets/0a9b2583-f405-4827-9df6-f43201add27b)

## Attrition Rate by Gender for different Age Group:
```
SELECT
    age_band,
    gender,
    COUNT(attrition) AS attrition_count,
    ROUND(
        COUNT(attrition)::NUMERIC / 
        (SELECT COUNT(attrition) 
         FROM hrdata 
         WHERE attrition = 'Yes')::NUMERIC * 100, 2
    ) AS pct
FROM hrdata
WHERE attrition = 'Yes'
GROUP BY 1, 2
ORDER BY 1, 2 DESC;
```
![image](https://github.com/user-attachments/assets/53eaf9f3-c19e-405c-b9b8-bcc36bf58907)

## Job Satisfaction Rating:
```
CREATE EXTENSION IF NOT EXISTS tablefunc

SELECT *
FROM crosstab(
    $$
    SELECT 
        job_role, 
        job_satisfaction, 
        SUM(employee_count)
    FROM hrdata
    GROUP BY job_role, job_satisfaction
    ORDER BY job_role, job_satisfaction
    $$
) AS ct(
    job_role VARCHAR(50), 
    one NUMERIC, 
    two NUMERIC, 
    three NUMERIC, 
    four NUMERIC
)
ORDER BY job_role;
```
![image](https://github.com/user-attachments/assets/1b1f7c37-4014-4e42-9b8a-2ac73499bbe3)

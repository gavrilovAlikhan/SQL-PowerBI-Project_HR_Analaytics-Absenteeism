# HR Analytics Absenteeism Project - SQL & Power BI
This project involves analysing employee absenteeism, health, and lifestyle data to create a comprehensive database and interactive Power BI dashboard. The aim is to identify candidates for a bonus program based on health metrics and calculate wage increases for non-smokers. Steps include importing data into SQL Server, optimising queries, and designing visuals following a HR wireframe. Interactive filters and a smart narrative section will enhance data interpretation.

# Data Source:
The source data contained Human Resource 741 records. This is included in the repository.

# Data Analysis
This project was done on Microsoft SQL Server Management Studio

# Data Visualisation
Data visualisation was done in Microsoft Power BI. [View Dashboard](https://www.novypro.com/project/hr-analytics-absenteeism) 

![HR_Absenteeism_Dashboard](https://github.com/gavrilovAlikhan/SQL-PowerBI-Project_HR_Analaytics-Absenteeism/assets/123990359/185bf6f3-ebd8-4831-908f-83bf00fb1d02)

# HR Request to Data Analysis Team
- Provide a list of Healthy Individuals & Low Absenteeism for our healthy bonus program – Total Budget $1000
-	Calculate a Wage Increase or annual compensation for Non-Smokers for (Insurance Budget of $983,221 for all Non-Smokers)
-	Create a Dashboard for HR to understand Absenteeism at work based on approved wireframe.

# Data Analysis Tasks
1. Build a Database
2. Develop SQL Query
3. Perform Analysis
4. Connect Database to PowerBI
5. Build a Dashboard (Wireframe)

# Exploratory Data Analysis

### 1) Create Databse
- Right-click on Databases > New Database
- Name the Database (HR_Project) > Click OK

![create_databse](https://github.com/gavrilovAlikhan/SQL-PowerBI-Project_HR_Analaytics-Absenteeism/assets/123990359/ff29d402-3dab-4633-b878-8aaf2e0b67f8)

### 2) Import Data
- Right-click on HR_Project > Tasks > Import Flat File
- Import Absenteeism_at_work.csv, Reasons.csv, compensation.csv
- Verify that the import worked:

![import_data](https://github.com/gavrilovAlikhan/SQL-PowerBI-Project_HR_Analaytics-Absenteeism/assets/123990359/0a46f584-e7e0-4dbd-912b-b7dad5cff377)

### 3) QUERIES
- Create initial query
``` SQL
SELECT * 
FROM Absenteeism_at_work a
LEFT JOIN compensation b 
ON a.ID = b.ID
LEFT JOIN Reasons r
ON a.Reason_for_absence = r.Number;
```

- Find the healthiest
``` SQL
SELECT *
FROM Absenteeism_at_work
WHERE Social_drinker = 0 AND Social_smoker = 0
AND Body_mass_index < 25 AND 
Absenteeism_time_in_hours < (SELECT AVG(Absenteeism_time_in_hours) FROM Absenteeism_at_work) -- less than avg absence;
```

- Find all non-smokers
``` SQL
SELECT COUNT(*)
AS nonsmokers
FROM Absenteeism_at_work
WHERE Social_smoker = 0; 
```

The query identified 686 non-smoker employees, enabling the calculation of the budget based on their total hours worked.

Step-By-Step Calculation:
1. Initial Budget from HR: $983,221
2. Number of Employees: 686
3. Hours Worked per Year per Employee: 5 days/week * 8 hours/day * 52 weeks/year = 2,080 hours/year
4. Total Hours Worked by All Employees: 686 employees * 2,080 hours/year = 1,425,280 hours/year
5. Hourly Increase Calculation: $983,221 / 1,425,280 hours = $0.68 per hour increase
6. Yearly Increase per Employee: $0.68/hour * 2,080 hours/year = $1,414.40

- Final Query for Power BI
``` SQL
SELECT 
a.ID,
r.Reason,
Month_of_absence,
Body_mass_index,
CASE WHEN Body_mass_index < 18.5 THEN 'Underweight'
	WHEN Body_mass_index BETWEEN 18.5 AND 25 THEN 'Healthy Weight'
	WHEN Body_mass_index BETWEEN 25 AND 30 THEN 'Overweight'
	WHEN Body_mass_index > 30 THEN 'Obese'
	ELSE 'Unknown' END
	AS BMI_Category,
CASE WHEN Month_of_absence IN (12,1,2) THEN 'Winter'
	WHEN Month_of_absence IN (3,4,5) THEN 'Spring'
	WHEN Month_of_absence IN (6,7,8) THEN 'Summer'
	WHEN Month_of_absence IN (9,10,11) THEN 'Fall'
	ELSE 'Unknown' END 
	AS Season_Names,
Seasons,
Month_of_absence,
Day_of_the_week,
Transportation_expense,
Education,
Son,
Social_drinker,
Social_smoker,
Pet,
Disciplinary_failure,
Age,
Work_load_Average_day,
Absenteeism_time_in_hours
FROM Absenteeism_at_work a
LEFT JOIN compensation b 
ON a.ID = b.ID
LEFT JOIN Reasons r
ON a.Reason_for_absence = r.Number;
```
### 4) Connect Database to PowerBI
1. Navigate to the "Get Data" tab in Power BI > Click on "More" in the "Get Data" tab
2. Type "SQL" and select "SQL Server Databases" > Identify the server name and database name > Utilize the advanced settings option to enter an SQL query > OK

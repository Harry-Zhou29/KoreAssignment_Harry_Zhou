# ETL Process with SSIS README

## Overview:
This SSIS solution is designed to perform an ETL (Extract, Transform, Load) process to move data from a CSV file to a production SQL Server table via a staging table.

## Prerequisites:
- SQL Server 2019.
- SQL Server Data Tools (SSDT) 2019.
- Microsoft Visual Studio 2019 with SSIS packages.
- The CSV file is located at: `E:\KoreAssignment_Harry_Zhou\Data_source\User_Source_File`. Alternatively, you can find it in this GitHub Data_Source repository.

## Setup Instructions:
1. Clone or download this repository to your local machine.

2. Open the solution file `KoreAssignment_Harry_Zhou.sln` in SQL Server Data Tools or Visual Studio.

3. Download the SQL script from the Script folder and execute it in SQL Server to create a database schema and initialize the baseline data.

### Note
The SQL script provided below represents the modifications made from the original task document.
```
-- Modify Prod_Users table schema by adding NOT NULL constraints to certain columns.
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'prod.Users') AND type in (N'U'))
BEGIN
    CREATE TABLE prod.Users (
        ID INT IDENTITY(1,1) PRIMARY KEY,
        UserID INT NOT NULL,-- add not null constraints
        FullName NVARCHAR(255) NOT NULL, --add not null constraints
        Age INT NOT NULL, ---add not null constraints
        Email NVARCHAR(255),
        RegistrationDate DATE NOT NULL,--add not null constraints
        LastLoginDate DATE,
        PurchaseTotal FLOAT,
        RecordLastUpdated DATETIME DEFAULT GETDATE()
    );
END
GO
-- Create stg.Users_DirtyData table to isolate dirty data for further processing.
IF NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'stg.Users_DirtyData') AND type in (N'U'))
BEGIN
    CREATE TABLE stg.Users_DirtyData (
        StgID INT IDENTITY(1,1) PRIMARY KEY,
        UserID INT,
        FullName NVARCHAR(255),
        Age INT,
        Email NVARCHAR(255),
        RegistrationDate DATE,
        LastLoginDate DATE,
        PurchaseTotal FLOAT
    );
END

```

4. Ensure that the necessary SQL Server connection manager is configured in the SSIS package. If not, create a new connection manager pointing to your SQL Server instance.

5. Verify and update the SSIS package configurations if needed. Configuration details such as file paths, table names, and column mappings can be adjusted in the package.

## Execution Steps:
1. Locate the provided CSV file containing the source data. If the file is not already in the designated folder, move it to the appropriate location as specified in the SSIS package configurations.

2. Build the SSIS project to ensure that all components are compiled successfully.

3. Open the SSIS package `KoreAssignment_Harry_Zhou_Package.dtsx` in SQL Server Data Tools or Visual Studio.

4. Execute the SSIS package by either:
   - Right-clicking on the package in Solution Explorer and selecting "Execute Package".
   - Pressing F5 to start debugging.

5. Monitor the SSIS package execution progress and check for any errors or warnings in the Output window.

6. Once the package execution completes successfully, verify the data in the production SQL Server table to ensure that the ETL process was executed correctly.

## Additional Notes:
- Before loading the CSV file into the Staging table, a validation process is applied to ensure the CSV file format is correct. If there are any changes to the column names or additional columns in the CSV file, this validation process will automatically generate log files in the Logs repository. These log files provide detailed information for review and verification purposes.
- During the loading of the CSV file into the Staging table, the following conversions are applied:

1. **Age Column:**
   - If the Age column is null, empty, or contains the string "null", it is converted to 0.

2. **UserID Column:**
   - If the UserID column is null, it is converted to -1.

3. **LastLoginDate Column:**
   - If the LastLoginDate column is null, it is converted to '1900-01-01'.





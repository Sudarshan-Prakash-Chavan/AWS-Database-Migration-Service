# AWS-Database-Migration-Service
This project demonstrates how to use AWS Database Migration Service (DMS) to migrate a MariaDB database from an Amazon RDS instance to an S3 bucket and vice versa. The project details the setup, configuration, and execution of the migration tasks, ensuring a smooth transition of data between these two services.

## Overview

AWS Database Migration Service (DMS) facilitates quick and secure migrations of databases from on-premises to AWS, between AWS services, or to other cloud providers. This project specifically focuses on:

- Migrating a MariaDB database from Amazon RDS to an S3 bucket for data archiving or processing.

- Migrating data back from the S3 bucket to a MariaDB instance on RDS.

## Architecture

![AWS-DMS](https://github.com/user-attachments/assets/bc1d2630-0568-4545-a914-a2e12947a9bc)

- **Amazon RDS (MariaDB)**: A managed relational database service that allows you to set up, operate, and scale a relational database in the cloud.
- **AWS DMS**: A service that helps to migrate databases to AWS quickly and securely. It handles the complexities of the migration process.
- **Amazon S3**: A scalable storage solution that serves as the target for the data being migrated from RDS.
- **MySQL Workbench**: A graphical tool for working with MySQL and MariaDB databases that allows you to manage your RDS instance and execute SQL commands.
- **IAM Role (DMSRDSS3Role)**: An IAM role that grants DMS the necessary permissions to read from RDS and write to S3.

## Pre-requisites

1. **AWS Account**: Ensure you have an AWS account with permissions to create RDS, S3, and DMS resources.
2. **MySQL Workbench**: Download and install MySQL Workbench from [MySQL Workbench Download](https://dev.mysql.com/downloads/installer/).
3. **IAM Role**: Create an IAM role named `DMSRDSS3Role` with the necessary permissions for DMS to access your RDS instance and S3 bucket.

## Migration from RDS to S3

### Step-by-Step Instructions

1. **Create a Replication Instance**:
   - Navigate to the AWS DMS Console.
   - Click on "Replication Instances" and then "Create".
   - Choose instance class, allocated storage, and other settings according to your requirements.
   - Click "Create" to initiate the replication instance setup.

2. **Create an RDS MariaDB Instance**:
   - In the AWS Management Console, navigate to RDS and create a new database instance.
   - Choose MariaDB as the engine.
   - Set the master username and password (use `Admin1234` for simplicity).
   - Select the default VPC and Security Group.
   - Click "Create database".
  
3. **Connect to RDS using MySQL Workbench**:
   - Open MySQL Workbench on your local machine.
   - Create a new connection with your RDS instance's endpoint.
   - Test the connection; if successful, click OK to connect.
  
4. **Run SQL Commands**:
   - In the SQL editor of MySQL Workbench, execute the following commands to set up your database and table:
   ```sql
   CREATE DATABASE test;
   USE test;
   CREATE TABLE employee (id INT, name VARCHAR(20));
   INSERT INTO employee (id, name) VALUES (1231232, 'Rahul');
   INSERT INTO employee (id, name) VALUES (1000005, 'Anup');
   INSERT INTO employee (id, name) VALUES (1100105, 'Anil');

5.  **Create an S3 Bucket:**

  -  In the AWS Management Console, navigate to S3.
  -  Click "Create bucket", provide a name, and choose the same region as your RDS instance.
  -  Configure any additional settings as needed and click "Create".

6.  **Create a Source Endpoint:**

  -  In the DMS Console, navigate to "Endpoints" and click "Create endpoint".
  -  Select "Source endpoint" and fill in the details for your RDS instance.
  -  Use the password `Admin1234` and test the connection to ensure it's configured correctly.

7.  **Create a Target Endpoint:**

  -  Again, in the DMS Console, create a new endpoint but select "Target endpoint".
  -  For the target, select S3 and provide the necessary details, including the IAM role ARN (e.g., `arn:aws:iam::YOUR_ACCOUNT_ID:role/DMSRDSS3Role`).
  -  Test the connection and click "Create endpoint".

8.  **Create the Migration Task:**

  -  In the DMS Console, navigate to "Database migration tasks" and click "Create task".
  -  Select your replication instance and source/target endpoints.
  -  Add a selection rule specifying the schema name (e.g., `test`) to include the relevant data.
  -  Click "Create task" to initiate the migration.

9.  **Monitor Migration:**

  -  Check the status of your migration task in the DMS Console. It should transition through various states: Creating, Ready, Starting, Running, and Load Completed.

10.  **Verify Migrated Data:**

  -  Navigate to your S3 bucket and check for a directory structure containing the database and table names. You should find CSV files with the migrated data.

##  Migration from S3 to RDS

###  Step-by-Step Instructions

1.  **Prepare S3 Bucket:**

  -  Ensure the directory structure exists in your S3 bucket, e.g., `<Bucket>/testdata/test1/`.
  -  Upload a file named `test.csv` containing the data you wish to migrate.

2.  **Create S3 Source Endpoint:**

  -  In the DMS Console, create a new source endpoint for S3.
  -  Use the JSON template provided below to define the table structure:
  
    {
        "TableCount": "1",
        "Tables": [
            {
                "TableName": "test1",
                "TablePath": "testdata/test1/",
                "TableOwner": "testdata",
                "TableColumns": [
                    {
                        "ColumnName": "S.No",
                        "ColumnType": "INT8",
                        "ColumnNullable": "false",
                        "ColumnIsPk": "false"
                    },
                    {
                        "ColumnName": "Name",
                        "ColumnType": "STRING",
                        "ColumnLength": "50"
                    },
                    {
                        "ColumnName": "Id",
                        "ColumnType": "INT8",
                        "ColumnLength": "50"
                    },
                    {
                        "ColumnName": "location",
                        "ColumnType": "STRING",
                        "ColumnLength": "10"
                    },
                    {
                        "ColumnName": "stream",
                        "ColumnType": "STRING",
                        "ColumnLength": "50"
                    }
                ],
                "TableColumnsTotal": "5"
            }
        ]
    }

  -  Test the connection and click "Create endpoint".

3.  **Create RDS Target Endpoint:**

  -  Create a new target endpoint for RDS using the same steps as before, ensuring to specify the password `Admin1234`.

4.  **Create the Migration Task:**

  -  In the DMS Console, create a new migration task.
  
  -  Add a selection rule with a wildcard to copy all data, and keep the other settings as default.
  
  -  Click "Create task".

5.  **Verify Migration:**

  -  Once the migration task completes, switch back to MySQL Workbench.

  -  Query the RDS instance to verify that the data from S3 has been migrated successfully.

##  Conclusion

  This project provides a comprehensive guide to using AWS Database Migration Service for migrating data between an RDS instance and an S3 bucket. By following the   outlined steps, you can effectively set up, configure, and execute migrations, ensuring data integrity and security.

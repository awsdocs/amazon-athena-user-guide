# Using the AWS CLI to recreate an AWS Glue database and its tables<a name="glue-recreate-db-and-tables-cli"></a>

Renaming a AWS Glue database directly is not possible, but you can copy its definition, modify the definition, and use the definition to recreate the database with a different name\. Similarly, you can copy the definitions of the tables in the old database, modify the definitions, and use the modified definitions to recreate the tables in the new database\.

The following procedure for Windows assumes that your AWS CLI is configured for JSON output\. To change the default output format in the AWS CLI, run `aws configure`\.

**To copy an AWS Glue Database using the AWS CLI**

1. At a command prompt, run the following AWS CLI command to retrieve the definition of the AWS Glue database that you want to copy\.

   ```
   aws glue get-database --name database_name
   ```

   For more information about the `get-database` command, see [get\-database](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/glue/get-database.html)\.

1. Save the JSON output to a file with the name of the new database \(for example, `new_database_name.json`\) to your desktop\.

1. Open the `new_database_name.json` file in a text editor\.

1. In the JSON file, change the `Name` entry to the new database name\.

1. Save the file\.

1. At a command prompt, run the following AWS CLI command to use the modified database definition file to create the database with the new name\.

   ```
   aws glue create-database --database-input "file://~/Desktop\new_database_name.json"
   ```

   For more information about the `create-database` command, see [create\-database](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/glue/create-database.html)\. For information about loading AWS CLI parameters from a file, see [Loading AWS CLI parameters from a file](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-file.html) in the *AWS Command Line Interface User Guide*\.

1. To verify that the new database has been created in AWS Glue, run the following command:

   ```
   aws glue get-database --name new_database_name
   ```

Now you are ready to get the definition for a table that you want to copy to the new database, modify the definition, and use the modified definition to recreate the table in the new database\. This procedure does not change the table name\.

**To copy an AWS Glue table using the AWS CLI**

1. At a command prompt, run the following AWS CLI command\.

   ```
   aws glue get-table --database-name database_name --name table_name
   ```

   For more information about the `get-table` command, see [get\-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/glue/get-table.html)\.

1. Save the JSON output to a file with the name of the table \(for example, *table\_name*\.json\) to your Windows desktop\.

1. Open the file in a text editor\.

1. In the JSON file, remove the outer `{"Table": ` entry and the corresponding closing brace `}` at the end of the file\.

1. In the JSON file, remove the following entries and their values:
   + `DatabaseName` – This entry is not required because the `create-table` CLI command uses the `--database-name` parameter\.
   + `CreateTime`
   + `UpdateTime`
   + `CreatedBy`
   + `IsRegisteredWithLakeFormation`
**Note**  
Including any of the above entries causes the `create-table` command to fail\.

1. Save the table definition file\.

1. At a command prompt, run the following AWS CLI command to recreate the table in the new database:

   ```
   aws glue create-table --database-name new_database_name --table-input "file://~/Desktop\table_name.json"     
   ```

   For more information about the `create-table` command, see [create\-table](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/glue/create-table.html)\.

   The table now appears in the new database in AWS Glue and can be queried from Athena\.

1. Repeat the steps to copy each additional table to the new database in AWS Glue\.
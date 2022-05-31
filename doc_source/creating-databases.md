# Creating databases in Athena<a name="creating-databases"></a>

A database in Athena is a logical grouping for tables you create in it\. Creating a database in the Athena console query editor is straightforward\.

**To create a database using the Athena query editor**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. On the **Editor** tab, enter the Hive data definition language \(DDL\) command `CREATE DATABASE myDataBase`\. Replace *myDatabase* with the name that you want to use\. For restrictions on database names, see [Names for tables, databases, and columns](tables-databases-columns-names.md)\.

1. Choose **Run** or press **Ctrl\+ENTER**\.

1. To make your database the current database, select it from the **Database** menu on the left of the query editor\.

For information on controlling permissions to Athena databases, see [Fine\-grained access to databases and tables in the AWS Glue Data Catalog](fine-grained-access-to-glue-resources.md)\.
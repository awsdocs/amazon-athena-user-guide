# Creating Databases in Athena<a name="creating-databases"></a>

A database in Athena is a logical grouping for tables you create in it\. Creating a database in the Athena console Query Editor is straightforward\.

**To create a database using the Athena Query Editor**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. On the **Query Editor** tab, enter the Hive data definition language \(DDL\) command `CREATE DATABASE myDataBase`\. Replace *myDatabase* with the name of the database that you want to create\.  
![\[Creating a database in the Athena Query Editor.\]](http://docs.aws.amazon.com/athena/latest/ug/images/createdatabase.png)

1. Choose **Run Query** or press **Ctrl\+ENTER**\.

1. To make your database the current database, select it from from the **Database** menu\.  
![\[Choosing a database in the Athena console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/catalogdashboard.png)
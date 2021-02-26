# Fine\-Grained Access to Databases and Tables in the AWS Glue Data Catalog<a name="fine-grained-access-to-glue-resources"></a>

If you use the AWS Glue Data Catalog with Amazon Athena, you can define resource\-level policies for the following Data Catalog objects that are used in Athena: databases and tables\.

You define resource\-level permissions in IAM identity\-based policies\.

**Important**  
This section discusses resource\-level permissions in IAM identity\-based policies\. These are different from resource\-based policies\. For more information about the differences, see [Identity\-Based Policies and Resource\-Based Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html) in the *AWS Identity and Access Management User Guide*\.

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

See the following topics for the corresponding tasks: 


| To perform this task | See the following topic | 
| --- | --- | 
| Create an IAM policy that defines fine\-grained access to resources | [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the AWS Identity and Access Management User Guide\. | 
| Learn about IAM identity\-based policies used in AWS Glue | [ Identity\-Based Policies \(IAM Policies\)](https://docs.aws.amazon.com/glue/latest/dg/using-identity-based-policies.html) in the AWS Glue Developer Guide\.  | 

 **In this section** 
+  [Limitations](#access-to-glue-resources-limitations) 
+  [Mandatory: Access Policy to the Default Database and Catalog per AWS Region](#full-access-to-default-db-per-region) 
+  [Table Partitions and Versions in AWS Glue](#access-to-glue-resources-table-partitions-and-versions) 
+  [Fine\-Grained Policy Examples](#examples-fine-grained-table-database-policies) 

## Limitations<a name="access-to-glue-resources-limitations"></a>

Consider the following limitations when using fine\-grained access control with the AWS Glue Data Catalog and Athena:
+ You can limit access only to databases and tables\. Fine\-grained access controls apply at the table level and you cannot limit access to individual partitions within a table\. For more information, see [Table Partitions and Versions in AWS Glue](#access-to-glue-resources-table-partitions-and-versions)\.
+ Athena does not support cross\-account access to the AWS Glue Data Catalog\. 
+ The AWS Glue Data Catalog contains the following resources: `CATALOG`, `DATABASE`, `TABLE`, and `FUNCTION`\. 
**Note**  
From this list, resources that are common between Athena and the AWS Glue Data Catalog are `TABLE`, `DATABASE`, and `CATALOG` for each account\. `Function` is specific to AWS Glue\. For delete actions in Athena, you must include permissions to AWS Glue actions\. See [Fine\-Grained Policy Examples](#examples-fine-grained-table-database-policies)\.

   The hierarchy is as follows: `CATALOG` is an ancestor of all `DATABASES` in each account, and each `DATABASE` is an ancestor for all of its `TABLES` and `FUNCTIONS`\. For example, for a table named `table_test` that belongs to a database `db` in the catalog in your account, its ancestors are `db` and the catalog in your account\. For the `db` database, its ancestor is the catalog in your account, and its descendants are tables and functions\. For more information about the hierarchical structure of resources, see [List of ARNs in Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/glue-specifying-resource-arns.html#data-catalog-resource-arns) in the *AWS Glue Developer Guide*\. 
+ For any non\-delete Athena action on a resource, such as `CREATE DATABASE`, `CREATE TABLE`, `SHOW DATABASE`, `SHOW TABLE`, or `ALTER TABLE`, you need permissions to call this action on the resource \(table or database\) and all ancestors of the resource in the Data Catalog\. For example, for a table, its ancestors are the database to which it belongs, and the catalog for the account\. For a database, its ancestor is the catalog for the account\. See [Fine\-Grained Policy Examples](#examples-fine-grained-table-database-policies)\. 
+ For a delete action in Athena, such as `DROP DATABASE` or `DROP TABLE`, you also need permissions to call the delete action on all anscestors and descendants of the resource in the Data Catalog\. For example, to delete a database you need permissions on the database, the catalog, which is its ancestor, and all the tables and user defined functions, which are its descendents\. A table does not have descendants\. To run `DROP TABLE`, you need permissions to this action on the table, the database to which it belongs, and the catalog\. See [Fine\-Grained Policy Examples](#examples-fine-grained-table-database-policies)\.
+ When limiting access to a specific database in the Data Catalog, you must also specify the access policy to the `default` database and catalog for each AWS Region for `GetDatabase` and `CreateDatabase` actions\. If you use Athena in more than one Region, add a separate line to the policy for the resource ARN for each `default` database and catalog in each Region\. 

  For example, to allow `GetDatabase` access to `example_db` in the `us-east-1` \(N\.Virginia\) Region, also include the `default` database and catalog in the policy for that Region for two actions: `GetDatabase` and `CreateDatabase`:

  ```
  {
     "Effect": "Allow",
     "Action": [
        "glue:GetDatabase", 
        "glue:CreateDatabase"
     ],
     "Resource": [
       "arn:aws:glue:us-east-1:123456789012:catalog",
       "arn:aws:glue:us-east-1:123456789012:database/default",
       "arn:aws:glue:us-east-1:123456789012:database/example_db"
     ]
  }
  ```

## Mandatory: Access Policy to the `Default` Database and Catalog per AWS Region<a name="full-access-to-default-db-per-region"></a>

For Athena to work with the AWS Glue Data Catalog, the following access policy to the `default` database and to the AWS Glue Data Catalog per AWS Region for `GetDatabase` and `CreateDatabase` must be present :

```
{
   "Effect": "Allow",
   "Action": [
      "glue:GetDatabase", 
      "glue:CreateDatabase"
   ],
   "Resource": [
     "arn:aws:glue:us-east-1:123456789012:catalog",
     "arn:aws:glue:us-east-1:123456789012:database/default"
   ]
}
```

## Table Partitions and Versions in AWS Glue<a name="access-to-glue-resources-table-partitions-and-versions"></a>

In AWS Glue, tables can have partitions and versions\. Table versions and partitions are not considered to be independent resources in AWS Glue\. Access to table versions and partitions is given by granting access on the table and ancestor resources for the table\. 

For the purposes of fine\-grained access control, the following access permissions apply:
+ Fine\-grained access controls apply at the table level\. You can limit access only to databases and tables\. For example, if you allow access to a partitioned table, this access applies to all partitions in the table\. You cannot limit access to individual partitions within a table\. 
**Important**  
Having access to all partitions within a table is not sufficient if you need to run actions in AWS Glue on partitions\. To run actions on partitions, you need permissions for those actions\. For example, to run `GetPartitions` on table `myTable` in the database `myDB`, you need permissions for the action ` glue:GetPartitions` in the Data Catalog, the `myDB` database, and `myTable`\. 
+ Fine\-grained access controls do not apply to table versions\. As with partitions, access to previous versions of a table is granted through access to the table version APIs in AWS Glue on the table, and to the table ancestors\.

For information about permissions on AWS Glue actions, see [AWS Glue API Permissions: Actions and Resources Reference](https://docs.aws.amazon.com/glue/latest/dg/api-permissions-reference.html) in the *AWS Glue Developer Guide*\. 

## Examples of Fine\-Grained Permissions to Tables and Databases<a name="examples-fine-grained-table-database-policies"></a>

The following table lists examples of IAM identity\-based policies that allow fine\-grained access to databases and tables in Athena\. We recommend that you start with these examples and, depending on your needs, adjust them to allow or deny specific actions to particular databases and tables\.

These examples include the access policy to the `default` database and catalog, for `GetDatabase` and `CreateDatabase` actions\. This policy is required for Athena and the AWS Glue Data Catalog to work together\. For multiple AWS Regions, include this policy for each of the `default` databases and their catalogs, one line for each Region\. 

In addition, replace the `example_db` database and `test` table names with the names for your databases and tables\.


| DDL Statement | Example of an IAM access policy granting access to the resource | 
| --- | --- | 
| CREATE DATABASE | Allows you to create the database named example\_db\.<pre>{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />      "glue:CreateDatabase"<br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/default",<br />     "arn:aws:glue:us-east-1:123456789012:database/example_db"<br />   ]<br /> }<br /></pre> | 
| ALTER DATABASE | Allows you to modify the properties for the example\_db database\.<pre>{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />      "glue:CreateDatabase"<br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/default"<br />   ]<br />},<br />{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />      "glue:UpdateDatabase"<br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/example_db"<br />   ]<br /> }</pre> | 
| DROP DATABASE | Allows you to drop the example\_db database, including all tables in it\.<pre>{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />      "glue:CreateDatabase"<br />   ],<br />   "Resource": [<br />    "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/default"<br />   ]<br />},<br />{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase",<br />      "glue:DeleteDatabase",<br />      "glue:GetTables", <br />      "glue:GetTable", <br />      "glue:DeleteTable" <br />   ],<br />   "Resource": [<br />    "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/example_db", <br />     "arn:aws:glue:us-east-1:123456789012:table/example_db/*", <br />     "arn:aws:glue:us-east-1:123456789012:userDefinedFunction/example_db/*"<br />   ]<br /> }</pre> | 
| SHOW DATABASES | Allows you to list all databases in the AWS Glue Data Catalog\.<pre>{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />       "glue:CreateDatabase"<br /><br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/default"<br />   ]<br />}, <br />{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabases"<br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/*"<br />   ]<br /> }</pre> | 
| CREATE TABLE | Allows you to create a table named test in the example\_db database\.<pre>{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />      "glue:CreateDatabase"<br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/default"<br />   ]<br />},<br /> {<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase",<br />      "glue:GetTable",<br />      "glue:CreateTable"     <br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/example_db",<br />     "arn:aws:glue:us-east-1:123456789012:table/example_db/test"<br />   ]<br /> }</pre> | 
| SHOW TABLES | Allows you to list all tables in the example\_db database\.<pre>{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />      "glue:CreateDatabase"<br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/default"<br />   ]<br />},<br /> {<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase",<br />      "glue:GetTables"    <br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/example_db",  <br />     "arn:aws:glue:us-east-1:123456789012:table/example_db/*"<br />   ]<br /> }</pre> | 
| DROP TABLE | Allows you to drop a partitioned table named test in the example\_db database\. If your table does not have partitions, do not include partition actions\.<pre>{<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase", <br />      "glue:CreateDatabase"<br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/default"<br />   ]<br />},<br /> {<br />   "Effect": "Allow",<br />   "Action": [<br />      "glue:GetDatabase",<br />      "glue:GetTable",<br />      "glue:DeleteTable", <br />      "glue:GetPartitions",<br />      "glue:GetPartition",<br />      "glue:DeletePartition" <br />   ],<br />   "Resource": [<br />     "arn:aws:glue:us-east-1:123456789012:catalog",<br />     "arn:aws:glue:us-east-1:123456789012:database/example_db",<br />     "arn:aws:glue:us-east-1:123456789012:table/example_db/test"<br />   ]<br /> }</pre> | 
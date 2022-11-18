# Accessing Amazon Athena<a name="accessing-athena"></a>

 You can access Amazon Athena using the AWS Management Console, the Amazon Athena API, or the AWS CLI\. For information about IAM policies in Athena, see [Identity and access management in Athena](security-iam-athena.md)\.

## Using the console<a name="console-athena"></a>

You can use the AWS Management Console for Amazon Athena to do the following:
+ Create or select a database\. 
+ Create, view, and delete tables\. 
+ Filter tables by starting to type their names\.
+ Preview tables and generate CREATE TABLE DDL for them\.
+ Show table properties\.
+ Run queries on tables, save and format queries, and view query history\.
+ Create up to ten queries using different query tabs in the query editor\. To open a new tab, click the plus sign\.
+ Display query results, save, and export them\.
+ Access the AWS Glue Data Catalog\.
+ View and change settings, such as view the query result location, configure auto\-complete, and encrypt query results\.

In the right pane, the Query Editor displays an introductory screen that prompts you to create your first table\. You can view your tables under **Tables** in the left pane\.

 Here's a high\-level overview of the actions available for each table:
+ **Preview tables** – View the query syntax in the Query Editor on the right\. 
+ **Show properties** – Show a table's name, its location in Amazon S3, input and output formats, the serialization \(SerDe\) library used, and whether the table has encrypted data\.
+ **Delete table** – Delete a table\.
+ **Generate CREATE TABLE DDL** – Generate the query behind a table and view it in the query editor\.

## Using the API<a name="Using-API"></a>

 Amazon Athena enables application programming for Athena\. For more information, see [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. The latest AWS SDKs include support for the Athena API\. 

For examples of using the AWS SDK for Java with Athena, see [Code Samples](code-samples.md)\. 

For more information about AWS SDK for Java documentation and downloads, see the *SDKs* section in [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\. 

## Using the CLI<a name="Using-CLI"></a>

 You can access Amazon Athena using the AWS CLI\. For more information, see the [AWS CLI reference for Athena](https://docs.aws.amazon.com/cli/latest/reference/athena/)\. 
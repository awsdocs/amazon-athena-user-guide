# AWS service integrations with Athena<a name="athena-aws-service-integrations"></a>

You can use Athena to query data from the AWS services listed in this section\. To see the Regions that each service supports, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *Amazon Web Services General Reference*\.

**AWS services integrated with Athena**
+ [AWS CloudFormation](#integ-ate-cfn)
+ [Amazon CloudFront](#integ-ate-cf)
+ [AWS CloudTrail](#integ-ate-ct)
+ [Elastic Load Balancing](#integ-ate-eb)
+ [AWS Glue Data Catalog](#integ-ate-gc)
+ [AWS Identity and Access Management \(IAM\)](#integ-ate-iam)
+ [Amazon QuickSight](#integ-ate-qs)
+ [Amazon S3 Inventory](#integ-ate-s3)
+ [AWS Step Functions](#integ-ate-sf)
+ [AWS Systems Manager Inventory](#integ-ate-sys)
+ [Amazon Virtual Private Cloud](#integ-ate-vpc)

For information about each integration, see the following sections\.

**AWS CloudFormation**    
*Data Catalog*  
Reference topic: [AWS::Athena::DataCatalog](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-datacatalog.html) in the *AWS CloudFormation User Guide*  
Specify an Athena data catalog, including a name, description, type, parameters, and tags\. For more information, see [DataCatalog](https://docs.aws.amazon.com/athena/latest/APIReference/API_DataCatalog.html) in the *Amazon Athena API Reference*\.  
*Named Query*  
Reference topic: [AWS::Athena::NamedQuery](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-namedquery.html) in the *AWS CloudFormation User Guide*  
Specify named queries with AWS CloudFormation and run them in Athena\. Named queries allow you to map a query name to a query and then run it as a saved query from the Athena console\. For information, see [CreateNamedQuery](https://docs.aws.amazon.com/athena/latest/APIReference/API_CreateNamedQuery.html) in the *Amazon Athena API Reference*\.  
*Workgroup*  
Reference topic: [AWS::Athena::WorkGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-athena-workgroup.html) in the *AWS CloudFormation User Guide*  
Specify Athena workgroups using AWS CloudFormation\. Use Athena workgroups to isolate queries for you or your group from other queries in the same account\. For more information, see [Using Workgroups to Control Query Access and Costs](manage-queries-control-costs-with-workgroups.md) in the *Amazon Athena User Guide* and [CreateWorkGroup](https://docs.aws.amazon.com/athena/latest/APIReference/API_CreateWorkGroup.html) in the *Amazon Athena API Reference*\.

**Amazon CloudFront**  
Reference topic: [Querying Amazon CloudFront Logs](cloudfront-logs.md)  
Use Athena to query Amazon CloudFront logs\. For more information about using CloudFront, see the [Amazon CloudFront Developer Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/)\.

**AWS CloudTrail**  
Reference topic: [Querying AWS CloudTrail Logs](cloudtrail-logs.md)  
Using Athena with CloudTrail logs is a powerful way to enhance your analysis of AWS service activity\. For example, you can use queries to identify trends and further isolate activity by attribute, such as source IP address or user\. You can create tables for querying logs directly from the CloudTrail console, and use those tables to run queries in Athena\. For more information, see [Creating a Table for CloudTrail Logs in the CloudTrail Console](cloudtrail-logs.md#create-cloudtrail-table-ct)\.

**Elastic Load Balancing**  
Reference topic: [Querying Application Load Balancer Logs](application-load-balancer-logs.md)  
Querying Application Load Balancer logs allows you to see the source of traffic, latency, and bytes transferred to and from Elastic Load Balancing instances and backend applications\. For more information, see [Creating the Table for ALB Logs](application-load-balancer-logs.md#create-alb-table)\.  
Reference topic: [Querying Classic Load Balancer Logs](elasticloadbalancer-classic-logs.md)  
Query Classic Load Balancer logs to analyze and understand traffic patterns to and from Elastic Load Balancing instances and backend applications\. You can see the source of traffic, latency, and bytes transferred\. For more information, see [Creating the Table for ELB Logs](elasticloadbalancer-classic-logs.md#create-elb-table)\.

**AWS Glue Data Catalog**  
Reference topic: [Integration with AWS Glue](glue-athena.md)   
Athena integrates with the AWS Glue Data Catalog, which offers a persistent metadata store for your data in Amazon S3\. This allows you to create tables and query data in Athena based on a central metadata store available throughout your Amazon Web Services account and integrated with the ETL and data discovery features of AWS Glue\. For more information, see [Integration with AWS Glue](glue-athena.md) and [What is AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) in the *AWS Glue Developer Guide*\.

**AWS Identity and Access Management \(IAM\)**  
Reference topic: [Actions for Amazon Athena](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonathena.html)  
You can use Athena API actions in IAM permission policies\. For more information, see [Actions for Amazon Athena](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonathena.html) and [Identity and Access Management in Athena](security-iam-athena.md)\.

**Amazon QuickSight**  
Reference topic: [Connecting to Amazon Athena with ODBC and JDBC Drivers](athena-bi-tools-jdbc-odbc.md)  
Athena integrates with Amazon QuickSight for easy data visualization\. You can use Athena to generate reports or to explore data with business intelligence tools or SQL clients connected with a JDBC or an ODBC driver\. For more information about Amazon QuickSight, see [What is Amazon QuickSight](https://docs.aws.amazon.com/quicksight/latest/user/welcome.html) in the *Amazon QuickSight User Guide*\. For information about using JDBC and ODBC drivers with Athena, see [Connecting to Amazon Athena with ODBC and JDBC Drivers](athena-bi-tools-jdbc-odbc.md)\.

**Amazon S3 Inventory**  
Reference topic: [Querying inventory with Athena](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-inventory.html#storage-inventory-athena-query) in the *Amazon Simple Storage Service User Guide*  
You can use Amazon Athena to query Amazon S3 inventory using standard SQL\. You can use Amazon S3 inventory to audit and report on the replication and encryption status of your objects for business, compliance, and regulatory needs\. For more information, see [Amazon S3 inventory](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-inventory.html) in the *Amazon Simple Storage Service User Guide*\.

**AWS Step Functions**  
Reference topic: [Call Athena with Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/connect-athena.html) in the *AWS Step Functions Developer Guide*  
Call Athena with AWS Step Functions\. AWS Step Functions can control select AWS services directly using the [Amazon States Language](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html)\. You can use Step Functions with Athena to start and stop query execution, get query results, run ad\-hoc or scheduled data queries, and retrieve results from data lakes in Amazon S3\. For more information, see the [AWS Step Functions Developer Guide](https://docs.aws.amazon.com/step-functions/latest/dg/)\.   
**Video: Orchestrate Amazon Athena Queries using AWS Step Functions**  
The following video demonstrates how to use Amazon Athena and AWS Step Functions to run a regularly scheduled Athena query and generate a corresponding report\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/rRr3QfIMTBo/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/rRr3QfIMTBo)
For an example that uses Step Functions and Amazon EventBridge to orchestrate AWS Glue DataBrew, Athena, and Amazon QuickSight, see [Orchestrating an AWS Glue DataBrew job and Amazon Athena query with AWS Step Functions](http://aws.amazon.com/blogs/big-data/orchestrating-an-aws-glue-databrew-job-and-amazon-athena-query-with-aws-step-functions/) in the AWS Big Data Blog\.

**AWS Systems Manager Inventory**  
Reference topic: [Querying inventory data from multiple Regions and accounts](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-inventory-query.html) in the *AWS Systems Manager User Guide*  
AWS Systems Manager Inventory integrates with Amazon Athena to help you query inventory data from multiple AWS Regions and accounts\. For more information, see the [AWS Systems Manager User Guide](https://docs.aws.amazon.com/systems-manager/latest/userguide/)\.

**Amazon Virtual Private Cloud**  
Reference topic: [Querying Amazon VPC Flow Logs](vpc-flow-logs.md)  
Amazon Virtual Private Cloud flow logs capture information about the IP traffic going to and from network interfaces in a VPC\. Query the logs in Athena to investigate network traffic patterns and identify threats and risks across your Amazon VPC network\. For more information about Amazon VPC, see the [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.

# Amazon Athena Vertica Connector<a name="athena-prebuilt-data-connectors-vertica"></a>

 Vertica is a columnar database platform that can be deployed in the cloud or on premises that supports exabyte scale data warehouses\. You can use the Amazon Athena Vertica connector in federated queries to query Vertica data sources from Athena\. For example, you can run analytical queries over a data warehouse on Vertica and a data lake in Amazon S3\.

To deploy the Athena Vertica connector, visit the [AthenaVerticaConnector](https://console.aws.amazon.com/lambda/home?region=us-east-1#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:292517598671:applications/AthenaVerticaConnector) page in the AWS Serverless Application Repository\.

The Amazon Athena Vertica connector exposes several configuration options through Lambda environment variables\. For information about configuration options, parameters, connection strings, deployment, and limitations, see [Amazon Athena Vertica Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-vertica) on GitHub\. 

For in\-depth information about using the Vertica connector, see [Querying a Vertica data source in Amazon Athena using the Athena Federated Query SDK](http://aws.amazon.com/blogs/big-data/querying-a-vertica-data-source-in-amazon-athena-using-the-athena-federated-query-sdk/) in the *AWS Big Data Blog*\.
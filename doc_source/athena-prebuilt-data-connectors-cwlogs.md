# Amazon Athena CloudWatch Connector<a name="athena-prebuilt-data-connectors-cwlogs"></a>

The Amazon Athena CloudWatch connector enables Amazon Athena to communicate with CloudWatch so that you can query your log data with SQL\.

The connector maps your LogGroups as schemas and each LogStream as a table\. The connector also maps a special `all_log_streams` view that contains all LogStreams in the LogGroup\. This view enables you to query all the logs in a LogGroup at once instead of searching through each LogStream individually\.

For more information about configuration options, throttling control, table mapping schema, permissions, deployment, performance considerations, and licensing, see [Amazon Athena CloudWatch Connector](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-cloudwatch) on GitHub\.
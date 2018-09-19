# Viewing Query History<a name="queries-viewing-history"></a>

To view your recent query history, use **History**\. Athena retains query history for 45 days\.

**Note**  
Starting on December 1, 2017, Athena retains query history for 45 days\.

To retain query history for a longer period, write a program using methods from Athena API and the AWS CLI to periodically retrieve the query history and save it to a data store:

1. Retrieve the query IDs with [ListQueryExecutions](https://docs.aws.amazon.com/athena/latest/APIReference/API_ListQueryExecutions.html)\.

1. Retrieve information about each query based on its ID with [GetQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryExecution.html)\.

1. Save the obtained information in a data store, such as Amazon S3, using the [put\-object](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html) AWS CLI command from the Amazon S3 API\.

## Viewing Query History<a name="id1"></a>

1. To view a query in your history for up to 45 days after it ran, choose **History** and select a query\. You can also see which queries succeeded and failed, download their results, and view query IDs, by clicking the status value\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/query_history.png)
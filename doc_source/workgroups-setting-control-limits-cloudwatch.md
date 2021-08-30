# Setting Data Usage Control Limits<a name="workgroups-setting-control-limits-cloudwatch"></a>

 Athena allows you to set two types of cost controls: per\-query limit and per\-workgroup limit\. For each workgroup, you can set only one per\-query limit and multiple per\-workgroup limits\.
+ The **per\-query control limit** specifies the total amount of data scanned per query\. If any query that runs in the workgroup exceeds the limit, it is canceled\. You can create only one per\-query control limit in a workgroup and it applies to each query that runs in it\. Edit the limit if you need to change it\. For detailed steps, see [To create a per\-query data usage control](#configure-control-limit-per-query)\.
+ The **workgroup\-wide data usage control limit** specifies the total amount of data scanned for all queries that run in this workgroup during the specified time period\. You can create multiple limits per workgroup\. The workgroup\-wide query limit allows you to set multiple thresholds on hourly or daily aggregates on data scanned by queries running in the workgroup\. 

  If the aggregate amount of data scanned exceeds the threshold, you can push a notification to an Amazon SNS topic\. To do this, you configure an Amazon SNS alarm and an action in the Athena console to notify an administrator when the limit is breached\. For detailed steps, see [To create a per\-workgroup data usage control](#configure-control-limit-per-workgroup)\. You can also create an alarm and an action on any metric that Athena publishes from the CloudWatch console\. For example, you can set an alert on a number of failed queries\. This alert can trigger an email to an administrator if the number crosses a certain threshold\. If the limit is exceeded, an action sends an Amazon SNS alarm notification to the specified users\.

  Other actions you can take:
  + Invoke a Lambda function\. For more information, see [Invoking Lambda functions using Amazon SNS notifications](https://docs.aws.amazon.com/sns/latest/dg/sns-lambda-as-subscriber.html) in the *Amazon Simple Notification Service Developer Guide*\.
  + Disable the workgroup to stop any further queries from running\. For steps, see [Enable and Disable a Workgroup](workgroups-create-update-delete.md#workgroups-enabled-disabled)\.

The per\-query and per\-workgroup limits are independent of each other\. A specified action is taken whenever either limit is exceeded\. If two or more users run queries at the same time in the same workgroup, it is possible that each query does not exceed any of the specified limits, but the total sum of data scanned exceeds the data usage limit per workgroup\. In this case, an Amazon SNS alarm is sent to the user\. <a name="configure-control-limit-per-query"></a>

**To create a per\-query data usage control**

The per\-query control limit specifies the total amount of data scanned per query\. If any query that runs in the workgroup exceeds the limit, it is canceled\. Canceled queries are charged according to [Amazon Athena pricing](https://aws.amazon.com/athena/pricing/)\.
**Note**  
In the case of canceled or failed queries, Athena may have already written partial results to Amazon S3\. In such cases, Athena does not delete partial results from the Amazon S3 prefix where results are stored\. You must remove the Amazon S3 prefix with partial results\. Athena uses Amazon S3 multipart uploads to write data Amazon S3\. We recommend that you set the bucket lifecycle policy to end multipart uploads in cases when queries fail\. For more information, see [Aborting Incomplete Multipart Uploads Using a Bucket Lifecycle Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/mpuoverview.html#mpu-abort-incomplete-mpu-lifecycle-config) in the *Amazon Simple Storage Service Developer Guide*\.

You can create only one per\-query control limit in a workgroup and it applies to each query that runs in it\. Edit the limit if you need to change it\. 

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose the **Workgroup** tab\.

   To create a data usage control for a query in a particular workgroup, you don't need to switch to it and can remain in another workgroup\. You do need to select the workgroup from the list and have permissions to edit the workgroup\.

1. Select the workgroup from the list, and then choose **View details**\. If you have permissions, the workgroup's details display in the **Overview** tab\.

1. Choose the **Data usage controls** tab\.  
![\[The screenshot for creating a data usage control per query.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-per-query-data-usage-control.png)

1. In the **Per query data usage control** section, specify the field values, as follows:
   + For **Data limits**, specify a value between 10000 KB \(minimum\) and 7000000 TB \(maximum\)\.
**Note**  
These are limits imposed by the console for data usage controls within workgroups\. They do not represent any query limits in Athena\.
   + For units, select the unit value from the drop\-down list \(KB, MB, GB, or TB\)\.
   + The default **Action** is to cancel the query if it exceeds the limit\. This setting cannot be changed\.

1. Choose **Create** if you are creating a new limit, or **Update** if you are editing an existing limit\. If you are editing an existing limit, refresh the **Overview** tab to see the updated limit\.<a name="configure-control-limit-per-workgroup"></a>

**To create a per\-workgroup data usage control**

The workgroup\-wide data usage control limit specifies the total amount of data scanned for all queries that run in this workgroup during the specified time period\. You can create multiple control limits per workgroup\. If the limit is exceeded, you can choose to take action, such as send an Amazon SNS alarm notification to the specified users\.

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Choose the **Workgroup** tab\.

   To create a data usage control for a particular workgroup, you don't need to switch to it and can remain in another workgroup\. You do need to select the workgroup from the list and have permissions to edit the workgroup\.

1. Select the workgroup from the list, and then choose **View details**\. If you have edit permissions, the workgroup's details display in the **Overview** tab\.

1. Choose the **Data usage controls** tab, and scroll down\. Then choose **Workgroup data usage controls** to create a new limit or edit an existing limit\. The **Create workgroup data usage control** dialog displays\.   
![\[The screenshot for creating a data usage control per workgroup.\]](http://docs.aws.amazon.com/athena/latest/ug/images/wg-create-data-usage-control.png)

1. Specify field values as follows:
   + For **Data limits**, specify a value between 10 MB \(minimum\) and 7000000 TB \(maximum\)\.
**Note**  
These are limits imposed by the console for data usage controls within workgroups\. They do not represent any query limits in Athena\.
   + For units, select the unit value from the drop\-down list\.
   + For time period, choose a time period from the drop\-down list\.
   + For **Action**, choose an Amazon SNS topic from the drop\-down list, if you have one configured\. Or, choose **Create an Amazon SNS topic** to go directly to the [Amazon SNS console](https://console.aws.amazon.com/sns/v2/home), create the Amazon SNS topic, and set up a subscription for it for one of the users in your Athena account\. For more information, see [Getting started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) in the *Amazon Simple Notification Service Getting Started Guide*\. 

1. Choose **Create** if you are creating a new limit, or **Save** if you are editing an existing limit\. If you are editing an existing limit, refresh the **Overview** tab for the workgroup to see the updated limit\.
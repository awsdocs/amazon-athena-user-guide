# Setting data usage control limits<a name="workgroups-setting-control-limits-cloudwatch"></a>

 Athena allows you to set two types of cost controls: per\-query limit and per\-workgroup limit\. For each workgroup, you can set only one per\-query limit and multiple per\-workgroup limits\.
+ The **per\-query control limit** specifies the total amount of data scanned per query\. If any query that runs in the workgroup exceeds the limit, it is canceled\. You can create only one per\-query control limit in a workgroup and it applies to each query that runs in it\. Edit the limit if you need to change it\. For detailed steps, see [To create a per\-query data usage control](#configure-control-limit-per-query)\.
+ The **workgroup\-wide data usage control limit** specifies the total amount of data scanned for all queries that run in this workgroup during the specified time period\. You can create multiple limits per workgroup\. The workgroup\-wide query limit allows you to set multiple thresholds on hourly or daily aggregates on data scanned by queries running in the workgroup\. 

  If the aggregate amount of data scanned exceeds the threshold, you can push a notification to an Amazon SNS topic\. To do this, you configure an Amazon SNS alarm and an action in the Athena console to notify an administrator when the limit is breached\. For detailed steps, see [To create a per\-workgroup data usage control](#configure-control-limit-per-workgroup)\. You can also create an alarm and an action on any metric that Athena publishes from the CloudWatch console\. For example, you can set an alert on a number of failed queries\. This alert can trigger an email to an administrator if the number crosses a certain threshold\. If the limit is exceeded, an action sends an Amazon SNS alarm notification to the specified users\.

  Other actions you can take:
  + Invoke a Lambda function\. For more information, see [Invoking Lambda functions using Amazon SNS notifications](https://docs.aws.amazon.com/sns/latest/dg/sns-lambda-as-subscriber.html) in the *Amazon Simple Notification Service Developer Guide*\.
  + Disable the workgroup to stop any further queries from running\. For steps, see [Enable and disable a workgroup](workgroups-create-update-delete.md#workgroups-enabled-disabled)\.

The per\-query and per\-workgroup limits are independent of each other\. A specified action is taken whenever either limit is exceeded\. If two or more users run queries at the same time in the same workgroup, it is possible that each query does not exceed any of the specified limits, but the total sum of data scanned exceeds the data usage limit per workgroup\. In this case, an Amazon SNS alarm is sent to the user\. <a name="configure-control-limit-per-query"></a>

**To create a per\-query data usage control**

The per\-query control limit specifies the total amount of data scanned per query\. If any query that runs in the workgroup exceeds the limit, it is canceled\. Canceled queries are charged according to [Amazon Athena pricing](http://aws.amazon.com/athena/pricing/)\.
**Note**  
In the case of canceled or failed queries, Athena may have already written partial results to Amazon S3\. In such cases, Athena does not delete partial results from the Amazon S3 prefix where results are stored\. You must remove the Amazon S3 prefix with partial results\. Athena uses Amazon S3 multipart uploads to write data Amazon S3\. We recommend that you set the bucket lifecycle policy to end multipart uploads in cases when queries fail\. For more information, see [Aborting incomplete multipart uploads using a bucket lifecycle policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/mpuoverview.html#mpu-abort-incomplete-mpu-lifecycle-config) in the *Amazon Simple Storage Service User Guide*\.

You can create only one per\-query control limit in a workgroup and it applies to each query that runs in it\. Edit the limit if you need to change it\. 

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/polaris-nav-pane-expansion.png)

1. In the navigation pane, choose **Workgroups**\.

1. Choose the name of the workgroup from the list\.

1. On the **Data usage controls** tab, in the **Per query data usage control** section, choose **Manage**\. 

1. On the **Manage per query data usage control** page, specify the following values:
   + For **Data limit**, specify a value between 10 MB \(minimum\) and 7 EB \(maximum\)\.
**Note**  
These are limits imposed by the console for data usage controls within workgroups\. They do not represent any query limits in Athena\.
   + For units, select the unit value from the drop\-down list \(for example, **Kilobytes KB** or **Exabytes EB**\)\.

   The default action is to cancel the query if it exceeds the limit\. This setting cannot be changed\.

1. Choose **Save**\.<a name="configure-control-limit-per-workgroup"></a>

**To create or edit a per\-workgroup data usage alert**

You can set multiple alert thresholds when queries running in a workgroup scan a specified amount of data within a specific period\. Alerts are implemented using Amazon CloudWatch alarms and apply to all queries in the workgroup\. When a threshold is reached, you can have Amazon SNS send an email to users that you specify\. Queries are not automatically canceled when a threshold is reached\.

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If the console navigation pane is not visible, choose the expansion menu on the left\.

1. In the navigation pane, choose **Workgroups**\.

1. Choose the name of the workgroup from the list\.

1. Choose **Edit** to edit the workgroup's settings\.

1. Scroll down to and expand **Workgroup data usage alerts \- optional**\.

1. Choose **Add alert**\.

1. For **Data usage threshold configuration**, specify values as follows:
   + For **Data threshold**, specify a number, and then select a unit value from the drop\-down list\.
   + **For Time period**, choose a time period from the drop\-down list\.
   + For **SNS topic selection**, choose an Amazon SNS topic from the drop\-down list\. Or, choose **Create SNS topic** to go directly to the [Amazon SNS console](https://console.aws.amazon.com/sns/v2/home), create the Amazon SNS topic, and set up a subscription for it for one of the users in your Athena account\. For more information, see [Getting started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) in the *Amazon Simple Notification Service Developer Guide*\. 

1. Choose **Add alert** if you are creating a new alert, or **Save** to save an existing alert\.
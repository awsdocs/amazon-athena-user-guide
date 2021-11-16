# Enabling Cross\-Account Federated Queries<a name="xacct-fed-query-enable"></a>

Federated query allows you to query data sources other than Amazon S3 using data source connectors deployed on AWS Lambda\. The cross\-account federated query feature allows the Lambda function and the data sources that are to be queried to be located in different accounts\.

As a data administrator, you can enable cross\-account federated queries by sharing your data connector with a data analyst's account or, as a data analyst, by using a shared Lambda ARN from a data administrator to add to your account\. When configuration changes are made to a connector in the originating account, the updated configuration is automatically applied to the shared instances of the connector in other user's accounts\.

## Considerations and Limitations<a name="xacct-fed-query-enable-considerations-and-limitations"></a>
+ The cross\-account federated query feature is available for non\-Hive metastore data connectors that use a Lambda\-based data source\.
+ The feature is not available for the AWS Glue Data Catalog data source type\. For information about cross\-account access to AWS Glue Data Catalogs, see [Cross\-Account Access to AWS Glue Data Catalogs](security-iam-cross-account-glue-catalog-access.md)\.

## Required Permissions<a name="xacct-fed-query-enable-required-permissions"></a>
+ For data administrator Account A to share a Lambda function with data analyst Account B, Account B requires Lambda invoke function and spill bucket access\. Accordingly, Account A should add a [ resource\-based policy](https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html) to the Lambda function and [principal](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-policy-language-overview.html) access to its spill bucket in Amazon S3\.

  1. The following policy grants Lambda invoke function permissions to Account B on a Lambda function in Account A\.

     ```
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Sid": "CrossAccountInvocationStatement",
           "Effect": "Allow",
           "Principal": {
             "AWS": ["arn:aws:iam::account-B-id:user/username"]
           }, 
           "Action": "lambda:InvokeFunction",
           "Resource": "arn:aws:lambda:aws-region:account-A-id:function:lambda-function-name"
         }
       ]
     }
     ```

  1. The following policy allows spill bucket access to the principal in Account B\.

     ```
     {
         "Version": "2008-10-17",
         "Statement": [
             {
                 "Effect": "Allow",
                 "Principal": {
                     "AWS": ["arn:aws:iam::account-B-id:user/username"]
                 },
                 "Action": [
                     "s3:GetObject",
                     "s3:ListBucket"
                  ],
                 "Resource": [
                     "arn:aws:s3:::spill-bucket",
                     "arn:aws:s3:::spill-bucket/*"
                 ]
             }
          ]
      }
     ```

  1. If the Lambda function is encrypting the spill bucket with a AWS KMS key instead of the default encryption offered by the federation SDK, the AWS KMS key policy in Account A must grant access to the user in Account B, as in the following example\.

     ```
     { 
         "Sid": "Allow use of the key", 
         "Effect": "Allow", 
         "Principal": 
         { 
            "AWS": ["arn:aws:iam::account-B-id:user/username"] 
         }, 
         "Action": [ "kms:Decrypt" ], 
         "Resource": "*" // Resource policy that gets placed on the KMS key. 
      }
     ```
+ For Account A to share its connector with Account B, Account B must create a role called `AthenaCrossAccountCreate-account-A-id` that Account A assumes by calling the AWS Security Token Service [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) API action\.

  The following policy, which allows the `CreateDataCatalog` action, should be created in Account B and added to the `AthenaCrossAccountCreate-account-A-id` role that Account B creates for Account A\.

  ```
  {
   "Effect": "Allow",
   "Action": "athena:CreateDataCatalog",
   "Resource": "arn:aws:athena:*:account-B-id:datacatalog/*"
  }
  ```

## Sharing a Data Source in Account A with Account B<a name="xacct-fed-query-enable-sharing-a-lambda-data-source-in-account-a-with-account-b"></a>

After permissions are in place, you can use the **Data sources** page in the Athena console to share a data connector in your account \(Account A\) with another account \(Account B\)\. Account A retains full control and ownership of the connector\. When Account A makes configuration changes to the connector, the updated configuration applies to the shared connector in Account B\.

**To share a Lambda data source in Account A with Account B**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If you are using the new console experience and the navigation pane is not visible, choose the expansion menu on the left\.  
![\[Choose the expansion menu.\]](http://docs.aws.amazon.com/athena/latest/ug/images/xacct-fed-query-enable-1.png)

1. Choose **Data sources**\.

1. On the **Data sources** page, choose the link of the connector that you want to share\.

1. On the details page for a Lambda data source, choose the **Share** option in the top right corner\.  
![\[Choose Share.\]](http://docs.aws.amazon.com/athena/latest/ug/images/xacct-fed-query-enable-2.png)

1. In the **Share *Lambda\-name* with another account** dialog box, enter the required information\.
   + For **Data source name**, enter the name of the copied data source as you want it to appear in the other account\.
   + For **Account ID**, enter the ID of the account with which you want to share your data source \(in this case, Account B\)\.  
![\[Enter a data source name and an AWS account ID.\]](http://docs.aws.amazon.com/athena/latest/ug/images/xacct-fed-query-enable-3.png)

1. Choose **Share**\. The shared data connector that you specified is created in Account B\. Configuration changes to the connector in Account A apply to the connector in Account B\.

## Adding a Shared Data Source from Account A to Account B<a name="xacct-fed-query-enable-add-a-shared-lambda-function-arn-to-your-account"></a>

As a data analyst, you may be given the ARN of a connector to add to your account from a data administrator\. You can use the **Data sources** page of the Athena console to add the Lambda ARN provided by your administrator to your account\.

**To add the Lambda ARN of a shared data connector to your account**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. If you are using the new console experience and the navigation pane is not visible, choose the expansion menu on the left\.

1. Choose **Data sources**\.

1. On the **Data sources** page, choose **Connect data source**\.  
![\[Choose Connect data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/xacct-fed-query-enable-4.png)

1. Choose **Custom or shared connector**\.  
![\[Choose Custom or shared connector.\]](http://docs.aws.amazon.com/athena/latest/ug/images/xacct-fed-query-enable-5.png)

1. In the **Lambda function** section, make sure that the option **Use an existing Lambda function** is selected\.  
![\[Specifying the Lambda ARN of another account.\]](http://docs.aws.amazon.com/athena/latest/ug/images/xacct-fed-query-enable-6.png)

1. For **Choose or enter a Lambda function**, enter the Lambda ARN of Account A\.

1. Choose **Connect data source**\.

## Troubleshooting<a name="xacct-fed-query-enable-troubleshooting"></a>

If you receive an error message that Account A does not have the permissions to assume a role in Account B, make sure that the name of the role created in Account B is spelled correctly and that it has the proper policy attached\.
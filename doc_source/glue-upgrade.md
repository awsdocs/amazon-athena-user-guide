# Upgrading to the AWS Glue Data Catalog Step\-by\-Step<a name="glue-upgrade"></a>

Currently, all regions that support Athena also support AWS Glue Data Catalog\. Databases and tables are available to Athena using the AWS Glue Data Catalog and vice versa\. 

If you created databases and tables using Athena or Amazon Redshift Spectrum prior to a region's support for AWS Glue, you can upgrade Athena to use the AWS Glue Data Catalog\. 

If you are using the older Athena\-managed data catalog, you see the option to upgrade at the top of the console\. The metadata in the Athena\-managed catalog isn't available in the AWS Glue Data Catalog or vice versa\. While the catalogs exist side\-by\-side, creating tables or databases with the same names fails in either AWS Glue or Athena\. This prevents name collisions when you do upgrade\. For more information about the benefits of using the AWS Glue Data Catalog, see [FAQ: Upgrading to the AWS Glue Data Catalog](glue-faq.md)\.

A wizard in the Athena console can walk you through upgrading to the AWS Glue console\. The upgrade takes just a few minutes, and you can pick up where you left off\. For information about each upgrade step, see the topics in this section\. 

For information about working with data and tables in the AWS Glue Data Catalog, see the guidelines in [Best Practices When Using Athena with AWS Glue](glue-best-practices.md)\.

## Step 1 \- Allow a User to Perform the Upgrade<a name="upgrade-step1"></a>

By default, the action that allows a user to perform the upgrade is not allowed in any policy, including any managed policies\. Because the AWS Glue Data Catalog is shared throughout an account, this extra failsafe prevents someone from accidentally migrating the catalog\.

Before the upgrade can be performed, you need to attach a customer\-managed IAM policy, with a policy statement that allows the upgrade action, to the user who performs the migration\.

The following is an example policy statement\.

```
{
     "Version": "2012-10-17",
     "Statement": [
        {
           "Effect": "Allow",
           "Action": [
              "glue:ImportCatalogToGlue"
           ],
           "Resource": [ "*" ]
        }
     ]
 }
```

## Step 2 \- Update Customer\-Managed/Inline Policies Associated with Athena Users<a name="upgrade-step2"></a>

If you have customer\-managed or inline IAM policies associated with Athena users, you need to update the policy or policies to allow actions that AWS Glue requires\. If you use the Athena managed policy, no action is required\. The AWS Glue policy actions to allow are listed in the example policy below\. For the full policy statement, see [IAM Policies for User Access](managed-policies.md)\.

```
{
  "Effect":"Allow",
  "Action":[
    "glue:CreateDatabase",
    "glue:DeleteDatabase",
    "glue:GetDatabase",
    "glue:GetDatabases",
    "glue:UpdateDatabase",
    "glue:CreateTable",
    "glue:DeleteTable",
    "glue:BatchDeleteTable",
    "glue:UpdateTable",
    "glue:GetTable",
    "glue:GetTables",
    "glue:BatchCreatePartition",
    "glue:CreatePartition",
    "glue:DeletePartition",
    "glue:BatchDeletePartition",
    "glue:UpdatePartition",
    "glue:GetPartition",
    "glue:GetPartitions",
    "glue:BatchGetPartition"
  ],
  "Resource":[
    "*"
  ]
}
```

## Step 3 \- Choose Upgrade in the Athena Console<a name="upgrade-step3"></a>

After you make the required IAM policy updates, choose **Upgrade** in the Athena console\. Athena moves your metadata to the AWS Glue Data Catalog\. The upgrade takes only a few minutes\. After you upgrade, the Athena console has a link to open the AWS Glue Catalog Manager from within Athena\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_post_migration.png)

When you create a table using the console, you can create a table using an AWS Glue crawler\. For more information, see [Using AWS Glue Crawlers](glue-best-practices.md#schema-crawlers)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/create-table-choices-glue.png)
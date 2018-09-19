# Upgrading to the AWS Glue Data Catalog Step\-by\-Step<a name="glue-upgrade"></a>

Amazon Athena manages its own data catalog until the time that AWS Glue releases in the Athena region\. At that time, if you previously created databases and tables using Athena or Amazon Redshift Spectrum, you can choose to upgrade Athena to the AWS Glue Data Catalog\. If you are new to Athena, you don't need to make any changes; databases and tables are available to Athena using the AWS Glue Data Catalog and vice versa\. For more information about the benefits of using the AWS Glue Data Catalog, see [FAQ: Upgrading to the AWS Glue Data Catalog](glue-faq.md)\. For a list of regions where AWS Glue is available, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#glue_region) in the *AWS General Reference*\.

Until you upgrade, the Athena\-managed data catalog continues to store your table and database metadata, and you see the option to upgrade at the top of the console\. The metadata in the Athena\-managed catalog isn't available in the AWS Glue Data Catalog or vice versa\. While the catalogs exist side\-by\-side, you aren't able to create tables or databases with the same names, and the creation process in either AWS Glue or Athena fails in this case\.

We created a wizard in the Athena console to walk you through the steps of upgrading to the AWS Glue console\. The upgrade takes just a few minutes, and you can pick up where you left off\. For more information about each upgrade step, see the topics in this section\. For more information about working with data and tables in the AWS Glue Data Catalog, see the guidelines in [Best Practices When Using Athena with AWS Glue](glue-best-practices.md)\.

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
              "glue:ImportCatalogToGlue "
           ],
           "Resource": [ "*" ]
        }
     ]
 }
```

## Step 2 \- Update Customer\-Managed/Inline Policies Associated with Athena Users<a name="upgrade-step2"></a>

If you have customer\-managed or inline IAM policies associated with Athena users, you need to update the policy or policies to allow actions that AWS Glue requires\. If you use the managed policy, they are automatically updated\. The AWS Glue policy actions to allow are listed in the example policy below\. For the full policy statement, see [IAM Policies for User Access](access.md#managed-policies)\.

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

When you create a table using the console, you now have the option to create a table using an AWS Glue crawler\. For more information, see [Using AWS Glue Crawlers](glue-best-practices.md#schema-crawlers)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_create_table.png)
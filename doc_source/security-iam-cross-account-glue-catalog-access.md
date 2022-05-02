# Cross\-account access to AWS Glue data catalogs<a name="security-iam-cross-account-glue-catalog-access"></a>

You can use Athena's cross\-account AWS Glue catalog feature to register an AWS Glue catalog from an account other than your own\. After you configure the required IAM permissions for AWS Glue and register the catalog as an Athena [DataCatalog](https://docs.aws.amazon.com/athena/latest/APIReference/API_DataCatalog.html) resource, you can use Athena to run cross\-account queries\. For information on using the Athena console to register a catalog from another account, see [Registering an AWS Glue Data Catalog from another account](data-sources-glue-cross-account.md)\.

For more information about cross\-account access in AWS Glue, see [Granting cross\-account access](https://docs.aws.amazon.com/glue/latest/dg/cross-account-access.html) in the *AWS Glue Developer Guide*\.

## Before you start<a name="security-iam-cross-account-glue-catalog-access-before-you-start"></a>

Because this feature uses existing Athena `DataCatalog` resource APIs and functionality to enable cross\-account access, we recommend that you read the following resources before you start:
+ [Connecting to data sources](work-with-data-stores.md) \- Contains topics on using Athena with AWS Glue, Hive, or Lambda data catalog sources\.
+ [Data Catalog example policies](datacatalogs-example-policies.md) \- Shows how to write policies that control access to data catalogs\.
+ [Using the AWS CLI with Hive metastores](datastores-hive-cli.md) \- Shows how to use the AWS CLI with Hive metastores, but contains use cases applicable to other data sources\.

## Considerations and limitations<a name="security-iam-cross-account-glue-catalog-access-considerations-and-limitations"></a>

Currently, Athena cross\-account AWS Glue catalog access has the following limitations:
+ The feature is available only in Regions where Athena engine version 2 is supported\. For a list of AWS Regions that support Athena engine version 2, see [Athena engine version 2](engine-versions-reference.md#engine-versions-reference-0002)\. To upgrade a workgroup to engine version 2, see [Changing Athena engine versions](engine-versions-changing.md)\.
+ When you register another account's AWS Glue Data Catalog in your account, you create a regional `DataCatalog` resource that is linked to the other account's data in that particular Region only\.
+ Currently, `CREATE VIEW` statements that include a cross\-account AWS Glue catalog are not supported\.

## Getting started<a name="security-iam-cross-account-glue-catalog-getting-started"></a>

In the following scenario, the "Borrower" account \(666666666666\) wants to run a `SELECT` query that refers to the AWS Glue catalog that belongs to the "Owner" account \(999999999999\), as in the following example:

```
SELECT * FROM ownerCatalog.tpch1000.customer
```

In the following procedure, Steps 1a and 1b show how to give the Borrower account access to the Owner account's AWS Glue resources, from both the Owner's perspective and from the Borrower's perspective\. The example grants access to the database `tpch1000` and the table `customer`\. Change these example names to fit your requirements\.

### Step 1a: Create an owner policy to grant AWS Glue access to the borrower<a name="security-iam-cross-account-glue-catalog-access-step-1a"></a>

To grant AWS Glue access from the Owner account \(999999999999\) to the Borrower's user or role, you can use the AWS Glue console or the AWS Glue [PutResourcePolicy](https://docs.aws.amazon.com/glue/latest/webapi/API_PutResourcePolicy.html) API operation\. The following procedure uses the AWS Glue console\.

**To grant AWS Glue access to the borrower account from the owner**

1. Sign in to the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/) from the Owner account\.

1. In the navigation pane, choose **Settings**\.

1. In the **Permissions** box, enter a policy like the following\. If you want to increase the permission scope, you can use the wildcard character `*` for both the database and table resource types\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "AWS": [
                       "arn:aws:iam::666666666666:user/username",
                       "arn:aws:iam::666666666666:role/rolename"
                   ]
               },
               "Action": "glue:*",
               "Resource": [
                   "arn:aws:glue:us-east-1:999999999999:catalog",
                   "arn:aws:glue:us-east-1:999999999999:database/tpch1000",
                   "arn:aws:glue:us-east-1:999999999999:table/tpch1000/customer"
               ]
           }
       ]
   }
   ```

### Step 1b: Create a borrower role or user policy with access to the owner's AWS Glue resources<a name="security-iam-cross-account-glue-catalog-access-step-1b"></a>

To give the Borrower account role or user access to the Owner account's AWS Glue resources, you can use the AWS Identity and Access Management \(IAM\) console or the [IAM API](https://docs.aws.amazon.com/IAM/latest/APIReference/API_Operations.html)\. The following procedures use the IAM console\.

**To grant a borrower role or user access to the owner account's AWS Glue resources**

1. Sign in to the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/) from the Borrower account\.

1. In the navigation pane, choose **Roles** or **Users**\.

1. Choose the role or user that you want to change\.

1. Do one of the following:
   + If you are changing a role, choose **Attach policies**\.
   + If you are changing a user, choose **Add permissions**, and then choose **Attach existing policies directly**\.

1. Choose **Create policy**\.

1. Choose **JSON**\.

1. In the box, enter the following policy, and then modify it according to your requirements:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": "glue:*",
               "Resource": [
                   "arn:aws:glue:us-east-1:999999999999:catalog",
                   "arn:aws:glue:us-east-1:999999999999:database/tpch1000",
                   "arn:aws:glue:us-east-1:999999999999:table/tpch1000/customer"
               ]
           }
       ]
   }
   ```

1. Choose **Review policy**\.

1. For **Name**, enter a name for the policy\.

1. Choose **Create policy**\.

After you finish, it is recommend that you use the [AWS Glue API](https://docs.aws.amazon.com/glue/latest/dg/aws-glue-api.html) to make some test cross\-account calls to confirm that permissions are configured as you expect\.

### Step 2: The borrower creates an Athena Data Catalog that points to the owner account<a name="security-iam-cross-account-glue-catalog-access-step-2"></a>

The creator of the Athena [DataCatalog](https://docs.aws.amazon.com/athena/latest/APIReference/API_DataCatalog.html) resource must have the necessary permissions to run the Athena [CreateDataCatalog](https://docs.aws.amazon.com/athena/latest/APIReference/API_CreateDataCatalog.html) API operation\. Depending on your requirements, access to additional API operations might be necessary\. For more information, see [Data Catalog example policies](datacatalogs-example-policies.md)\.

The following `CreateDataCatalog` request body registers an AWS Glue catalog for cross\-account access:

```
# Example CreateDataCatalog request to register a cross-account Glue catalog:
{
    "Description": "Cross-account Glue catalog",
    "Name": "ownerCatalog",
    "Parameters": {"catalog-id" : "999999999999"  # Owner's account ID
    },
    "Type": "GLUE"
}
```

The following sample code uses a Java client to create the `DataCatalog` object\.

```
# Sample code to create the DataCatalog through Java client
CreateDataCatalogRequest request = new CreateDataCatalogRequest()
    .withName("ownerCatalog")
    .withType(DataCatalogType.GLUE)
    .withParameters(ImmutableMap.of("catalog-id", "999999999999"));

athenaClient.createDataCatalog(request);
```

After these steps, the Borrower should see `ownerCatalog` when it calls the [ListDataCatalogs](https://docs.aws.amazon.com/athena/latest/APIReference/API_ListDataCatalogs.html) API operation\.

### Step 3: The borrower submits a query<a name="security-iam-cross-account-glue-catalog-access-step-4"></a>

The Borrower submits a query that references the catalog using the *catalog*\.*database*\.*table* syntax, as in the following example:

```
SELECT * FROM ownerCatalog.tpch1000.customer
```

Instead of using the fully qualified syntax, the Borrower can also specify the catalog contextually by passing it in through the [QueryExecutionContext](https://docs.aws.amazon.com/athena/latest/APIReference/API_QueryExecutionContext.html)\.

## Additional Amazon S3 permissions<a name="security-iam-cross-account-glue-catalog-access-additional-s3-permissions"></a>
+ If the Borrower account uses an Athena query to write new data to a table in the Owner account, the Owner will not automatically have access to this data in Amazon S3, even though the table exists in the Owner's account\. This is because the Borrower is the object owner of the information in Amazon S3 unless otherwise configured\. To grant the Owner access to the data, set the permissions on the objects accordingly as an additional step\.
+ Certain cross\-account DDL operations like [MSCK REPAIR TABLE](msck-repair-table.md) require Amazon S3 permissions\. For example, if the Borrower account is performing a cross\-account `MSCK REPAIR` operation against a table in the Owner account that has its data in an Owner account S3 bucket, that bucket must grant permissions to the Borrower for the query to succeed\.

For information about granting bucket permissions, see [How do I set ACL bucket permissions?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-bucket-permissions.html) in the *Amazon Simple Storage Service User Guide*\.

## Using a catalog dynamically<a name="security-iam-cross-account-glue-catalog-access-dynamic-catalogs"></a>

In some cases you might want to quickly perform testing against a cross\-account AWS Glue catalog without the prerequisite step of registering it\. You can dynamically perform cross\-account queries without creating the `DataCatalog` resource object if the required IAM and Amazon S3 permissions are correctly configured as described earlier in this document\.

To explicitly reference a catalog without registration, use the syntax in the following example:

```
SELECT * FROM "glue:arn:aws:glue:us-east-1:999999999999:catalog".tpch1000.customer
```

Use the format "`glue:<arn>`", where `<arn>` is the [AWS Glue Data Catalog ARN](https://docs.aws.amazon.com/glue/latest/dg/glue-specifying-resource-arns.html#data-catalog-resource-arns) that you want to use\. In the example, Athena uses this syntax to dynamically point to account 999999999999's AWS Glue data catalog as if you had separately created a `DataCatalog` object for it\.

### Notes for using dynamic catalogs<a name="security-iam-cross-account-glue-catalog-access-notes-dynamic-catalogs"></a>

When you use dynamic catalogs, remember the following points\.
+ Use of a dynamic catalog requires the IAM permissions that you normally use for Athena Data Catalog API operations\. The main difference is that the Data Catalog resource name follows the `glue:*` naming convention\.
+ The catalog ARN must belong to the same Region where the query is being run\.
+ When using a dynamic catalog in a DML query or view, surround it with escaped double quotation marks \(`\"`\)\. When using a dynamic catalog in a DDL query, surround it with backtick characters \(```\)\.
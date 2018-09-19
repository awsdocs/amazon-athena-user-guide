# FAQ: Upgrading to the AWS Glue Data Catalog<a name="glue-faq"></a>

If you created databases and tables using Athena in a region before AWS Glue was available in that region, metadata is stored in an Athena\-managed data catalog, which only Athena and Amazon Redshift Spectrum can access\. To use AWS Glue features together with Athena and Redshift Spectrum, you must upgrade to the AWS Glue Data Catalog\. Athena can only be used together with the AWS Glue Data Catalog in regions where AWS Glue is available\. For a list of regions, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#glue_region) in the *AWS General Reference*\.

## Why should I upgrade to the AWS Glue Data Catalog?<a name="faq-benefits"></a>

AWS Glue is a completely\-managed extract, transform, and load \(ETL\) service\. It has three main components:
+  **An AWS Glue crawler** can automatically scan your data sources, identify data formats, and infer schema\.
+  **A fully managed ETL service** allows you to transform and move data to various destinations\.
+  **The AWS Glue Data Catalog** stores metadata information about databases and tables, pointing to a data store in Amazon S3 or a JDBC\-compliant data store\.

For more information, see [AWS Glue Concepts](https://docs.aws.amazon.com/glue/latest/dg/components-key-concepts.html)\.

Upgrading to the AWS Glue Data Catalog has the following benefits\.

### Unified metadata repository<a name="unified-metadata-repository"></a>

The AWS Glue Data Catalog provides a unified metadata repository across a variety of data sources and data formats\. It provides out\-of\-the\-box integration with [Amazon Simple Storage Service \(Amazon S3\)](https://aws.amazon.com/s3/), [Amazon Relational Database Service \(Amazon RDS\)](https://aws.amazon.com/rds/), [Amazon Redshift](https://aws.amazon.com/redshift/), [Amazon Redshift Spectrum](https://aws.amazon.com/redshift/spectrum/), Athena, [Amazon EMR](https://aws.amazon.com/emr/), and any application compatible with the Apache Hive metastore\. You can create your table definitions one time and query across engines\.

For more information, see [Populating the AWS Glue Data Catalog](https://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html)\.

### Automatic schema and partition recognition<a name="automatic-schema-and-partition-recognition"></a>

AWS Glue crawlers automatically crawl your data sources, identify data formats, and suggest schema and transformations\. Crawlers can help automate table creation and automatic loading of partitions that you can query using Athena, Amazon EMR, and Redshift Spectrum\. You can also create tables and partitions directly using the AWS Glue API, SDKs, and the AWS CLI\.

For more information, see [Cataloging Tables with a Crawler](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html)\.

### Easy\-to\-build pipelines<a name="easy-to-build-pipelines"></a>

The AWS Glue ETL engine generates Python code that is entirely customizable, reusable, and portable\. You can edit the code using your favorite IDE or notebook and share it with others using GitHub\. After your ETL job is ready, you can schedule it to run on the fully managed, scale\-out Spark infrastructure of AWS Glue\. AWS Glue handles provisioning, configuration, and scaling of the resources required to run your ETL jobs, allowing you to tightly integrate ETL with your workflow\.

For more information, see [Authoring AWS Glue Jobs](https://docs.aws.amazon.com/glue/latest/dg/author-job.html) in the *AWS Glue Developer Guide*\.

## Are there separate charges for AWS Glue?<a name="faq-pricing"></a>

Yes\. With AWS Glue, you pay a monthly rate for storing and accessing the metadata stored in the AWS Glue Data Catalog, an hourly rate billed per second for AWS Glue ETL jobs and crawler runtime, and an hourly rate billed per second for each provisioned development endpoint\. The AWS Glue Data Catalog allows you to store up to a million objects at no charge\. If you store more than a million objects, you are charged USD$1 for each 100,000 objects over a million\. An object in the AWS Glue Data Catalog is a table, a partition, or a database\. For more information, see [AWS Glue Pricing](https://aws.amazon.com/glue/pricing)\.

## Upgrade process FAQ<a name="faq-upgrade"></a>
+  [Who can perform the upgrade?](#faq-upgrade-who) 
+  [My users use a managed policy with Athena and Redshift Spectrum\. What steps do I need to take to upgrade?](#faq-upgrade-managed-policy) 
+  [What happens if I don’t upgrade?](#faq-upgrade-delayed) 
+  [Why do I need to add AWS Glue policies to Athena users?](#faq-upgrade-custom-policy) 
+  [What happens if I don’t allow AWS Glue policies for Athena users?](#faq-upgrade-no-perms) 
+  [Is there risk of data loss during the upgrade?](#faq-upgrade-data-loss) 
+  [Is my data also moved during this upgrade?](#faq-upgrade-data-move) 

### Who can perform the upgrade?<a name="faq-upgrade-who"></a>

You need to attach a customer\-managed IAM policy with a policy statement that allows the upgrade action to the user who will perform the migration\. This extra check prevents someone from accidentally migrating the catalog for the entire account\. For more information, see [Step 1 \- Allow a User to Perform the Upgrade](glue-upgrade.md#upgrade-step1)\.

### My users use a managed policy with Athena and Redshift Spectrum\. What steps do I need to take to upgrade?<a name="faq-upgrade-managed-policy"></a>

The Athena managed policy has been automatically updated with new policy actions that allow Athena users to access AWS Glue\. However, you still must explicitly allow the upgrade action for the user who performs the upgrade\. To prevent accidental upgrade, the managed policy does not allow this action\.

### What happens if I don’t upgrade?<a name="faq-upgrade-delayed"></a>

If you don’t upgrade, you are not able to use AWS Glue features together with the databases and tables you create in Athena or vice versa\. You can use these services independently\. During this time, Athena and AWS Glue both prevent you from creating databases and tables that have the same names in the other data catalog\. This prevents name collisions when you do upgrade\.

### Why do I need to add AWS Glue policies to Athena users?<a name="faq-upgrade-custom-policy"></a>

Before you upgrade, Athena manages the data catalog, so Athena actions must be allowed for your users to perform queries\. After you upgrade to the AWS Glue Data Catalog, Athena actions no longer apply to accessing the AWS Glue Data Catalog, so AWS Glue actions must be allowed for your users\. Remember, the managed policy for Athena has already been updated to allow the required AWS Glue actions, so no action is required if you use the managed policy\.

### What happens if I don’t allow AWS Glue policies for Athena users?<a name="faq-upgrade-no-perms"></a>

If you upgrade to the AWS Glue Data Catalog and don't update a user's customer\-managed or inline IAM policies, Athena queries fail because the user won't be allowed to perform actions in AWS Glue\. For the specific actions to allow, see [Step 2 \- Update Customer\-Managed/Inline Policies Associated with Athena Users](glue-upgrade.md#upgrade-step2)\.

### Is there risk of data loss during the upgrade?<a name="faq-upgrade-data-loss"></a>

No\.

### Is my data also moved during this upgrade?<a name="faq-upgrade-data-move"></a>

No\. The migration only affects metadata\.
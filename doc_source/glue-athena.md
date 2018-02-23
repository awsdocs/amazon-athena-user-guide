# Integration with AWS Glue<a name="glue-athena"></a>

 [AWS Glue](http://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) is a fully managed ETL \(extract, transform, and load\) service that can categorize your data, clean it, enrich it, and move it reliably between various data stores\. AWS Glue crawlers automatically infer database and table schema from your source data, storing the associated metadata in the AWS Glue Data Catalog\. When you create a table in Athena, you can choose to create it using an AWS Glue crawler\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/glue_architecture.png)

In regions where AWS Glue is supported, Athena uses the AWS Glue Data Catalog as a central location to store and retrieve table metadata throughout an AWS account\. The Athena execution engine requires table metadata that instructs it where to read data, how to read it, and other information necessary to process the data\. The AWS Glue Data Catalog provides a unified metadata repository across a variety of data sources and data formats, integrating not only with Athena, but with Amazon S3, Amazon RDS, Amazon Redshift, Amazon Redshift Spectrum, Amazon EMR, and any application compatible with the Apache Hive metastore\.

For more information about the AWS Glue Data Catalog, see [Populating the AWS Glue Data Catalog](http://docs.aws.amazon.com/glue/latest/dg/populate-data-catalog.html) in the *AWS Glue Developer Guide*\. For a list of regions where AWS Glue is available, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html#glue_region) in the *AWS General Reference*\.

Separate charges apply to AWS Glue\. For more information, see [AWS Glue Pricing](https://aws.amazon.com/glue/pricing) and [Are there separate charges for AWS Glue?](glue-faq.md#faq-pricing) For more information about the benefits of using AWS Glue with Athena, see [Why should I upgrade to the AWS Glue Data Catalog?](glue-faq.md#faq-benefits) 


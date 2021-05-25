# How Athena Accesses Data Registered With Lake Formation<a name="lf-athena-access"></a>

The access workflow described in this section applies only when running Athena queries on Amazon S3 locations and metadata objects that are registered with Lake Formation\. For more information, see [Registering a Data Lake](https://docs.aws.amazon.com/lake-formation/latest/dg/register-data-lake.html) in the *AWS Lake Formation Developer Guide*\. In addition to registering data, the Lake Formation administrator applies Lake Formation permissions that grant or revoke access to metadata in the Data Catalog and the data location in Amazon S3\. For more information, see [Security and Access Control to Metadata and Data](https://docs.aws.amazon.com/lake-formation/latest/dg/security-data-access.html#security-data-access-permissions) in the *AWS Lake Formation Developer Guide*\.

Each time an Athena principal \(user, group, or role\) runs a query on data registered using Lake Formation, Lake Formation verifies that the principal has the appropriate Lake Formation permissions to the database, table, and Amazon S3 location as appropriate for the query\. If the principal has access, Lake Formation *vends* temporary credentials to Athena, and the query runs\.

The following diagram illustrates the flow described above\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/lake-formation-athena.png)





The following diagram shows how credential vending works in Athena on a query\-by\-query basis for a hypothetical `SELECT` query on a table with an Amazon S3 location registered in Lake Formation:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/lake_formation_athena_security_aws.png)

1. A principal runs a `SELECT` query in Athena\.

1. Athena analyzes the query and checks Lake Formation permissions to see if the principal has been granted access to the table and table columns\.

1. If the principal has access, Athena requests credentials from Lake Formation\. If the principal *does not* have access, Athena issues an access denied error\.

1. Lake Formation issues credentials to Athena to use when reading data from Amazon S3, along with the list of allowed columns\.

1. Athena uses the Lake Formation temporary credentials to query the data from Amazon S3\. After the query completes, Athena discards the credentials\.
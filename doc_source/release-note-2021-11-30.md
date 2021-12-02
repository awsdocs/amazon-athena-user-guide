# November 30, 2021<a name="release-note-2021-11-30"></a>

Published on 2021\-11\-30

Amazon Athena users can now use AWS Lake Formation to configure fine\-grained access permissions and read from ACID\-compliant tables\. Amazon Athena makes it simple to analyze data in Amazon S3 based data lakes to help ensure that users only have access to data to which they're authorized\. The ACID features help ensure that their queries are reliable in the face of complex changes to the underlying data\.

Use [Lake Formation data filtering ](https://docs.aws.amazon.com/lake-formation/latest/dg/data-filtering-overview.html) to secure the tables in your Amazon S3 data lake by granting permissions at the column, row, and cell levels\. These permissions are enforced when Athena users query your data\. This fine level of control means that you can grant users access to sensitive information without using course\-grained masking that would otherwise impede their analyses\. Furthermore, with Lake Formation governed tables, Athena users can query data while multiple users simultaneously add and delete the table’s Amazon S3 data objects\.

For more information, see [Using Athena ACID Transactions](acid-transactions.md) and [Using Governed Tables](lf-governed-tables.md)\.
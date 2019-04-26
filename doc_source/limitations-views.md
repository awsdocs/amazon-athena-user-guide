# Limitations for Views<a name="limitations-views"></a>
+ Athena view names cannot contain special characters, other than underscore `(_)`\. For more information, see [Names for Tables, Databases, and Columns](tables-databases-columns-names.md)\.
+ Avoid using reserved keywords for naming views\. If you use reserved keywords, use double quotes to enclose reserved keywords in your queries on views\. See [Reserved Keywords](reserved-words.md)\.
+ You cannot use views with geospatial functions\.
+ You cannot use views to manage access control on data in Amazon S3\. To query a view, you need permissions to access the data stored in Amazon S3\. For more information, see [Access to Amazon S3](s3-permissions.md)\.
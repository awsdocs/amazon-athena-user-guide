# February 8, 2022<a name="release-note-2022-02-08"></a>

Published on 2022\-02\-08

**Expected bucket owner** – As an added security measure, you can now optionally specify the AWS account ID that you expect to be the owner of your query results output location bucket in Athena\. If the account ID of the query results bucket owner does not match the account ID that you specify, attempts to output to the bucket will fail with an Amazon S3 permissions error\. You can make this setting at the client or workgroup level\.

For more information, see [Specifying a Query Result Location](querying.md#query-results-specify-location)\.
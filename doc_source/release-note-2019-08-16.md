# August 16, 2019<a name="release-note-2019-08-16"></a>

Published on 2019\-12\-17

[Amazon Athena](https://aws.amazon.com/athena/) adds support for querying data in Amazon S3 Requester Pays buckets\.

When an Amazon S3 bucket is configured as Requester Pays, the requester, not the bucket owner, pays for the Amazon S3 request and data transfer costs\. In Athena, workgroup administrators can now configure workgroup settings to allow workgroup members to query S3 Requester Pays buckets\.

For information about how to configure the Requester Pays setting for your workgroup, refer to [Create a Workgroup](https://docs.aws.amazon.com/athena/latest/ug/workgroups-create-update-delete.html#creating-workgroups) in the Amazon Athena User Guide\. For more information about Requester Pays buckets, see [Requester Pays Buckets](https://docs.aws.amazon.com/AmazonS3/latest/dev/RequesterPaysBuckets.html) in the Amazon Simple Storage Service Developer Guide\.
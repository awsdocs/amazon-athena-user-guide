# February 22, 2019<a name="release-note-2019-02-22"></a>

Published on *2019\-02\-22*

Added tag support for workgroups in Amazon Athena\. A tag consists of a key and a value, both of which you define\. When you tag a workgroup, you assign custom metadata to it\. You can add tags to workgroups to help categorize them, using [AWS tagging best practices](https://d1.awsstatic.com/whitepapers/aws-tagging-best-practices.pdf)\. You can use tags to restrict access to workgroups, and to track costs\. For example, create a workgroup for each cost center\. Then, by adding tags to these workgroups, you can track your Athena spending for each cost center\. For more information, see [Using Tags for Billing](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/custom-tags.html) in the *AWS Billing and Cost Management User Guide*\.

You can work with tags by using the Athena console or the API operations\. For more information, see [Tagging Workgroups](tags.md)\. 

In the Athena console, you can add one or more tags to each of your workgroups, and search by tags\. Workgroups are an IAM\-controlled resource in Athena\. In IAM, you can restrict who can add, remove, or list tags on workgroups that you create\. You can also use the `CreateWorkGroup` API operation that has the optional tag parameter for adding one or more tags to the workgroup\. To add, remove, or list tags, use `TagResource`, `UntagResource`, and `ListTagsForResource`\. For more information, see [Working with Tags Using the API Actions](tags.md)\.

To allow users to add tags when creating workgroups, ensure that you give each user IAM permissions to both the `TagResource` and `CreateWorkGroup` API actions\. For more information and examples, see [Tag\-based IAM access control policies](tags-access-control.md)\. 

There are no changes to the JDBC driver when you use tags on workgroups\. If you create new workgroups and use the JDBC driver or the AWS SDK, upgrade to the latest version of the driver and SDK\. For information, see [Using Athena with the JDBC driver](connect-with-jdbc.md)\.
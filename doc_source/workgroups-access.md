# Access to Workgroups and Tags<a name="workgroups-access"></a>

A workgroup is a resource managed by Athena\. Therefore, if your workgroup policy uses actions that take `workgroup` as an input, you must specify the workgroup's ARN as follows, where `workgroup-name` is the name of your workgroup:

```
"Resource": [arn:aws:athena:region:AWSAcctID:workgroup/workgroup-name]
```

For example, for a workgroup named `test_workgroup` in the `us-west-2` region for AWS account `123456789012`, specify the workgroup as a resource using the following ARN:

```
"Resource":["arn:aws:athena:us-east-2:123456789012:workgroup/test_workgroup"]
```
+ For a list of workgroup policies, see [Workgroup Example Policies](example-policies-workgroup.md)\.
+ For a list of tag\-based policies for workgroups, see [Tag\-Based IAM Access Control Policies](tags-access-control.md)\.
+ For more information about creating IAM policies for workgroups, see [Workgroup IAM Policies](workgroups-iam-policy.md)\.
+ For a complete list of Amazon Athena actions, see the API action names in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. 
+ For more information about IAM policies, see [Creating Policies with the Visual Editor](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-visual-editor) in the *IAM User Guide*\. 

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.
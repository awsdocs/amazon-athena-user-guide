# Workgroup and Tag Policies<a name="workgroups-access"></a>

A workgroup is a resource managed by Athena\. Therefore, if your workgroup policy uses actions that take `workgroup` as an input, you must specify the workgroup's ARN as follows:

```
"Resource": [arn:aws:athena:<region>:<user-account>:workgroup/<workgroup-name>]
```

Where `<workgroup-name>` is the name of your workgroup\. For example, for workgroup named `test_workgroup`, specify it as a resource as follows:

```
"Resource": ["arn:aws:athena:us-east-1:123456789012:workgroup/test_workgroup"]
```

For a full list of example policies, see [Workgroup Example Policies](example-policies-workgroup.md)\.

For a list of tag\-based policies for workgroups, see [Tag\-Based IAM Access Control Policies](tags-access-control.md)\.

For a complete list of Amazon Athena actions, see the API action names in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/Welcome.html)\. 

For more information about IAM policies, see [Creating Policies with the Visual Editor](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-visual-editor) in the *IAM User Guide*\. 

For more information about creating IAM policies for workgroups, see [Workgroup IAM Policies](workgroups-iam-policy.md)\.
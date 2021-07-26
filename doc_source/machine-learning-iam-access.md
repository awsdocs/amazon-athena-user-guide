# Allowing Access for ML with Athena<a name="machine-learning-iam-access"></a>

IAM principals who run Athena ML queries must be allowed to perform the `sagemaker:invokeEndpoint` action for Sagemaker endpoints that they use\. Include a policy statement similar to the following in identity\-based permissions policies attached to user identities\. In addition, attach the [AWS managed policy: AmazonAthenaFullAccess](managed-policies.md#amazonathenafullaccess-managed-policy), which grants full access to Athena actions, or a modified inline policy that allows a subset of actions\.

Replace `arn:aws:sagemaker:region:AWSAcctID:ModelEndpoint` in the example with the ARN or ARNs of model endpoints to be used in queries\. For more information, see [Actions, Resources, and Condition Keys for SageMaker](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonsagemaker.html) in the *Service Authorization Reference*\.

```
{
            "Effect": "Allow",
            "Action": [
                "sagemaker:invokeEndpoint"
            ],
            "Resource": "arn:aws:sagemaker:us-west-2:123456789012:workteam/public-crowd/default"
}
```

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.
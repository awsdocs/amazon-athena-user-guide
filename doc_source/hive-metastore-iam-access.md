# Allow access to an Athena Data Connector for External Hive Metastore<a name="hive-metastore-iam-access"></a>

The permission policy examples in this topic demonstrate required allowed actions and the resources for which they are allowed\. Examine these policies carefully and modify them according to your requirements before you attach similar permissions policies to IAM identities\.
+  [Example Policy to Allow an IAM Principal to Query Data Using Athena Data Connector for External Hive Metastore](#hive-using-iam) 
+  [Example Policy to Allow an IAM Principal to Create an Athena Data Connector for External Hive Metastore](#hive-creating-iam) 

**Example – Allow an IAM principal to query data using Athena Data Connector for External Hive Metastore**  
The following policy is attached to IAM principals in addition to the [AWS managed policy: AmazonAthenaFullAccess](managed-policies.md#amazonathenafullaccess-managed-policy), which grants full access to Athena actions\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "lambda:GetFunction",
                "lambda:GetLayerVersion",
                "lambda:InvokeFunction"
            ],
            "Resource": [
                "arn:aws:lambda:*:111122223333:function:MyAthenaLambdaFunction",
                "arn:aws:lambda:*:111122223333:function:AnotherAthenaLambdaFunction",
                "arn:aws:lambda:*:111122223333:layer:MyAthenaLambdaLayer:*"
            ]
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:ListBucket",
                "s3:PutObject",
                "s3:ListMultipartUploadParts",
                "s3:AbortMultipartUpload"
            ],
            "Resource": "arn:aws:s3:::MyLambdaSpillBucket/MyLambdaSpillLocation"
        }
    ]
}
```


**Explanation of permissions**  

| Allowed actions | Explanation | 
| --- | --- | 
|  <pre>"s3:GetBucketLocation",<br />"s3:GetObject",<br />"s3:ListBucket",<br />"s3:PutObject",<br />"s3:ListMultipartUploadParts",<br />"s3:AbortMultipartUpload"</pre>  |  `s3` actions allow reading from and writing to the resource specified as `"arn:aws:s3:::MyLambdaSpillBucket/MyLambdaSpillLocation"`, where *MyLambdaSpillLocation* identifies the spill bucket that is specified in the configuration of the Lambda function or functions being invoked\. The *arn:aws:lambda:\*:*MyAWSAcctId*:layer:*MyAthenaLambdaLayer*:\** resource identifier is required only if you use a Lambda layer to create custom runtime dependencies to reduce function artifact size at deployment time\. The `*` in the last position is a wildcard for layer version\.  | 
|  <pre>"lambda:GetFunction",<br />"lambda:GetLayerVersion",<br />"lambda:InvokeFunction"</pre>  | Allows queries to invoke the AWS Lambda functions specified in the Resource block\. For example, arn:aws:lambda:\*:MyAWSAcctId:function:MyAthenaLambdaFunction, where MyAthenaLambdaFunction specifies the name of a Lambda function to be invoked\. Multiple functions can be specified as shown in the example\. | 

**Example – Allow an IAM principal to create an Athena Data Connector for External Hive Metastore**  
The following policy is attached to IAM principals in addition to the [AWS managed policy: AmazonAthenaFullAccess](managed-policies.md#amazonathenafullaccess-managed-policy), which grants full access to Athena actions\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "lambda:GetFunction",
                "lambda:ListFunctions",
                "lambda:GetLayerVersion",
                "lambda:InvokeFunction",
                "lambda:CreateFunction",
                "lambda:DeleteFunction",
                "lambda:PublishLayerVersion",
                "lambda:DeleteLayerVersion",
                "lambda:UpdateFunctionConfiguration",
                "lambda:PutFunctionConcurrency",
                "lambda:DeleteFunctionConcurrency"
            ],
            "Resource": "arn:aws:lambda:*:111122223333: function: MyAthenaLambdaFunctionsPrefix*"
        }
    ]
}
```
 **Explanation of Permissions**   
Allows queries to invoke the AWS Lambda functions for the AWS Lambda functions specified in the `Resource` block\. For example, `arn:aws:lambda:*:MyAWSAcctId:function:MyAthenaLambdaFunction`, where *MyAthenaLambdaFunction* specifies the name of a Lambda function to be invoked\. Multiple functions can be specified as shown in the example\.
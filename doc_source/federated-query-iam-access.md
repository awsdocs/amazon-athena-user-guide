# Example IAM Permissions Policies to Allow Athena Federated Query<a name="federated-query-iam-access"></a>

The permission policy examples in this topic demonstrate required allowed actions and the resources for which they are allowed\. Examine these policies carefully and modify them according to your requirements before attaching them to IAM identities\. Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

For information about attaching policies to IAM identities, see [Adding and Removing IAM Identity Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html) in the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.
+  [Example Policy to Allow an IAM Principal to Run and Return Results Using Athena Federated Query](#fed-using-iam) 
+  [Example Policy to Allow an IAM Principal to Create a Data Source Connector](#fed-creating-iam) 

**Example – Allow an IAM Principal to Run and Return Results Using Athena Federated Query**  
The following identity\-based permissions policy allows actions that a user or other IAM principal requires to use Athena Federated Query\. Principals who are allowed to perform these actions are able to run queries that specify Athena catalogs associated with a federated data source\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "athena:GetWorkGroup",
                "s3:PutObject",
                "s3:GetObject",
                "athena:StartQueryExecution",
                "s3:AbortMultipartUpload",
                "lambda:InvokeFunction",
                "athena:CancelQueryExecution",
                "athena:StopQueryExecution",
                "athena:GetQueryExecution",
                "athena:GetQueryResults",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": [
                "arn:aws:athena:*:MyAWSAcctId:workgroup/WorkgroupName",
                "arn:aws:s3:::MyQueryResultsBucket/*",
                "arn:aws:s3:::MyLambdaSpillBucket/MyLambdaSpillPrefix*",
                "arn:aws:lambda:*:MyAWSAcctId:function:OneAthenaLambdaFunction",
                "arn:aws:lambda:*:MyAWSAcctId:function:AnotherAthenaLambdaFunction"
            ]
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "athena:ListWorkGroups",
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::MyLambdaSpillBucket"
        }
    ]
}
```


**Explanation of Permissions**  

| Allowed Actions | Explanation | 
| --- | --- | 
|  <pre> "athena:StartQueryExecution",<br /> "athena:GetQueryResults",<br /> "athena:GetWorkGroup",<br /> "athena:CancelQueryExecution",<br /> "athena:StopQueryExecution",<br /> "athena:GetQueryExecution",<br /></pre>  |  Athena permissions that are required to run federated queries\.  | 
|  <pre>"s3:PutObject",<br />"s3:GetObject",<br />"s3:AbortMultipartUpload"</pre>  |  `s3:PutObject` and `s3:AbortMultipartUpload` allow writing query results to all sub\-folders of the query results bucket as specified by the `arn:aws:s3:::MyQueryResultsBucket/*` resource identifier, where *MyQueryResultsBucket* is the Athena query results bucket\. For more information, see [Working with Query Results, Output Files, and Query History](querying.md)\. `s3:GetObject` allows reading of query results and query history for the resource specified as `arn:aws:s3:::MyQueryResultsBucket`, where *MyQueryResultsBucket* is the Athena query results bucket\. `s3:GetObject` also allows reading from the resource specified as `"arn:aws:s3:::MyLambdaSpillBucket/MyLambdaSpillPrefix*"`, where *MyLambdaSpillPrefix* is specified in the configuration of the Lambda function or functions being invoked\.  | 
|  <pre>"lambda:InvokeFunction"</pre>  | Allows queries to invoke the AWS Lambda functions for the AWS Lambda functions specified in the Resource block\. For example, arn:aws:lambda:\*:MyAWSAcctId:function:MyAthenaLambdaFunction, where MyAthenaLambdaFunction specifies the name of a Lambda function to be invoked\. Multiple functions can be specified as shown in the example\. | 

**Example – Allow an IAM Principal to Create a Data Source Connector**  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "lambda:CreateFunction",
                "lambda:ListVersionsByFunction",
                "iam:CreateRole",
                "lambda:GetFunctionConfiguration",
                "iam:AttachRolePolicy",
                "iam:PutRolePolicy",
                "lambda:PutFunctionConcurrency",
                "iam:PassRole",
                "iam:DetachRolePolicy",
                "lambda:ListTags",
                "iam:ListAttachedRolePolicies",
                "iam:DeleteRolePolicy",
                "lambda:DeleteFunction",
                "lambda:GetAlias",
                "iam:ListRolePolicies",
                "iam:GetRole",
                "iam:GetPolicy",
                "lambda:InvokeFunction",
                "lambda:GetFunction",
                "lambda:ListAliases",
                "lambda:UpdateFunctionConfiguration",
                "iam:DeleteRole",
                "lambda:UpdateFunctionCode",
                "s3:GetObject",
                "lambda:AddPermission",
                "iam:UpdateRole",
                "lambda:DeleteFunctionConcurrency",
                "lambda:RemovePermission",
                "iam:GetRolePolicy",
                "lambda:GetPolicy"
            ],
            "Resource": [
                "arn:aws:lambda:*:MyAWSAcctId:function:MyAthenaLambdaFunctionsPrefix*",
                "arn:aws:s3:::awsserverlessrepo-changesets-1iiv3xa62ln3m/*",
                "arn:aws:iam::*:role/*",
                "arn:aws:iam::MyAWSAcctId:policy/*"
            ]
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "cloudformation:CreateUploadBucket",
                "cloudformation:DescribeStackDriftDetectionStatus",
                "cloudformation:ListExports",
                "cloudformation:ListStacks",
                "cloudformation:ListImports",
                "lambda:ListFunctions",
                "iam:ListRoles",
                "lambda:GetAccountSettings",
                "ec2:DescribeSecurityGroups",
                "cloudformation:EstimateTemplateCost",
                "ec2:DescribeVpcs",
                "lambda:ListEventSourceMappings",
                "cloudformation:DescribeAccountLimits",
                "ec2:DescribeSubnets",
                "cloudformation:CreateStackSet",
                "cloudformation:ValidateTemplate"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": "cloudformation:*",
            "Resource": [
                "arn:aws:cloudformation:*:MyAWSAcctId:stack/aws-serverless-repository-MyCFStackPrefix*/*",
                "arn:aws:cloudformation:*:MyAWSAcctId:stack/serverlessrepo-MyCFStackPrefix*/*",
                "arn:aws:cloudformation:*:*:transform/Serverless-*",
                "arn:aws:cloudformation:*:MyAWSAcctId:stackset/aws-serverless-repository-MyCFStackPrefix*:*",
                "arn:aws:cloudformation:*:MyAWSAcctId:stackset/serverlessrepo-MyCFStackPrefix*:*"
            ]
        },
        {
            "Sid": "VisualEditor3",
            "Effect": "Allow",
            "Action": "serverlessrepo:*",
            "Resource": "arn:aws:serverlessrepo:*:*:applications/*"
        }
    ]
}
```


**Explanation of Permissions**  

| Allowed Actions | Explanation | 
| --- | --- | 
|  <pre>"lambda:CreateFunction",<br />"lambda:ListVersionsByFunction",<br />"lambda:GetFunctionConfiguration",<br />"lambda:PutFunctionConcurrency",<br />"lambda:ListTags",<br />"lambda:DeleteFunction",<br />"lambda:GetAlias",<br />"lambda:InvokeFunction",<br />"lambda:GetFunction",<br />"lambda:ListAliases",<br />"lambda:UpdateFunctionConfiguration",<br />"lambda:UpdateFunctionCode",<br />"lambda:AddPermission",<br />"lambda:DeleteFunctionConcurrency",<br />"lambda:RemovePermission",<br />"lambda:GetPolicy"<br />"lambda:GetAccountSettings",<br />"lambda:ListFunctions",<br />"lambda:ListEventSourceMappings",<br /></pre>  |  Allow the creation and management of Lambda functions listed as resources\. In the example, a name prefix is used in the resource identifier `arn:aws:lambda:*:MyAWSAcctId:function:MyAthenaLambdaFunctionsPrefix*`, where *MyAthenaLambdaFunctionsPrefix* is a shared prefix used in the name of a group of Lambda functions so that they don't need to be specified individually as resources\. You can specify one or more Lambda function resources\.  | 
|  <pre>"s3:GetObject"</pre>  | Allows reading of a bucket that AWS Serverless Application Repository requires as specified by the resource identifier arn:aws:s3:::awsserverlessrepo\-changesets\-1iiv3xa62ln3m/\*\. This bucket may be specific to your account\. | 
|  <pre>"cloudformation:*"</pre>  |  Allows the creation and management of AWS CloudFormation stacks specified by the resource *MyCFStackPrefix*\. These stacks and stacksets are how AWS Serverless Application Repository deploys connectors and UDFs\.  | 
|  <pre>"serverlessrepo:*"</pre>  | Allows searching, viewing, publishing, and updating applications in the AWS Serverless Application Repository, specified by the resource identifier arn:aws:serverlessrepo:\*:\*:applications/\*\. | 
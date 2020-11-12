# Using Athena with CalledVia Context Keys<a name="security-iam-athena-calledvia"></a>

When a [principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html#intro-structure-principal) makes a [request](https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html#intro-structure-request) to AWS, AWS gathers the request information into a *request context* that evaluates and authorizes the request\. You can use the `Condition` element of a JSON policy to compare keys in the request context with key values that you specify in your policy\. *Global condition context keys* are condition keys with an `aws:` prefix\.

## The aws:CalledVia Context Key<a name="security-iam-athena-calledvia-the-awscalledvia-context-key"></a>

You can use the [aws:CalledVia](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-calledvia) global condition context key to compare the services in the policy with the services that made requests on behalf of the IAM principal \(user or role\)\. When a principal makes a request to an AWS service, that service might use the principal's credentials to make subsequent requests to other services\. The `aws:CalledVia` key contains an ordered list of each service in the chain that made requests on the principal's behalf\.

By specifying a service principal name for the `aws:CalledVia` context key, you can make the context key AWS service\-specific\. For example, you can use the `aws:CalledVia` condition key to limit requests to only those made from Athena\. To use the `aws:CalledVia` condition key in a policy with Athena, you specify the Athena service principal name `athena.amazonaws.com`, as in the following example\.

```
 ...
    "Condition": {
        "ForAnyValue:StringEquals": { 
            "aws:CalledVia": "athena.amazonaws.com"
        }
    }
...
```

You can use the `aws:CalledVia` context key to ensure that callers only have access to a resource \(like a Lambda function\) if they call the resource from Athena\.

## Add an Optional CalledVia Context Key for Fine Grained Access to a Lambda function<a name="security-iam-athena-calledvia-example-policy-to-add-an-optional-calledvia-context-key-for-fine-grained-access-to-a-lambda-function"></a>

Athena requires the caller to have `lambda:InvokeFunction` permissions in order to invoke the Lambda function associated with the query\. The following statement allows fine\-grained access to a Lambda function so that the user can use only Athena to invoke the Lambda function\.

```
{
    "Sid": "VisualEditor3",
    "Effect": "Allow",
    "Action": "lambda:InvokeFunction",
    "Resource": "arn:aws:lambda:us-east-1:MyAWSAcctId:function:OneAthenaLambdaFunction",
    "Condition": {
        "ForAnyValue:StringEquals": { 
            "aws:CalledVia": "athena.amazonaws.com"
        }
    }
}
```

The following example shows the addition of the previous statement to a policy that allows a user to run and read a federated query\. Principals who are allowed to perform these actions can run queries that specify Athena catalogs associated with a federated data source\. However, the principal cannot access the associated Lambda function unless the function is invoked through Athena\.

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
                "athena:CancelQueryExecution", 
                "athena:StopQueryExecution", 
                "athena:GetQueryExecution", 
                "athena:GetQueryResults", 
                "s3:ListMultipartUploadParts" 
            ], 
            "Resource": [ 
                "arn:aws:athena:*:MyAWSAcctId:workgroup/AmazonAthenaPreviewFunctionality",
                "arn:aws:s3:::MyQueryResultsBucket/*", 
                "arn:aws:s3:::MyLambdaSpillBucket/MyLambdaSpillPrefix*"
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
            "Action": 
                [ 
                "s3:ListBucket", 
                "s3:GetBucketLocation" 
                ], 
            "Resource": "arn:aws:s3:::MyLambdaSpillBucket" 
        },
        {
            "Sid": "VisualEditor3",
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": [
                "arn:aws:lambda:*:MyAWSAcctId:function:OneAthenaLambdaFunction", 
                "arn:aws:lambda:*:MyAWSAcctId:function:AnotherAthenaLambdaFunction"
            ], 
            "Condition": {
                "ForAnyValue:StringEquals": { 
                    "aws:CalledVia": "athena.amazonaws.com"
                }
            }
        }            
    ]
}
```

For more information about `CalledVia` condition keys, see [AWS global condition context keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) in the *IAM User Guide*\.
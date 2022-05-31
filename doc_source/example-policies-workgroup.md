# Workgroup example policies<a name="example-policies-workgroup"></a>

This section includes example policies you can use to enable various actions on workgroups\. Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

A workgroup is an IAM resource managed by Athena\. Therefore, if your workgroup policy uses actions that take `workgroup` as an input, you must specify the workgroup's ARN as follows:

```
"Resource": [arn:aws:athena:<region>:<user-account>:workgroup/<workgroup-name>]
```

Where `<workgroup-name>` is the name of your workgroup\. For example, for workgroup named `test_workgroup`, specify it as a resource as follows:

```
"Resource": ["arn:aws:athena:us-east-1:123456789012:workgroup/test_workgroup"]
```

For a complete list of Amazon Athena actions, see the API action names in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. For more information about IAM policies, see [Creating policies with the visual editor](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-visual-editor) in the *IAM User Guide*\. For more information about creating IAM policies for workgroups, see [ IAM policies for accessing workgroups](workgroups-iam-policy.md)\.
+  [Example Policy for Full Access to All Workgroups](#example1-full-access-all-wkgs) 
+  [Example Policy for Full Access to a Specified Workgroup](#example2-full-access-this-wkg) 
+  [Example Policy for Running Queries in a Specified Workgroup](#example3-user-access) 
+  [Example Policy for Running Queries in the Primary Workgroup](#example4-run-in-primary-access) 
+  [Example Policy for Management Operations on a Specified Workgroup](#example5-manage-wkgs-access) 
+  [Example Policy for Listing Workgroups](#example6-list-all-wkgs-access) 
+  [Example Policy for Running and Stopping Queries in a Specific Workgroup](#example7-run-queries-access) 
+  [Example Policy for Working with Named Queries in a Specific Workgroup](#example8-named-queries-access) 

**Example policy for full access to all workgroups**  
The following policy allows full access to all workgroup resources that might exist in the account\. We recommend that you use this policy for those users in your account that must administer and manage workgroups for all other users\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:*"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

**Example policy for full access to a specified workgroup**  
The following policy allows full access to the single specific workgroup resource, named `workgroupA`\. You could use this policy for users with full control over a particular workgroup\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:ListEngineVersions",
                "athena:ListWorkGroups",
                "athena:ListDataCatalogs",
                "athena:ListDatabases",
                "athena:GetDatabase",
                "athena:ListTableMetadata",
                "athena:GetTableMetadata"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "athena:BatchGetQueryExecution",
                "athena:GetQueryExecution",
                "athena:ListQueryExecutions",
                "athena:StartQueryExecution",
                "athena:StopQueryExecution",
                "athena:GetQueryResults",
                "athena:GetQueryResultsStream",
                "athena:CreateNamedQuery",
                "athena:GetNamedQuery",
                "athena:BatchGetNamedQuery",
                "athena:ListNamedQueries",
                "athena:DeleteNamedQuery",
                "athena:CreatePreparedStatement",
                "athena:GetPreparedStatement",
                "athena:ListPreparedStatements",
                "athena:UpdatePreparedStatement",
                "athena:DeletePreparedStatement"
            ],
            "Resource": [
                "arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "athena:DeleteWorkGroup",
                "athena:UpdateWorkGroup",
                "athena:GetWorkGroup",
                "athena:CreateWorkGroup"
            ],
            "Resource": [
                "arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA"
            ]
        }
    ]
}
```

**Example policy for running queries in a specified workgroup**  
In the following policy, a user is allowed to run queries in the specified `workgroupA`, and view them\. The user is not allowed to perform management tasks for the workgroup itself, such as updating or deleting it\.   

```
{
    "Version": "2012-10-17",
    "Statement": [
       {
            "Effect": "Allow",
            "Action": [
                "athena:ListEngineVersions",
                "athena:ListWorkGroups",
                "athena:ListDataCatalogs",
                "athena:ListDatabases",
                "athena:GetDatabase",
                "athena:ListTableMetadata",
                "athena:GetTableMetadata"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "athena:GetWorkGroup", 
                "athena:BatchGetQueryExecution",
                "athena:GetQueryExecution",
                "athena:ListQueryExecutions",
                "athena:StartQueryExecution",
                "athena:StopQueryExecution",
                "athena:GetQueryResults",
                "athena:GetQueryResultsStream",
                "athena:CreateNamedQuery",
                "athena:GetNamedQuery",
                "athena:BatchGetNamedQuery",
                "athena:ListNamedQueries",
                "athena:DeleteNamedQuery",
                "athena:CreatePreparedStatement",
                "athena:GetPreparedStatement",
                "athena:ListPreparedStatements",
                "athena:UpdatePreparedStatement",
                "athena:DeletePreparedStatement"
            ],
            "Resource": [
                "arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA"
            ]
        }
    ]
}
```

**Example policy for running queries in the primary workgroup**  
You can modify the preceding example to allow a particular user to also run queries in the primary workgroup\.   
We recommend that you add the primary workgroup resource for all users who are otherwise configured to run queries in their designated workgroups\. Adding this resource to their workgroup user policies is useful in case their designated workgroup is deleted or is disabled\. In this case, they can continue running queries in the primary workgroup\.
To allow users in your account to run queries in the primary workgroup, add a line that contains the ARN of the primary workgroup to the resource section of the [Example Policy for Running Queries in a Specified Workgroup](#example3-user-access), as in the following example\.  

```
arn:aws:athena:us-east-1:123456789012:workgroup/primary"
```

**Example policy for management operations on a specified workgroup**  
In the following policy, a user is allowed to create, delete, obtain details, and update a workgroup `test_workgroup`\.   

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:ListEngineVersions"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "athena:CreateWorkGroup",
                "athena:GetWorkGroup",
                "athena:DeleteWorkGroup",
                "athena:UpdateWorkGroup"
            ],
            "Resource": [
                "arn:aws:athena:us-east-1:123456789012:workgroup/test_workgroup"
            ]
        }
    ]
}
```

**Example policy for listing workgroups**  
The following policy allows all users to list all workgroups:  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:ListWorkGroups"
            ],
            "Resource": "*"
        }
    ]
}
```

**Example policy for running and stopping queries in a specific workgroup**  
In this policy, a user is allowed to run queries in the workgroup:  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:StartQueryExecution",
                "athena:StopQueryExecution"
            ],
            "Resource": [
                "arn:aws:athena:us-east-1:123456789012:workgroup/test_workgroup"
            ]
        }
    ]
}
```

**Example policy for working with named queries in a specific workgroup**  
In the following policy, a user has permissions to create, delete, and obtain information about named queries in the specified workgroup:  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:CreateNamedQuery",
                "athena:GetNamedQuery",
                "athena:DeleteNamedQuery"
            ],
            "Resource": [
                "arn:aws:athena:us-east-1:123456789012:workgroup/test_workgroup"            ]
        }
    ]
}
```
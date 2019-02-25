# Tag\-Based IAM Access Control Policies<a name="tags-access-control"></a>

Having tags allows you to write an IAM policy that includes the Condition block to control access to workgroups based on their tags\. 

## Tag Policy Examples<a name="tag-policy-examples"></a>

**Example 1\. Basic Tagging Policy**  
The following IAM policy allows you to run queries and interact with tags for the workgroup named `workgroupA`:  

```
{
    "Version": "2012-10-17",
    "Statement": [
       {
            "Effect": "Allow",
            "Action": [
                "athena:ListWorkGroups",
                "athena:GetExecutionEngine",
                "athena:GetExecutionEngines",
                "athena:GetNamespace",
                "athena:GetCatalogs",
                "athena:GetNamespaces",
                "athena:GetTables",
                "athena:GetTable"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "athena:StartQueryExecution",
                "athena:GetQueryResults",
                "athena:DeleteNamedQuery",
                "athena:GetNamedQuery",
                "athena:ListQueryExecutions",
                "athena:StopQueryExecution",
                "athena:GetQueryResultsStream",
                "athena:GetQueryExecutions",
                "athena:ListNamedQueries",
                "athena:CreateNamedQuery",
                "athena:GetQueryExecution",
                "athena:BatchGetNamedQuery",
                "athena:BatchGetQueryExecution", 
                "athena:GetWorkGroup",
                "athena:TagResource",
                "athena:UntagResource",
                "athena:ListTagsForResource" 
            ],
            "Resource": "arn:aws:athena:us-east-1:123456789012:workgroup/workgroupA"
        }
    ]
}
```

**Example 2: Policy Block that Denies Actions on a Workgroup Based on a Tag Key and Tag Value Pair**  
Tags that are associated with an existing workgroup are referred to as resource tags\. Resource tags let you write policy blocks, such as the following, which deny the listed actions on any workgroup tagged with tag key and tag value pair, such as: `stack`, `production`\.  

```
{
    "Effect": "Deny",
    "Action": [
        "athena:StartQueryExecution",
        "athena:GetQueryResults",
        "athena:DeleteNamedQuery",
        "athena:UpdateWorkGroup",
        "athena:GetNamedQuery",
        "athena:ListQueryExecutions",
        "athena:GetWorkGroup",
        "athena:StopQueryExecution",
        "athena:GetQueryResultsStream",
        "athena:GetQueryExecutions",
        "athena:ListNamedQueries",
        "athena:CreateNamedQuery",
        "athena:GetQueryExecution",
        "athena:BatchGetNamedQuery",
        "athena:BatchGetQueryExecution",
        "athena:TagResource",
        "athena:UntagResource",
        "athena:ListTagsForResource"
    ],
    "Resource": "arn:aws:athena:us-east-1:123456789012:workgroup/*",
    "Condition": {
        "StringEquals": {
            "aws:ResourceTag/stack": "production"
        }
    }
}
```

**Example 3\. Policy Block that Restricts Tag\-Changing Action Requests to Specified Tags**  
Tags passed in as parameters to a tag\-mutating API action, such as `CreateWorkGroup` with tags, `TagResource`, and `UntagResource`, are referred to as request tags\. Use these tags, as shown in the following example policy block\. This allows CreateWorkGroup only if one of the tags included when you create a workgroup is a tag with the `costcenter` key with one of the allowed tag values: `1`, `2`, or `3`\.  
Note: Make sure that you give a user IAM permissions to the `TagResource` and `CreateWorkGroup` API operations, if you want to allow them to pass in tags upon `CreateWorkGroup`\.  

```
{
    "Effect": "Allow",
    "Action": [
        "athena:CreateWorkGroup",
        "athena:TagResource"
    ],
    "Resource": "arn:aws:athena:us-east-1:123456789012:workgroup/*",
    "Condition": {
        "StringEquals": {
            "aws:RequestTag/costcenter": [
                "1",
                "2",
                "3"
            ]
        }
    }
}
```
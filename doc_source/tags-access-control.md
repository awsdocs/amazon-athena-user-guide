# Tag\-Based IAM Access Control Policies<a name="tags-access-control"></a>

Having tags allows you to write an IAM policy that includes the `Condition` block to control access to a resource based on its tags\. 

## Tag Policy Examples for Workgroups<a name="tag-policy-examples-workgroups"></a>

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
Tags that are associated with a resource like a workgroup are referred to as resource tags\. Resource tags let you write policy blocks like the following that deny the listed actions on any workgroup tagged with a key\-value pair like `stack`, `production`\.  

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
Tags that are passed in as parameters to operations that change tags \(for example, `TagResource`, `UntagResource`, or `CreateWorkGroup` with tags\) are referred to as request tags\. The following example policy block allows the `CreateWorkGroup` operation only if one of the tags passed has the key `costcenter` and the value `1`, `2`, or `3`\.  
If you want to allow IAM users to pass in tags as part of a `CreateWorkGroup` operation, make sure that you give the users permissions to the `TagResource` and `CreateWorkGroup` actions\.

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

## Tag Policy Examples for Data Catalogs<a name="tag-policy-examples-data-catalogs"></a>

**Example 1\. Basic Tagging Policy**  
The following IAM policy allows you to interact with tags for the data catalog named `datacatalogA`:  

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "athena:ListWorkGroups",
            "athena:ListDataCatalogs",
            "athena:GetExecutionEngine",
            "athena:GetExecutionEngines",
            "athena:GetNamespace",
            "athena:GetNamespaces",
            "athena:GetTables",
            "athena:GetTable"
         ],
         "Resource":"*"
      },
      {
         "Effect":"Allow",
         "Action":[
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
         "Resource": [
            "arn:aws:athena:us-east-1:123456789012:workgroup/*"
         ]
      },
      {
        "Effect":"Allow",
        "Action":[
            "athena:CreateDataCatalog",
            "athena:DeleteDataCatalog",
            "athena:GetDataCatalog",
            "athena:GetDatabase",
            "athena:GetTableMetadata",
            "athena:ListDatabases",
            "athena:ListTableMetadata",
            "athena:UpdateDataCatalog",
            "athena:TagResource",
            "athena:UntagResource",
            "athena:ListTagsForResource"
        ],
        "Resource":"arn:aws:athena:us-east-1:123456789012:datacatalog/datacatalogA"
      }
   ]
}
```

**Example 2: Policy Block that Denies Actions on a Data Catalog Based on a Tag Key and Tag Value Pair**  
You can use resource tags to write policy blocks that deny specific actions on data catalogs that are tagged with specific tag key\-value pairs\. The following example policy denies actions on data catalogs that have the tag key\-value pair `stack`, `production`\.  

```
{
   "Effect":"Deny",
   "Action":[
      "athena:CreateDataCatalog",
      "athena:DeleteDataCatalog",
      "athena:GetDataCatalog",
      "athena:GetDatabase",
      "athena:GetTableMetadata",
      "athena:ListDatabases",
      "athena:ListTableMetadata",
      "athena:UpdateDataCatalog",
      "athena:StartQueryExecution",
      "athena:TagResource",
      "athena:UntagResource",
      "athena:ListTagsForResource"
   ],
   "Resource":"arn:aws:athena:us-east-1:123456789012:datacatalog/*",
   "Condition":{
      "StringEquals":{
         "aws:ResourceTag/stack":"production"
      }
   }
}
```

**Example 3\. Policy Block that Restricts Tag\-Changing Action Requests to Specified Tags**  
Tags that are passed in as parameters to operations that change tags \(for example, `TagResource`, `UntagResource`, or `CreateDataCatalog` with tags\) are referred to as request tags\. The following example policy block allows the `CreateDataCatalog` operation only if one of the tags passed has the key `costcenter` and the value `1`, `2`, or `3`\.  
If you want to allow IAM users to pass in tags as part of a `CreateDataCatalog` operation, make sure that you give the users permissions to the `TagResource` and `CreateDataCatalog` actions\.

```
{
   "Effect":"Allow",
   "Action":[
      "athena:CreateDataCatalog",
      "athena:TagResource"
   ],
   "Resource":"arn:aws:athena:us-east-1:123456789012:datacatalog/*",
   "Condition":{
      "StringEquals":{
         "aws:RequestTag/costcenter":[
            "1",
            "2",
            "3"
         ]
      }
   }
}
```
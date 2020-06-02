# Data Catalog Example Policies<a name="datacatalogs-example-policies"></a>

This section includes example policies you can use to enable various actions on data catalogs\.

A data catalog is an IAM resource managed by Athena\. Therefore, if your data catalog policy uses actions that take `datacatalog` as an input, you must specify the data catalog's ARN as follows:

```
"Resource": [arn:aws:athena:<region>:<user-account>:datacatalog/<datacatalog-name>]
```

The `<datacatalog-name>` is the name of your data catalog\. For example, for a data catalog named `test_datacatalog`, specify it as a resource as follows:

```
"Resource": ["arn:aws:athena:us-east-1:123456789012:datacatalog/test_datacatalog"]
```

For a complete list of Amazon Athena actions, see the API action names in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. For more information about IAM policies, see [Creating Policies with the Visual Editor](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-visual-editor) in the *IAM User Guide*\. For more information about creating IAM policies for workgroups, see [ IAM Policies for Accessing Data Catalogs](datacatalogs-iam-policy.md)\.
+ [Example Policy for Full Access to All Data Catalogs](#datacatalog-policy-full-access-to-all-data-catalogs)
+ [Example Policy for Full Access to a Specified Data Catalog](#datacatalog-policy-full-access-to-a-specified-catalog)
+ [Example Policy for Querying a Specified Data Catalog](#datacatalog-policy-querying-a-specified-data-catalog)
+ [Example Policy for Management Operations on a Specified Data Catalog](#datacatalog-policy-management-operations-on-a-specified-catalog)
+ [Example Policy for Listing Data Catalogs](#datacatalog-policy-listing-data-catalogs)
+ [Example Policy for Metadata Operations on Data Catalogs](#datacatalog-policy-metadata-operations)

**Example Example Policy for Full Access to All Data Catalogs**  
The following policy allows full access to all data catalog resources that might exist in the account\. We recommend that you use this policy for those users in your account that must administer and manage data catalogs for all other users\.  

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "athena:*"
         ],
         "Resource":[
            "*"
         ]
      }
   ]
}
```

**Example Example Policy for Full Access to a Specified Data Catalog**  
The following policy allows full access to the single specific data catalog resource, named `datacatalogA`\. You could use this policy for users with full control over a particular data catalog\.  

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "athena:ListDataCatalogs",
            "athena:ListWorkGroups",
            "athena:GetExecutionEngine",
            "athena:GetExecutionEngines",
            "athena:GetNamespace",
            "athena:GetCatalogs",
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
            "athena:ListNamedQueries",
            "athena:CreateNamedQuery",
            "athena:GetQueryExecution",
            "athena:BatchGetNamedQuery",
            "athena:BatchGetQueryExecution",
            "athena:DeleteWorkGroup",
            "athena:UpdateWorkGroup",
            "athena:GetWorkGroup",
            "athena:CreateWorkGroup"
         ],
         "Resource":[
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
            "athena:UpdateDataCatalog"
        ],
        "Resource":"arn:aws:athena:us-east-1:123456789012:datacatalog/datacatalogA"
      }
   ]
}
```

**Example Example Policy for Querying a Specified Data Catalog**  
In the following policy, a user is allowed to run queries on the specified `datacatalogA`\. The user is not allowed to perform management tasks for the data catalog itself, such as updating or deleting it\.   

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "athena:StartQueryExecution"
         ],
         "Resource":[
            "arn:aws:athena:us-east-1:123456789012:workgroup/*"
         ]
      },
      {
         "Effect":"Allow",
         "Action":[
            "athena:GetDataCatalog"
         ],
         "Resource":[
            "arn:aws:athena:us-east-1:123456789012:datacatalog/datacatalogA"
         ]
      }
   ]
}
```

**Example Example Policy for Management Operations on a Specified Data Catalog**  
In the following policy, a user is allowed to create, delete, obtain details, and update a data catalog `datacatalogA`\.   

```
{
   "Effect":"Allow",
   "Action":[
      "athena:CreateDataCatalog",
      "athena:GetDataCatalog",
      "athena:DeleteDataCatalog",
      "athena:UpdateDataCatalog"
   ],
   "Resource":[
      "arn:aws:athena:us-east-1:123456789012:datacalog/datacatalogA"
   ]
}
```

**Example Example Policy for Listing Data Catalogs**  
The following policy allows all users to list all data catalogs:  

```
{
   "Effect":"Allow",
   "Action":[
      "athena:ListDataCatalogs"
   ],
   "Resource":"*"
}
```

**Example Example Policy for Metadata Operations on Data Catalogs**  
The following policy allows metadata operations on data catalogs:  

```
{
   "Effect":"Allow",
   "Action":[
      "athena:GetDatabase",
      "athena:GetTableMetadata",
      "athena:ListDatabases",
      "athena:ListTableMetadata"
   ],
   "Resource":"*"
}
```
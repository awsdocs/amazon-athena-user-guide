# Allow Access to Prepared Statements<a name="security-iam-athena-prepared-statements"></a>

This topic covers IAM permissions for prepared statements in Amazon Athena\. Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

For more information about prepared statements, see [Querying with Prepared Statements](querying-with-prepared-statements.md)\.

The following IAM permissions are required for creating, managing, and executing prepared statements\.

```
athena:CreatePreparedStatement
athena:UpdatePreparedStatement
athena:GetPreparedStatement
athena:ListPreparedStatements
athena:DeletePreparedStatement
```

Use these permissions as shown in the following table\.


****  

| To do this | use these permissions | 
| --- | --- | 
| Run a PREPARE query | athena:StartQueryExecution athena:CreatePreparedStatement | 
| Re\-run a PREPARE query to update an existing prepared statement | athena:StartQueryExecution athena:UpdatePreparedStatement | 
| Run an EXECUTE query | athena:StartQueryExecution athena:GetPreparedStatement | 
| Run a DEALLOCATE PREPARE query | athena:StartQueryExecution athena:DeletePreparedStatement | 

## Example<a name="security-iam-athena-prepared-statements-example"></a>

The following example IAM policy grants permissions to manage and run prepared statements on a specified account ID and workgroup\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:StartQueryExecution",
                "athena:CreatePreparedStatement",
                "athena:UpdatePreparedStatement",
                "athena:GetPreparedStatement",
                "athena:DeletePreparedStatement",
                "athena:ListPreparedStatements"
            ],
            "Resource": [
                "arn:aws:athena:*:111122223333:workgroup/<workgroup-name>"
            ]
        }
    ]
}
```
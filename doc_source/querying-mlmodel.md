# Using Machine Learning \(ML\) with Amazon Athena \(Preview\)<a name="querying-mlmodel"></a>

Machine Learning \(ML\) with Amazon Athena \(Preview\) lets you use Athena to write SQL statements that run Machine Learning \(ML\) inference using Amazon SageMaker\. This feature simplifies access to ML models for data analysis, eliminating the need to use complex programming methods to run inference\.

To use ML with Athena \(Preview\), you define an ML with Athena \(Preview\) function with the `USING FUNCTION` clause\. The function points to the Amazon SageMaker model endpoint that you want to use and specifies the variable names and data types to pass to the model\. Subsequent clauses in the query reference the function to pass values to the model\. The model runs inference based on the values that the query passes and then returns inference results\. For more information about Amazon SageMaker and how Amazon SageMaker endpoints work, see the [Amazon SageMaker Developer Guide](https://docs.aws.amazon.com/sagemaker/latest/dg/)\.

## Considerations and Limitations<a name="considerations-and-limitations"></a>
+ **Available Regions** – The Athena ML feature is available in preview in the US East \(N\. Virginia\), Asia Pacific \(Mumbai\), Europe \(Ireland\), and US West \(Oregon\) Regions\. 
+ **AmazonAthenaPreviewFunctionality workgroup** – To use this feature in preview, you must create an Athena workgroup named `AmazonAthenaPreviewFunctionality` and join that workgroup\. For more information, see [Managing Workgroups](workgroups-create-update-delete.md)\.
+ **Amazon SageMaker model endpoint must accept and return `text/csv`** – For more information about data formats, see [Common Data Formats for Inference](https://docs.aws.amazon.com/sagemaker/latest/dg/cdf-inference.html) in the *Amazon SageMaker Developer Guide*\.
+ **Amazon SageMaker endpoint scaling** – Make sure that the referenced Amazon SageMaker model endpoint is sufficiently scaled up for Athena calls to the endpoint\. For more information, see [Automaticaly Scale Amazon SageMaker Models](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling.html) in the *Amazon SageMaker Developer Guide* and [CreateEndpointConfig](https://docs.aws.amazon.com/sagemaker/latest/dg/API_CreateEndpointConfig.html) in the *Amazon SageMaker API Reference*\.
+ **IAM permissions** – To run a query that specifies an ML with Athena \(Preview\) function, the IAM principal running the query must be allowed to perform the `sagemaker:InvokeEndpoint` action for the referenced Amazon SageMaker model endpoint\. For more information, see [Allowing Access for ML with Athena \(Preview\)](machine-learning-iam-access.md)\.
+ **ML with Athena \(Preview\) functions cannot be used in `GROUP BY` clauses directly**

## ML with Athena \(Preview\) Syntax<a name="ml-syntax"></a>

The `USING FUNCTION` clause specifies an ML with Athena \(Preview\) function or multiple functions that can be referenced by a subsequent `SELECT` statement in the query\. You define the function name, variable names, and data types for the variables and return values\.

### Synopsis<a name="ml-synopsis"></a>

The following example illustrates a `USING FUNCTION` clause that specifies ML with Athena \(Preview\) function\.

```
USING FUNCTION ML_function_name(variable1 data_type[, variable2 data_type][,...]) RETURNS data_type TYPE SAGEMAKER_INVOKE_ENDPOINT WITH (sagemaker_endpoint= 'my_sagemaker_endpoint')[, FUNCTION...][, ...] SELECT  [...] ML_function_name(expression) [...]
```

### Parameters<a name="udf-parameters"></a>

**USING FUNCTION *ML\_function\_name*\(*variable1 data\_type*\[, *variable2 data\_type*\]\[,\.\.\.\]\)**  
*ML\_function\_name* defines the function name, which can be used in subsequent query clauses\. Each *variable data\_type* specifies a named variable with its corresponding data type, which the Amazon SageMaker model can accept as input\. Specify *data\_type* as one of the supported Athena data types that the Amazon SageMaker model can accept as input\.

**RETURNS data\_type TYPE**  
`data_type` specifies the SQL data type that `ML_function_name` returns to the query as output from the Amazon SageMaker model\.

**SAGEMAKER\_INVOKE\_ENDPOINT WITH \(sagemaker\_endpoint= '*my\_sagemaker\_endpoint*'\)**  
*my\_sagemaker\_endpoint* specifies the endpoint of the Amazon SageMaker model\.

**SELECT \[\.\.\.\] ML\_function\_name\(expression\) \[\.\.\.\]**  
The SELECT query that passes values to function variables and the Amazon SageMaker model to return a result\. `ML_function_name` specifies the function defined earlier in the query, followed by an `expression` that is evaluated to pass values\. Values that are passed and returned must match the corresponding data types specified for the function in the `USING FUNCTION` clause\.

### Examples<a name="ml-examples"></a>

The following example demonstrates a query using ML with Athena \(Preview\)\.

**Example**  

```
USING FUNCTION predict_customer_registration(age INTEGER) 
    RETURNS DOUBLE TYPE 
    SAGEMAKER_INVOKE_ENDPOINT WITH (sagemaker_endpoint = 'xgboost-2019-09-20-04-49-29-303') 
SELECT predict_customer_registration(age) AS probability_of_enrolling, customer_id 
     FROM "sampledb"."ml_test_dataset" 
     WHERE predict_customer_registration(age) < 0.5;
```
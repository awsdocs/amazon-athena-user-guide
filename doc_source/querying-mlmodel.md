# Using Machine Learning \(ML\) with Amazon Athena<a name="querying-mlmodel"></a>

Machine Learning \(ML\) with Amazon Athena lets you use Athena to write SQL statements that run Machine Learning \(ML\) inference using Amazon SageMaker\. This feature simplifies access to ML models for data analysis, eliminating the need to use complex programming methods to run inference\.

To use ML with Athena, you define an ML with Athena function with the `USING EXTERNAL FUNCTION` clause\. The function points to the SageMaker model endpoint that you want to use and specifies the variable names and data types to pass to the model\. Subsequent clauses in the query reference the function to pass values to the model\. The model runs inference based on the values that the query passes and then returns inference results\. For more information about SageMaker and how SageMaker endpoints work, see the [Amazon SageMaker Developer Guide](https://docs.aws.amazon.com/sagemaker/latest/dg/)\.

For an example that uses ML with Athena and SageMaker inference to detect an anomalous value in a result set, see the AWS Big Data Blog article [Detecting anomalous values by invoking the Amazon Athena machine learning inference function](http://aws.amazon.com/blogs/big-data/detecting-anomalous-values-by-invoking-the-amazon-athena-machine-learning-inference-function/)\.

## Considerations and limitations<a name="considerations-and-limitations"></a>
+ **Available Regions** – The Athena ML feature is feature in the AWS Regions where Athena engine version 2 or later are supported\.
+ **SageMaker model endpoint must accept and return `text/csv`** – For more information about data formats, see [Common data formats for inference](https://docs.aws.amazon.com/sagemaker/latest/dg/cdf-inference.html) in the *Amazon SageMaker Developer Guide*\.
+ **SageMaker endpoint scaling** – Make sure that the referenced SageMaker model endpoint is sufficiently scaled up for Athena calls to the endpoint\. For more information, see [Automatically scale SageMaker models](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling.html) in the *Amazon SageMaker Developer Guide* and [CreateEndpointConfig](https://docs.aws.amazon.com/sagemaker/latest/dg/API_CreateEndpointConfig.html) in the *Amazon SageMaker API Reference*\.
+ **IAM permissions** – To run a query that specifies an ML with Athena function, the IAM principal running the query must be allowed to perform the `sagemaker:InvokeEndpoint` action for the referenced SageMaker model endpoint\. For more information, see [Allowing access for ML with Athena](machine-learning-iam-access.md)\.
+ **ML with Athena functions cannot be used in `GROUP BY` clauses directly**

## ML with Athena syntax<a name="ml-syntax"></a>

The `USING EXTERNAL FUNCTION` clause specifies an ML with Athena function or multiple functions that can be referenced by a subsequent `SELECT` statement in the query\. You define the function name, variable names, and data types for the variables and return values\.

### Synopsis<a name="ml-synopsis"></a>

The following syntax shows a `USING EXTERNAL FUNCTION` clause that specifies an ML with Athena function\.

```
USING EXTERNAL FUNCTION ml_function_name (variable1 data_type[, variable2 data_type][,...])
RETURNS data_type 
SAGEMAKER 'sagemaker_endpoint'
SELECT ml_function_name()
```

### Parameters<a name="udf-parameters"></a>

**USING EXTERNAL FUNCTION *ml\_function\_name* \(*variable1* *data\_type*\[, *variable2* *data\_type*\]\[,\.\.\.\]\)**  
*ml\_function\_name* defines the function name, which can be used in subsequent query clauses\. Each *variable data\_type* specifies a named variable and its corresponding data type that the SageMaker model accepts as input\. The data type specified must be a supported Athena data type\.

**RETURNS *data\_type***  
*data\_type* specifies the SQL data type that *ml\_function\_name* returns to the query as output from the SageMaker model\.

**SAGEMAKER '*sagemaker\_endpoint*'**  
*sagemaker\_endpoint* specifies the endpoint of the SageMaker model\.

**SELECT \[\.\.\.\] *ml\_function\_name*\(*expression*\) \[\.\.\.\]**  
The SELECT query that passes values to function variables and the SageMaker model to return a result\. *ml\_function\_name* specifies the function defined earlier in the query, followed by an *expression* that is evaluated to pass values\. Values that are passed and returned must match the corresponding data types specified for the function in the `USING EXTERNAL FUNCTION` clause\.

### Example<a name="ml-examples"></a>

The following example demonstrates a query using ML with Athena\.

**Example**  

```
USING EXTERNAL FUNCTION predict_customer_registration(age INTEGER) 
    RETURNS DOUBLE
    SAGEMAKER 'xgboost-2019-09-20-04-49-29-303' 
SELECT predict_customer_registration(age) AS probability_of_enrolling, customer_id 
     FROM "sampledb"."ml_test_dataset" 
     WHERE predict_customer_registration(age) < 0.5;
```

## Customer use examples<a name="ml-videos"></a>

The following videos, which use the Preview version of Machine Learning \(ML\) with Amazon Athena, showcase ways in which you can use SageMaker with Athena\.

### Predicting customer churn<a name="ml-videos-predict-churn"></a>

The following video shows how to combine Athena with the machine learning capabilities of Amazon SageMaker to predict customer churn\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/CUHbSpekRVg/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/CUHbSpekRVg)

### Detecting botnets<a name="ml-videos-detect-botnets"></a>

The following video shows how one company uses Amazon Athena and Amazon SageMaker to detect botnets\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/0dUv-jCt2aw/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/0dUv-jCt2aw)
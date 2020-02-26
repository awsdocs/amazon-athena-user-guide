# Querying with User Defined Functions \(Preview\)<a name="querying-udf"></a>

User Defined Functions \(UDF\) in Amazon Athena allow you to create custom functions to process records or groups of records\. A UDF accepts parameters, performs work, and then returns a result\.

To use a UDF in Athena, you write a `USING FUNCTION` clause before a `SELECT` statement in a SQL query\. The `SELECT` statement references the UDF and defines the variables that are passed to the UDF when the query runs\. The SQL query invokes a Lambda function using the Java runtime when it calls the UDF\. UDFs are defined within the Lambda function as methods in a Java deployment package\. Multiple UDFs can be defined in the same Java deployment package for a Lambda function\. You also specify the name of the Lambda function in the `USING FUNCTION` clause\.

You have two options for deploying a Lambda function for Athena UDFs\. You can deploy the function directly using Lambda, or you can use the AWS Serverless Application Repository\. To find existing Lambda functions for UDFs, you can search the public AWS Serverless Application Repository or your private repository and then deploy to Lambda\. You can also create or modify Java source code, package it into a JAR file, and deploy it using Lambda or the AWS Serverless Application Repository\. We provide example Java source code and packages to get you started\. For more information about Lambda, see [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\. For more information about AWS Serverless Application Repository, see the [AWS Serverless Application Repository Developer Guide](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/)\.

## Considerations and Limitations<a name="udf-considerations-limitations"></a>
+ **AmazonAthenaPreviewFunctionality workgroup** – To use this feature in preview, you must create an Athena workgroup named `AmazonAthenaPreviewFunctionality` and join that workgroup\. For more information, see [Managing Workgroups](workgroups-create-update-delete.md)\.
+ **Built\-in Athena functions** – Built\-in Presto functions in Athena are designed to be highly performant\. We recommend that you use built\-in functions over UDFs when possible\. For more information about built\-in functions, see [Presto Functions in Amazon Athena](presto-functions.md)\.
+ **Scalar UDFs only** – Athena only supports scalar UDFs, which process one row at a time and return a single column value\. Athena passes a batch of rows, potentially in parallel, to the UDF each time it invokes Lambda\. When designing UDFs and queries, be mindful of the potential impact to network traffic that this processing design can have\.
+ **Java runtime only** – Currently, Athena UDFs support only the Java 8 runtime for Lambda\.
+ **IAM permissions** – To run a query in Athena that contains a UDF query statement and to create UDF statements, the IAM principal running the query must be allowed to perform actions in addition to Athena functions\. For more information, see [Example IAM Permissions Policies to Allow Amazon Athena User Defined Functions \(UDF\)](udf-iam-access.md)\.
+ **Lambda quotas** – Lambda quotas apply to UDFs\. For more information, see [AWS Lambda Quotas](https://docs.aws.amazon.com/lambda/latest/dg/limits.html) in the *AWS Lambda Developer Guide*\.
+ **Known issues** – For the most up\-to\-date list of known issues, see [Limitations and Issues](https://github.com/awslabs/aws-athena-query-federation/wiki/Limitations_And_Issues) in the Athena Federated Query \(Preview\) 

## UDF Query Syntax<a name="udf-query-syntax"></a>

The `USING FUNCTION` clause specifies a UDF or multiple UDFs that can be referenced by a subsequent `SELECT` statement in the query\. You need the method name for the UDF and the name of the Lambda function that hosts the UDF\.

### Synopsis<a name="udf-synopsis"></a>

```
USING FUNCTION UDF_name(variable1 data_type[, variable2 data_type][,...]) RETURNS data_type TYPE 
     LAMBDA_INVOKE WITH (lambda_name = 'my_lambda_function')[, FUNCTION][, ...] SELECT  [...] UDF_name(expression) [...]
```

### Parameters<a name="udf-parameters"></a>

**USING FUNCTION UDF\_name\(variable1 data\_type\[, variable2 data\_type\]\[,\.\.\.\]\)**  
*UDF\_name* specifies the name of the UDF, which must correspond to a Java method within the referenced Lambda function\. Each *variable data\_type* specifies a named variable with its corresponding data type, which the UDF can accept as input\. Specify *data\_type* as one of the supported Athena data types listed in the following table\. The data type must map to the corresponding Java data type\.      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/athena/latest/ug/querying-udf.html)

**RETURNS data\_type TYPE**  
`data_type` specifies the SQL data type that the UDF returns as output\. Athena data types listed in the table above are supported\.

**LAMBDA\_INVOKE WITH \(lambda\_name = '*my\_lambda\_function*'\)**  
*my\_lambda\_function* specifies the name of the Lambda function to be invoked when running the UDF\.

**SELECT \[\.\.\.\] UDF\_name\(expression\) \[\.\.\.\]**  
The SELECT query that passes values to the UDF and returns a result\. `UDF_name` specifies the UDF to use, followed by an `expression` that is evaluated to pass values\. Values that are passed and returned must match the corresponding data types specified for the UDF in the `USING FUNCTION` clause\.

### Examples<a name="udf-examples"></a>

The following examples demonstrate queries using UDFs\. The Athena query examples are based on the [AthenaUDFHandler\.java](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-udfs/src/main/java/com/amazonaws/athena/connectors/udfs/AthenaUDFHandler.java) code in GitHub\.

**Example – Compress and Decompress a String**  
**Athena SQL**  
The following example demonstrates using the `compress` UDF defined in a Lambda function named `MyAthenaUDFLambda`\.  

```
USING FUNCTION compress(col1 VARCHAR) 
     RETURNS VARCHAR TYPE 
     LAMBDA_INVOKE WITH (lambda_name = 'MyAthenaUDFLambda') 
SELECT 
     compress('StringToBeCompressed');
```
The query result returns `ewLLinKzEsPyXdKdc7PLShKLS5OTQEAUrEH9w==`\.

The following example demonstrates using the `decompress` UDF defined in the same Lambda function\.

```
USING FUNCTION decompress(col1 VARCHAR) 
     RETURNS VARCHAR TYPE 
     LAMBDA_INVOKE WITH (lambda_name = 'MyAthenaUDFLambda') 
SELECT 
     decompress('ewLLinKzEsPyXdKdc7PLShKLS5OTQEAUrEH9w==');
```

The query result returns `StringToBeCompressed`\.

## Creating and Deploying a UDF Using Lambda<a name="udf-creating-and-deploying"></a>

To create a custom UDF, you create a new Java class by extending the `UserDefinedFunctionHandler` class\. The source code for the [UserDefinedFunctionHandler\.java](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-federation-sdk/src/main/java/com/amazonaws/athena/connector/lambda/handlers/UserDefinedFunctionHandler.java) in the SDK is available on GitHub in the awslabs/aws\-athena\-query\-federation/athena\-federation\-sdk [repository](https://github.com/awslabs/aws-athena-query-federation/athena-federation-sdk), along with [example UDF implementations](https://github.com/awslabs/aws-athena-query-federation/tree/master/athena-udfs) that you can examine and modify to create a custom UDF\.

The steps in this section demonstrate writing and building a custom UDF Jar file using [Apache Maven](https://maven.apache.org/index.html) from the command line and a deploy\.

**Topics**
+ [Clone the SDK and Prepare Your Development Environment](#udf-create-install-sdk-prep-environment)
+ [Create your Maven Project](#create-maven-project)
+ [Add Dependencies and Plugins to Your Maven Project](#udf-add-maven-dependencies)
+ [Write Java Code for the UDFs](#udf-write-java)
+ [Build the JAR File](#udf-create-package-jar)
+ [Deploy the JAR to AWS Lambda](#udf-create-deploy)

### Clone the SDK and Prepare Your Development Environment<a name="udf-create-install-sdk-prep-environment"></a>

Before you begin, make sure that git is installed on your system using `sudo yum install git -y`\.

**To install the AWS Query Federation SDK**
+ Enter the following at the command line to clone the SDK repository\. This repository includes the SDK, examples and a suite of data source connectors\. For more information about data source connectors, see [Using Amazon Athena Federated Query \(Preview\)](connect-to-a-data-source.md)\.

  ```
  git clone https://github.com/awslabs/aws-athena-query-federation.git
  ```

**To install prerequisites for this procedure**

If you are working on a development machine that already has Apache Maven, the AWS CLI, and the AWS Serverless Application Model build tool installed, you can skip this step\.

1. From the root of the `aws-athena-query-federation` directory that you created when you cloned, run the [prepare\_dev\_env\.sh](https://github.com/awslabs/aws-athena-query-federation/blob/master/tools/prepare_dev_env.sh) script that prepares your development environment\.

1. Update your shell to source new variables created by the installation process or restart your terminal session\.

   ```
   source ~/.profile
   ```
**Important**  
If you skip this step, you will get errors later about the AWS CLI or AWS SAM build tool not being able to publish your Lambda function\.

### Create your Maven Project<a name="create-maven-project"></a>

Run the following command to create your Maven project\. Replace *groupId* with the unique ID of your organization, and replace *my\-athena\-udf* with the name of your application For more information, see [How do I make my first Maven project?](https://maven.apache.org/guides/getting-started/index.html#How_do_I_make_my_first_Maven_project) in Apache Maven documentation\.

```
mvn -B archetype:generate \
-DarchetypeGroupId=org.apache.maven.archetypes \
-DgroupId=groupId \
-DartifactId=my-athena-udfs
```

### Add Dependencies and Plugins to Your Maven Project<a name="udf-add-maven-dependencies"></a>

Add the following configurations to your Maven project `pom.xml` file\. For an example, see the [pom\.xml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-udfs/pom.xml) file in GitHub\.

```
<properties>
    <aws-athena-federation-sdk.version>2019.48.1</aws-athena-federation-sdk.version>
</properties>

<dependencies>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-athena-federation-sdk</artifactId>
        <version>${aws-athena-federation-sdk.version}</version>
    </dependency>
</dependencies>
    
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <createDependencyReducedPom>false</createDependencyReducedPom>
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

### Write Java Code for the UDFs<a name="udf-write-java"></a>

Create a new class by extending [UserDefinedFunctionHandler\.java](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-federation-sdk/src/main/java/com/amazonaws/athena/connector/lambda/handlers/UserDefinedFunctionHandler.java)\. Write your UDFs inside the class\.

In the following example, two Java methods for UDFs, `compress()` and `decompress()`, are created inside the class `MyUserDefinedFunctions`\.

```
*package *com.mycompany.athena.udfs;

public class MyUserDefinedFunctions
        extends UserDefinedFunctionHandler
{
    private static final String SOURCE_TYPE = "MyCompany";

    public MyUserDefinedFunctions()
    {
        super(SOURCE_TYPE);
    }

    /**
     * Compresses a valid UTF-8 String using the zlib compression library.
     * Encodes bytes with Base64 encoding scheme.
     *
     * @param input the String to be compressed
     * @return the compressed String
     */
    public String compress(String input)
    {
        byte[] inputBytes = input.getBytes(StandardCharsets.UTF_8);

        // create compressor
        Deflater compressor = new Deflater();
        compressor.setInput(inputBytes);
        compressor.finish();

        // compress bytes to output stream
        byte[] buffer = new byte[4096];
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream(inputBytes.length);
        while (!compressor.finished()) {
            int bytes = compressor.deflate(buffer);
            byteArrayOutputStream.write(buffer, 0, bytes);
        }

        try {
            byteArrayOutputStream.close();
        }
        catch (IOException e) {
            throw new RuntimeException("Failed to close ByteArrayOutputStream", e);
        }

        // return encoded string
        byte[] compressedBytes = byteArrayOutputStream.toByteArray();
        return Base64.getEncoder().encodeToString(compressedBytes);
    }

    /**
     * Decompresses a valid String that has been compressed using the zlib compression library.
     * Decodes bytes with Base64 decoding scheme.
     *
     * @param input the String to be decompressed
     * @return the decompressed String
     */
    public String decompress(String input)
    {
        byte[] inputBytes = Base64.getDecoder().decode((input));

        // create decompressor
        Inflater decompressor = new Inflater();
        decompressor.setInput(inputBytes, 0, inputBytes.length);

        // decompress bytes to output stream
        byte[] buffer = new byte[4096];
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream(inputBytes.length);
        try {
            while (!decompressor.finished()) {
                int bytes = decompressor.inflate(buffer);
                if (bytes == 0 && decompressor.needsInput()) {
                    throw new DataFormatException("Input is truncated");
                }
                byteArrayOutputStream.write(buffer, 0, bytes);
            }
        }
        catch (DataFormatException e) {
            throw new RuntimeException("Failed to decompress string", e);
        }

        try {
            byteArrayOutputStream.close();
        }
        catch (IOException e) {
            throw new RuntimeException("Failed to close ByteArrayOutputStream", e);
        }

        // return decoded string
        byte[] decompressedBytes = byteArrayOutputStream.toByteArray();
        return new String(decompressedBytes, StandardCharsets.UTF_8);
    }
}
```

### Build the JAR File<a name="udf-create-package-jar"></a>

Run `mvn clean install` to build your project\. After it successfully builds, a JAR file is created in the `target` folder of your project named `artifactId-version.jar`, where *artifactId* is the name you provided in the Maven project, for example, `my-athena-udfs`\.

### Deploy the JAR to AWS Lambda<a name="udf-create-deploy"></a>

You have two options to deploy your code to Lambda:
+ Deploy Using AWS Serverless Application Repository \(Recommended\)
+ Create a Lambda Function from the JAR file

#### Option 1: Deploying to the AWS Serverless Application Repository<a name="udf-create-deploy-sar"></a>

When you deploy your JAR file to the AWS Serverless Application Repository, you create an AWS SAM template YAML file that represents the architecture of your application\. You then specify this YAML file and an Amazon S3 bucket where artifacts for your application are uploaded and made available to the AWS Serverless Application Repository\. The procedure below uses the [publish\.sh](https://github.com/awslabs/aws-athena-query-federation/blob/master/tools/publish.sh) script located in the `athena-query-federation/tools` directory of the Athena Query Federation SDK that you cloned earlier\.

For more information and requirements, see [Publishing Applications](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/serverlessrepo-publishing-applications.html) in the *AWS Serverless Application Repository Developer Guide*, [AWS SAM Template Concepts](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-template-basics.html) in the *AWS Serverless Application Model Developer Guide*, and [Publishing Serverless Applications Using the AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-template-publishing-applications.html)\.

The following example demonstrates parameters in a YAML file\. Add similar parameters to your YAML file and save it in your project directory\. See [athena\-udf\.yaml](https://github.com/awslabs/aws-athena-query-federation/blob/master/athena-udfs/athena-udfs.yaml) in GitHub for a full example\.

```
Transform: 'AWS::Serverless-2016-10-31'
Metadata:
  'AWS::ServerlessRepo::Application':
    Name: MyApplicationName
    Description: 'The description I write for my application'
    Author: 'Author Name'
    Labels:
      - athena-federation
    SemanticVersion: 1.0.0
Parameters:
  LambdaFunctionName:
    Description: 'The name of the Lambda function that will contain your UDFs.'
    Type: String
  LambdaTimeout:
    Description: 'Maximum Lambda invocation runtime in seconds. (min 1 - 900 max)'
    Default: 900
    Type: Number
  LambdaMemory:
    Description: 'Lambda memory in MB (min 128 - 3008 max).'
    Default: 3008
    Type: Number
Resources:
  ConnectorConfig:
    Type: 'AWS::Serverless::Function'
    Properties:
      FunctionName: !Ref LambdaFunctionName
      Handler: "full.path.to.your.handler. For example, com.amazonaws.athena.connectors.udfs.MyUDFHandler"
      CodeUri: "Relative path to your JAR file. For example, ./target/athena-udfs-1.0.jar"
      Description: "My description of the UDFs that this Lambda function enables."
      Runtime: java8
      Timeout: !Ref LambdaTimeout
      MemorySize: !Ref LambdaMemory
```

Copy the `publish.sh` script to the project directory where you saved your YAML file, and run the following command:

```
./publish.sh MyS3Location MyYamlFile
```

For example, if your bucket location is `s3://mybucket/mysarapps/athenaudf` and your YAML file was saved as `my-athena-udfs.yaml`:

```
./publish.sh mybucket/mysarapps/athenaudf my-athena-udfs
```

**To create a Lambda function**

1. Open the Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/), choose **Create function**, and then choose **Browse serverless app repository**

1. Choose **Private applications**, find your application in the list, or search for it using key words, and select it\.

1. Review and provide application details, and then choose **Deploy\.**

   You can now use the method names defined in your Lambda function JAR file as UDFs in Athena\.

#### Option 2: Creating a Lambda Function Directly<a name="udf-create-deploy-lambda"></a>

You can also create a Lambda function directly using the console or AWS CLI\. The following example demonstrates using the Lambda `create-function` CLI command\. 

```
aws lambda create-function \
 --function-name MyLambdaFunctionName \
 --runtime java8 \
 --role arn:aws:iam::1234567890123:role/my_lambda_role \
 --handler com.mycompany.athena.udfs.MyUserDefinedFunctions \
 --timeout 900 \
 --zip-file fileb://./target/my-athena-udfs-1.0-SNAPSHOT.jar
```
# Connecting Athena to a Hive Metastore Using an Existing IAM Execution Role<a name="connect-data-source-hive-existing-iam-role"></a>

To connect your external Hive metastore to Athena with a Lambda function that uses an existing IAM role, you can use Athena's reference implementation of the Athena connector for external Hive metastore\.

The three major steps are as follows:

1. **[Clone and Build](#connect-data-source-hive-existing-iam-role-clone-and-build-the-lambda-function)** – Clone the Athena reference implementation and build the JAR file that contains the Lambda function code\.

1. **[AWS Lambda console](#connect-data-source-hive-existing-iam-role-aws-lambda-console)** – In the AWS Lambda console, create a Lambda function, assign it an existing IAM execution role, and upload the function code that you generated\.

1. **[Amazon Athena console](#connect-data-source-hive-existing-iam-role-connect-athena)** – In the Amazon Athena console, create a data catalog name that you can use to refer to your external Hive metastore in your Athena queries\.

If you already have permissions to create a custom IAM role, you can use a simpler workflow that uses the Athena console and the AWS Serverless Application Repository to create and configure a Lambda function\. For more information, see [Connecting Athena to an Apache Hive Metastore](connect-to-data-source-hive-connecting-athena-to-an-apache-hive-metastore.md)\.

## Prerequisites<a name="connect-data-source-hive-existing-iam-role-prerequisites"></a>
+ Git must be installed on your system\.
+ You must have [Apache Maven](https://maven.apache.org/) installed\.
+ You have an IAM execution role that you can assign to the Lambda function\. For more information, see [Allow Lambda Function Access to External Hive Metastores](hive-metastore-iam-access-lambda.md)\.

## Clone and Build the Lambda function<a name="connect-data-source-hive-existing-iam-role-clone-and-build-the-lambda-function"></a>

The function code for the Athena reference implementation is a Maven project located on GitHub at [awslabs/aws\-athena\-hive\-metastore](https://github.com/awslabs/aws-athena-hive-metastore)\. For detailed information about the project, see the corresponding README file on GitHub or the [Reference Implementation](datastores-hive-reference-implementation.md) topic in this documentation\.

**To clone and build the Lambda function code**

1. Enter the following command to clone the Athena reference implementation:

   ```
   git clone https://github.com/awslabs/aws-athena-hive-metastore
   ```

1. Run the following command to build the `.jar` file for the Lambda function:

   ```
   mvn clean install
   ```

   After the project builds successfully, the following `.jar` file is created in the target folder of your project:

   `hms-lambda-func-1.0-SNAPSHOT-withdep.jar`

   In the next section, you use the AWS Lambda console to upload this file to your AWS account\.

## Create and Configure the Lambda Function in the AWS Lambda Console<a name="connect-data-source-hive-existing-iam-role-aws-lambda-console"></a>

In this section, you use the AWS Lambda console to create a function that uses an existing IAM execution role\. After you configure a VPC for the function, you upload the function code and configure the environment variables for the function\.

### Create the Lambda Function<a name="connect-data-source-hive-existing-iam-role-create-the-lambda-function"></a>

In this step, you create a function in the AWS Lambda console that uses an existing IAM role\.

**To create a Lambda function that uses an existing IAM role**

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. In the navigation pane, choose **Functions**\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. For **Function name**, enter the name of your Lambda function \(for example, **EHMSBasedLambda**\)\.

1. For **Runtime**, choose **Java 8**\.  
![\[Creating a function in the Lambda console.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-1.png)

1. Under **Permissions**, expand **Change default execution role**\.

1. For **Execution role**, choose **Use an existing role**\.

1. For **Existing role**, choose the IAM execution role that your Lambda function will use for Athena \(this example uses a role called `AthenaLambdaExecutionRole`\)\.  
![\[Choosing an existing IAM execution role for the Lambda function.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-2.png)

1. Expand **Advanced settings**\.

1. For **VPC**, choose the VPC that your function will have access to\.

1. For **Subnets**, choose the VPC subnets for Lambda to use\.

1. For **Security groups**, choose the VPC security groups for Lambda to use\.

1. Choose **Create function**\. The AWS Lambda console and opens the configuration page for your function and begins creating your function\.  
![\[Specifying the VPC details for the Lambda function.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-3.png)

### Upload the Code and Configure the Lambda function<a name="connect-data-source-hive-existing-iam-role-upload-and-configure"></a>

When the console informs you that your function has been successfully created, you are ready to upload the function code and configure its environment variables\.

**To upload your Lambda function code and configure its environment variables**

1. In the Lambda console, navigate to the page for your function if necessary\.

1. For **Function code**, choose **Actions**, and then choose **Upload a \.zip or \.jar file**\.  
![\[Uploading the function code for the Lambda function.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-4.png)

1. Upload the `hms-lambda-func-1.0-SNAPSHOT-withdep.jar` file that you generated previously\.

1. In the **Environment variables** section of the configuration page for your function, choose **Edit**\.  
![\[Choose Edit to edit the environment variables for the Lambda function.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-5.png)

1. On the **Edit environment variables** page, add the following environment variable keys and values:
   + **HMS\_URIS** – Use the following syntax to enter the URI of your Hive metastore host that uses the Thrift protocol at port 9083\.

     ```
     thrift://<host_name>:9083.
     ```
   + **SPILL\_LOCATION** – Specify an Amazon S3 location in your AWS account to hold spillover metadata if the Lambda function response size exceeds 4MB\.  
![\[Specifying values for the Lambda function environment variables.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-6.png)

1. Choose **Save**\.

## Connect Athena to Your Hive Metastore<a name="connect-data-source-hive-existing-iam-role-connect-athena"></a>

Now you can use the Athena console to prepare Athena to use your Hive metastore\. In this step, you create a data catalog name to use in your Athena queries that refers to your external Hive metastore\.

**To connect Athena to your Hive metastore**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Do one of the following:
   + In the Query Editor navigation pane, choose **Connect data source**\.  
![\[Choose Connect data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-7.png)
   + Choose the **Data sources** tab, and then choose **Connect data source**\.  
![\[Choose Data sources, Connect data source.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-8.png)

1. On the **Connect data source** page, for **Choose a metadata catalog**, choose **Apache Hive metastore**\.  
![\[Choose Apache Hive metastore.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-9.png)

1. Choose **Next**\.

1. On the **Connection details** page, for **Lambda function**, use the **Choose Lambda function** option to choose the Lambda function that you created\.  
![\[Choose the Lambda function that you created.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-10.png)

   A new **Lambda function ARN** entry shows the ARN of your Lambda function\.  
![\[Create a unique data catalog name to use in your SQL queries in Athena.\]](http://docs.aws.amazon.com/athena/latest/ug/images/connect-data-source-hive-existing-iam-role-11.png)

1. For **Catalog name**, enter a unique name that you will use in your SQL queries to reference your Hive data source\.
**Note**  
The names `awsdatacatalog`, `hive`, `jmx`, and `system` are reserved by Athena and cannot be used for custom catalog names\.

1. Choose **Connect**\. This connects Athena to your Hive metastore catalog\.

1. You can now use the **Catalog name** that you specified to reference the Hive metastore in your SQL queries\. In your SQL queries, use the following example syntax, replacing `ehms-catalog` with the catalog name that you specified earlier\.

   ```
   SELECT * FROM ehms-catalog.CustomerData.customers; 
   ```

1. To view, edit, or delete the data sources that you create, see [Managing Data Sources](data-sources-managing.md)\.
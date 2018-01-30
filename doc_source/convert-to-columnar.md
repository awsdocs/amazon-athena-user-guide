# Converting to Columnar Formats<a name="convert-to-columnar"></a>

Your Amazon Athena query performance improves if you convert your data into open source columnar formats, such as [Apache Parquet](https://parquet.apache.org) or [ORC](https://orc.apache.org/)\.

You can do this to existing Amazon S3 data sources by creating a cluster in Amazon EMR and converting it using Hive\. The following example using the AWS CLI shows you how to do this with a script and data stored in Amazon S3\.

## Overview<a name="overview"></a>

The process for converting to columnar formats using an EMR cluster is as follows:

1. Create an EMR cluster with Hive installed\.

1. In the step section of the cluster create statement, specify a script stored in Amazon S3, which points to your input data and creates output data in the columnar format in an Amazon S3 location\. In this example, the cluster auto\-terminates\.

   The full script is located on Amazon S3 at:

   ```
   s3://athena-examples/conversion/write-parquet-to-s3.q
   ```

   Here's an example script beginning with the `CREATE TABLE` snippet:

   ```
   ADD JAR /usr/lib/hive-hcatalog/share/hcatalog/hive-hcatalog-core-1.0.0-amzn-5.jar;
   CREATE EXTERNAL TABLE impressions (
     requestBeginTime string,
     adId string,
     impressionId string,
     referrer string,
     userAgent string,
     userCookie string,
     ip string,
     number string,
     processId string,
     browserCookie string,
     requestEndTime string,
     timers struct<modelLookup:string, requestTime:string>,
     threadId string,
     hostname string,
     sessionId string)
   PARTITIONED BY (dt string)
   ROW FORMAT  serde 'org.apache.hive.hcatalog.data.JsonSerDe'
   with serdeproperties ( 'paths'='requestBeginTime, adId, impressionId, referrer, userAgent, userCookie, ip' )
   LOCATION 's3://${REGION}.elasticmapreduce/samples/hive-ads/tables/impressions' ;
   ```
**Note**  
Replace `REGION` in the `LOCATION` clause with the region where you are running queries\. For example, if your console is in us\-east\-1, `REGION` is `s3://us-east-1.elasticmapreduce/samples/hive-ads/tables/`\.

   This creates the table in Hive on the cluster which uses samples located in the Amazon EMR samples bucket\. 

1. On Amazon EMR release 4\.7\.0, include the `ADD JAR` line to find the appropriate JsonSerDe\. The prettified sample data looks like the following:

   ```
   {
       "number": "977680",
       "referrer": "fastcompany.com",
       "processId": "1823",
       "adId": "TRktxshQXAHWo261jAHubijAoNlAqA",
       "browserCookie": "mvlrdwrmef",
       "userCookie": "emFlrLGrm5fA2xLFT5npwbPuG7kf6X",
       "requestEndTime": "1239714001000",
       "impressionId": "1I5G20RmOuG2rt7fFGFgsaWk9Xpkfb",
       "userAgent": "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0; SLCC1; .NET CLR 2.0.50727; Media Center PC 5.0; .NET CLR 3.0.04506; InfoPa",
       "timers": {
           "modelLookup": "0.3292",
           "requestTime": "0.6398"
       },
       "threadId": "99",
       "ip": "67.189.155.225",
       "modelId": "bxxiuxduad",
       "hostname": "ec2-0-51-75-39.amazon.com",
       "sessionId": "J9NOccA3dDMFlixCuSOtl9QBbjs6aS",
       "requestBeginTime": "1239714000000"
   }
   ```

1. In Hive, load the data from the partitions, so the script runs the following:

   ```
   MSCK REPAIR TABLE impressions;
   ```

   The script then creates a table that stores your data in a Parquet\-formatted file on Amazon S3:

   ```
   CREATE EXTERNAL TABLE  parquet_hive (
       requestBeginTime string,
       adId string,
       impressionId string,
       referrer string,
       userAgent string,
       userCookie string,
       ip string
   )   STORED AS PARQUET
   LOCATION 's3://myBucket/myParquet/';
   ```

   The data are inserted from the *impressions* table into *parquet\_hive*:

   ```
   INSERT OVERWRITE TABLE parquet_hive
   SELECT
   requestbegintime,
   adid,
   impressionid,
   referrer,
   useragent,
   usercookie,
   ip FROM impressions WHERE dt='2009-04-14-04-05';
   ```

   The script stores the above *impressions* table columns from the date, 2009\-04\-14\-04\-05, into s3://myBucket/myParquet/ in a Parquet\-formatted file\. 

1. After your EMR cluster is terminated, create your table in Athena, which uses the data in the format produced by the cluster\.

## Before you begin<a name="prerequisites"></a>

+ You need to create EMR clusters\. For more information about Amazon EMR, see the [Amazon EMR Management Guide](http://docs.aws.amazon.com/emr/latest/ManagementGuide/)\.

+ Follow the instructions found in Setting Up\.

## Example: Converting data to Parquet using an EMR cluster<a name="example-converting-data-to-parquet-using-an-emr-cluster"></a>

1. Use the AWS CLI to create a cluster\. If you need to install the AWS CLI, see [Installing the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the AWS Command Line Interface User Guide\.

1. You need roles to use Amazon EMR, so if you haven't used Amazon EMR before, create the default roles using the following command:

   ```
   aws emr create-default-roles
   ```

1. Create an Amazon EMR cluster using the emr\-4\.7\.0 release to convert the data using the following AWS CLI emr create\-cluster command:

   ```
   export REGION=us-east-1
   export SAMPLEURI=s3://${REGION}.elasticmapreduce/samples/hive-ads/tables/impressions/
   export S3BUCKET=myBucketName
   
   aws emr create-cluster --applications Name=Hadoop Name=Hive Name=HCatalog \
   --ec2-attributes KeyName=myKey,InstanceProfile=EMR_EC2_DefaultRole,SubnetId=subnet-mySubnetId \
   --service-role EMR_DefaultRole --release-label emr-4.7.0 --instance-type \
   m4.large --instance-count 1 --steps Type=HIVE,Name="Convert to Parquet",\
   ActionOnFailure=CONTINUE,ActionOnFailure=TERMINATE_CLUSTER,Args=[-f,\
   s3://athena-examples/conversion/write-parquet-to-s3.q,-hiveconf,INPUT=${SAMPLEURI},-hiveconf,OUTPUT=s3://{$S3BUCKET}/myParquet,-hiveconf,REGION=${REGION}] \
   --region ${REGION} --auto-terminate
   ```

   For more information, see [Create and Use IAM Roles for Amazon EMR](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-iam-roles-creatingroles.html) in the Amazon EMR Management Guide\.

   A successful request gives you a cluster ID\.

1.  Monitor the progress of your cluster using the AWS Management Console, or using the cluster ID with the *list\-steps* subcommand in the AWS CLI:

   ```
   aws emr list-steps --cluster-id myClusterID
   ```

   Look for the script step status\. If it is COMPLETED, then the conversion is done and you are ready to query the data\.

1.  Create the same table that you created on the EMR cluster\.

   You can use the same statement as above\. Log into Athena and enter the statement in the **Query Editor** window:

   ```
   CREATE EXTERNAL TABLE  parquet_hive (
       requestBeginTime string,
       adId string,
       impressionId string,
       referrer string,
       userAgent string,
       userCookie string,
       ip string
   )   STORED AS PARQUET
   LOCATION 's3://myBucket/myParquet/';
   ```

   Choose **Run Query**\.

1. Run the following query to show that you can query this data:

   ```
   SELECT * FROM parquet_hive LIMIT 10;
   ```

   Alternatively, you can select the view \(eye\) icon next to the table's name in **Catalog**:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/parquet_hive_view.png)

   The results should show output similar to this:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/athena/latest/ug/images/parquet_query.png)
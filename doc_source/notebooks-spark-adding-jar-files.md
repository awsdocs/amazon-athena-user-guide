# Adding JAR files to Athena for Apache Spark<a name="notebooks-spark-adding-jar-files"></a>

You can import JAR files that implement custom UDFs \(user defined functions\) to Athena for Apache Spark\.

**Note**  
You cannot use JAR files to add new connectors or new table formats such as Apache Iceberg to a Spark session in Athena\. The Spark session must be configured with the JAR file when the session starts, and Athena does not support adding JAR files at the start of a Spark session\.

## Adding JAR files and registering a UDF<a name="notebooks-spark-adding-jar-files-adding-jar-files-and-registering-a-udf"></a>

The following procedure shows how to add a JAR file from Amazon S3 to your notebook in Athena and register a UDF\.

**To add a JAR file and register a user defined function**

1. Place the following sample Java code into a file called `custom-UDF1.jar`\. The code implements a user defined function called `customUDF1` that reverses the text strings that are passed to it\.

   ```
   package pkg.customUDF1; 
    
   import org.apache.hadoop.hive.ql.metadata.HiveException; 
   import org.apache.hadoop.hive.ql.udf.generic.GenericUDF; 
   import org.apache.hadoop.hive.serde2.io.HiveVarcharWritable; 
   import org.apache.hadoop.hive.serde2.objectinspector.ObjectInspector; 
   import org.apache.hadoop.hive.serde2.objectinspector.primitive.PrimitiveObjectInspectorFactory; 
   import org.apache.hadoop.io.Text; 
    
   import java.util.Arrays; 
   import java.util.Objects; 
   import java.util.stream.Collectors; 
    
   public class UDFClass1 extends GenericUDF { 
    
       @Override 
       public org.apache.hadoop.hive.serde2.objectinspector.ObjectInspector initialize(org.apache.hadoop.hive.serde2.objectinspector.ObjectInspector[] objectInspectors) { 
           if (objectInspectors.length != 1) { 
               throw new RuntimeException("argument length must be 1"); 
           } 
           if (objectInspectors[0].getCategory() != ObjectInspector.Category.PRIMITIVE) { 
               throw new IllegalArgumentException("input type should be primitive"); 
           } 
           return PrimitiveObjectInspectorFactory.writableStringObjectInspector; 
       } 
    
       @Override 
       public Object evaluate(DeferredObject[] deferredObjects) throws HiveException { 
           StringBuilder sb = new StringBuilder(); 
           sb.append(deferredObjects[0].get()).reverse(); 
           String str = sb.toString(); 
           return new Text(str); 
       } 
    
       @Override 
       public String getDisplayString(String[] strings) { 
           return "customUDF1"; 
       } 
   }
   ```

1. Build the Java `custom-UDF1.jar` file, and then upload it to your own location in Amazon S3 \(for example, `s3://DOC-EXAMPLE-BUCKET/custom-UDF1.jar`\)\.

1. In your Athena for Spark notebook, run the following commands\. Make sure that you use the `s3a://` scheme for `add jar`\.

   ```
   spark.sql('add jar s3a://DOC-EXAMPLE-BUCKET/custom-UDF1.jar')  
   spark.sql('create temporary function reverse as "pkg.customUDF1.UDFClass1"')
   ```

1. Create and display a simple table that you can use to test the function\.

   ```
   spark.createDataFrame(["athena", "spark"], "String").write.saveAsTable("simple_table") 
    
   spark.sql('select * from simple_table').show() 
   >> 
   +------+ 
   | value| 
   +------+ 
   |athena| 
   | spark| 
   +------+
   ```

1. Run the UDF on the table and display the result\.

   ```
   spark.sql('select reverse(value) from simple_table').show() 
   >> 
   +--------------+ 
   |reverse(value)| 
   +--------------+ 
   |        anehta| 
   |         kraps| 
   +--------------+
   ```

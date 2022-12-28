# Using Apache Spark in Amazon Athena<a name="notebooks-spark"></a>

Amazon Athena makes it easy to interactively run data analytics and exploration using Apache Spark without the need to plan for, configure, or manage resources\. Running Apache Spark applications on Athena means submitting Spark code for processing and receiving the results directly without the need for additional configuration\. You can use the simplified notebook experience in Amazon Athena console to develop Apache Spark applications using Python or Athena notebook APIs\. Apache Spark on Amazon Athena is serverless and provides automatic, on\-demand scaling that delivers instant\-on compute to meet changing data volumes and processing requirements\.

Amazon Athena offers the following features:
+ **Console usage** – Submit your Spark applications from the Amazon Athena console\.
+ **Scripting** – Quickly and interactively build and debug Apache Spark applications in Python\.
+ **Dynamic scaling** – Amazon Athena automatically determines the compute and memory resources needed to run a job and continuously scales those resources accordingly up to the maximums that you specify\. This dynamic scaling reduces cost without affecting speed\.
+ **Notebook experience** – Use the Athena notebook editor to create, edit, and run computations using a familiar interface\. Athena notebooks are compatible with Jupyter notebooks and contain a list of cells that are executed in order as calculations\. Cell content can include code, text, Markdown, mathematics, plots and rich media\. 

## Considerations and limitations<a name="notebooks-spark-considerations-and-limitations"></a>
+ Currently, Amazon Athena for Apache Spark is available in the following AWS Regions:
  + Asia Pacific \(Tokyo\)
  + Europe \(Ireland\)
  + US East \(N\. Virginia\)
  + US East \(Ohio\)
  + US West \(Oregon\)
+ AWS Lake Formation is not supported\.
+ Cross\-engine view queries are not supported\. Views created by Athena SQL are not queryable by Athena for Spark\. Because views for the two engines are implemented differently, they are not compatible for cross\-engine use\.
+ For a list of supported Python libraries, see the [List of preinstalled Python libraries](notebooks-spark-preinstalled-python-libraries.md)\.
+ For information on troubleshooting Spark notebooks, sessions, and workgroups in Athena, see [Troubleshooting Athena for Spark](notebooks-spark-troubleshooting.md)\.
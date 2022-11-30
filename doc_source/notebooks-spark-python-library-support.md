# Python library support in Amazon Athena for Apache Spark<a name="notebooks-spark-python-library-support"></a>

This page describes the terminology used and lifecycle management followed for the runtimes, libraries, and packages used in Amazon Athena for Apache Spark\.

## Definitions<a name="notebooks-spark-python-library-support-definitions"></a>
+ **Amazon Athena for Apache Spark** is a customized version of open source Apache Spark\.
+ The **Athena runtime** is the environment in which your code runs\. The environment includes a Python interpreter and PySpark libraries\.
+ An **external library or package** is a Java or Scala JAR or Python library that is not part of the Athena runtime but can be included in Athena for Spark jobs\. External packages can be built by Amazon or by you\.
+ A **convenience package** is a collection of external packages selected by Athena that you can choose to include in your Spark applications\.
+ A **bundle** combines the Athena runtime and a convenience package\.
+ A **user library** is an external library or package that you explicitly add to your Athena for Spark job\.
  + A user library is an external package that is not part of a convenience package\. A user library requires loading and installation, as when you write some `.py` files, zip them up, and then add the `.zip` file to your application\.
+ An **Athena for Spark application** is a job or query that you submit to Athena for Spark\.

## Lifecycle management<a name="notebooks-spark-python-library-support-lifecycle-management"></a>

### Runtime versioning and deprecation<a name="notebooks-spark-python-library-support-runtime-versioning-and-deprecation"></a>

The main component in the Athena runtime is the Python interpreter\. Because Python is an evolving language, new versions are released regularly and support removed for older versions\. Athena does not recommend that you run programs with deprecated Python interpreter versions and highly recommends that you use the latest Athena runtime whenever possible\.

The Athena runtime deprecation schedule is as follows:

1. After Athena provides a new runtime, Athena will continue to support the previous runtime for 6 months\. During that time, Athena will apply security patches and updates to the previous runtime\.

1. After 6 months, Athena will end support for the previous runtime\. Athena will no longer apply security patches and other updates to the previous runtime\. Spark applications using the previous runtime will no longer be eligible for technical support\.

1. After 12 months, you will no longer be able to update or edit Spark applications in a workgroup that uses the previous runtime\. We recommend that you update your Spark applications before this time period ends\. After the time period ends, you can still run existing notebooks, but any notebooks that still use the previous runtime will log a warning to that effect\.

1. After 18 months, you will no longer be able to run jobs in the workgroup using the previous runtime\.

### Convenience package versioning and deprecation<a name="notebooks-spark-python-library-support-convenience-package-versioning-and-deprecation"></a>

The contents of convenience packages change over time\. Athena occasionally adds, removes, or upgrades these convenience packages\. 

Athena uses the following guidelines for convenience packages:
+ Convenience packages have a simple versioning scheme such as 1, 2, 3\.
+ Each convenience package version includes specific versions of external packages\. After Athena creates a convenience package, the convenience package's set of external packages and their corresponding versions do not change\.
+ Athena creates a new convenience package version when it includes a new external package, removes an external package, or upgrades the version of one or more external packages\.

Athena deprecates a convenience package when it deprecates the Athena runtime that the package uses\. Athena can deprecate packages sooner to limit the number of bundles that it supports\.

The convenience package deprecation schedule follows the Athena runtime deprecation schedule\.
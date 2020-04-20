# Resilience in Athena<a name="security-resilience"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Athena offers several features to help support your data resiliency and backup needs\.

Athena is serverless, so there is no infrastructure to set up or manage\. Athena is highly available and executes queries using compute resources across multiple Availability Zones, automatically routing queries appropriately if a particular Availability Zone is unreachable\. Athena uses Amazon S3 as its underlying data store, making your data highly available and durable\. Amazon S3 provides durable infrastructure to store important data and is designed for durability of 99\.999999999% of objects\. Your data is redundantly stored across multiple facilities and multiple devices in each facility\.
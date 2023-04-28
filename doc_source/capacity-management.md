# Managing query processing capacity<a name="capacity-management"></a>

You can use capacity reservations to get dedicated processing capacity for the queries you run in Athena\. With capacity reservations, you can take advantage of workload management capabilities that help you prioritize, control, and scale your most important interactive workloads\. For example, you can add capacity at any time to increase the number of queries you can run concurrently, control which workloads can use the capacity, and share capacity among workloads\. Capacity is fully\-managed by Athena and held for you as long as you require\. Setup is easy and no changes to your SQL statements are required\.

To get processing capacity for your queries, you create a capacity reservation, specify the number of Data Processing Units \(DPUs\) that you require, and assign one or more workgroups to the reservation\.

Workgroups play an important role when you use capacity reservations\. Workgroups allow you to organize queries into logical groupings\. With capacity reservations, you selectively assign capacity to workgroups so that you control how the queries for each workgroup behave and how they are billed\. For more information about workgroups, see [Using workgroups to control query access and costs](manage-queries-control-costs-with-workgroups.md)\.

Assigning workgroups to reservations lets you give priority to the queries that you submit to the assigned workgroups\. For example, you could allocate capacity to a workgroup used for time\-sensitive financial reporting queries to isolate those queries from less critical queries in another workgroup\. This enables consistent query execution for critical workloads while allowing other workloads to run independently\.

You can use capacity reservations and workgroups together to meet different requirements\. The following are some example scenarios:
+ **Isolation** – To isolate an important workload, you assign a single workgroup to one reservation\.
+ **Sharing** – Multiple workloads use capacity from one reservation\. For example, if you want a predictable monthly cost for a specific set of workloads, you can assign multiple workgroups to a single reservation\. The assigned workgroups share the reservation's capacity\. 
+ **Multiple model** – You can use capacity reservations and per\-query billing in the same account\. For example, to ensure reliable execution of queries that support a production application, you assign a workgroup for those queries to a capacity reservation\. To develop the queries before you move them to the production workgroup, you use a separate workgroup that is not part of a reservation and uses per\-query billing\.

## Understanding DPUs<a name="capacity-management-understanding-dpus"></a>

Capacity is measured in Data Processing Units \(DPUs\)\. DPUs represent the compute and memory resources used by Athena to access and process data on your behalf\. One DPU provides 4 vCPUs and 16 GB of memory\. The number of DPUs that you specify determines the number of queries that you can run concurrently\. For example, a reservation with 256 DPUs can allow approximately twice the number of concurrent queries than a reservation with 128 DPUs\.

You can create up to 100 capacity reservations with up to 1,000 total DPUs per account and region\. The minimum number of DPUs that you can request is 24\. If you require more than 1,000 DPUs for your use case, please reach out to [athena\-feedback@amazon\.com](mailto:athena-feedback@amazon.com?subject=Athena Provisioned Capacity DPU Limit Request)\.

For information about estimating your capacity requirements, see [Determining capacity requirements](capacity-management-requirements.md)\. For pricing information, see [Amazon Athena pricing](http://aws.amazon.com/athena/pricing/)\.

## Considerations and limitations<a name="capacity-management-considerations-limitations"></a>
+ The feature requires [Athena engine version 3](engine-versions-changing.md)\. 
+ A single workgroup can be assigned to at most one reservation at a time, and you can add a maximum of 20 workgroups to a reservation\.
+ You cannot add Spark enabled workgroups to a capacity reservation\.
+ To delete a workgroup that has been assigned to a reservation, remove the workgroup from the reservation first\.
+ The minimum number of DPUs you can provision is 24\. 
+ You can create up to 100 capacity reservations with up to 1,000 total DPUs per account and region\. 
+ Requests for capacity are not guaranteed and can take up to 30 minutes to complete\.
+ There is a minimum billing period of 8 hours\. For pricing information, see [Amazon Athena pricing](http://aws.amazon.com/athena/pricing/)\.
+ Reserved capacity is not transferable to another capacity reservation, AWS account, or AWS Region\.
+ DDL queries on capacity reservations consume DPUs\.
+ Queries that run on provisioned capacity do not count against your active query limits for DDL and DML\.
+ If all DPUs are in use, submitted queries are queued\. Such queries are not rejected and do not go to on\-demand capacity\.
+ Support for creating and configuring reservations with AWS CloudFormation is planned\.
+ Currently, the feature is available in the following AWS Regions:
  + US East \(Ohio\)
  + US East \(N\. Virginia\)
  + US West \(Oregon\)
  + Asia Pacific \(Singapore\)
  + Asia Pacific \(Sydney\)
  + Asia Pacific \(Tokyo\)
  + Europe \(Ireland\)
  + Europe \(Stockholm\)

**Topics**
+ [Understanding DPUs](#capacity-management-understanding-dpus)
+ [Considerations and limitations](#capacity-management-considerations-limitations)
+ [Determining capacity requirements](capacity-management-requirements.md)
+ [Creating capacity reservations](capacity-management-creating-capacity-reservations.md)
+ [Managing reservations](capacity-management-managing-reservations.md)
+ [IAM policies for capacity reservations](capacity-reservations-iam-policy.md)
+ [Athena capacity reservation APIs](capacity-management-api-list.md)
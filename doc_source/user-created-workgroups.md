# How Workgroups Work<a name="user-created-workgroups"></a>

Workgroups in Athena have the following characteristics:
+ By default, each account has a primary workgroup and the default permissions allow all authenticated users access to this workgroup\. The primary workgroup cannot be deleted\.
+ Each workgroup that you create shows saved queries and query history only for queries that ran in it, and not for all queries in the account\. This separates your queries from other queries within an account and makes it more efficient for you to locate your own saved queries and queries in history\.
+ Disabling a workgroup prevents queries from running in it, until you enable it\. Queries sent to a disabled workgroup fail, until you enable it again\.
+ If you have permissions, you can delete an empty workgroup, and a workgroup that contains saved queries\. In this case, before deleting a workgroup, Athena warns you that saved queries are deleted\. Before deleting a workgroup to which other users have access, make sure its users have access to other workgroups in which they can continue to run queries\. 
+ You can set up workgroup\-wide settings and enforce their usage by all queries that run in a workgroup\. The settings include query results location in Amazon S3, expected bucket owner, encryption, and control of objects written to the query results bucket\.
**Important**  
When you enforce workgroup\-wide settings, all queries that run in this workgroup use workgroup settings\. This happens even if their client\-side settings may differ from workgroup settings\. For information, see [Workgroup Settings Override Client\-Side Settings](workgroups-settings-override.md)\.

## Limitations for Workgroups<a name="limitations-workgroups"></a>
+ You can create up to 1000 workgroups per Region in your account\. 
+ The primary workgroup cannot be deleted\. 
+ You can open up to ten query tabs within each workgroup\. When you switch between workgroups, your query tabs remain open for up to three workgroups\. 
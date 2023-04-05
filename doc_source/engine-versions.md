# Athena engine versioning<a name="engine-versions"></a>

Athena occasionally releases a new engine version to provide improved performance, functionality, and code fixes\. When a new engine version is available, Athena notifies you through the Athena console and your [AWS Health Dashboard](https://aws.amazon.com/premiumsupport/technology/personal-health-dashboard/)\. Your AWS Health Dashboard notifies you about events that can affect your AWS services or account\. For more information about AWS Health Dashboard, see [Getting started with the AWS Health Dashboard](https://docs.aws.amazon.com/health/latest/ug/getting-started-phd.html)\.

Engine versioning is configured per [workgroup](manage-queries-control-costs-with-workgroups.md)\. You can use workgroups to control which query engine your queries use\. The query engine that is in use is shown in the query editor, on the details page for the workgroup, and by the Athena APIs\.

You can choose to upgrade your workgroups as soon as a new engine is available or continue using the older version until it is no longer supported\. You can also let Athena decide when to upgrade your workgroups\. This is the default setting\. If you take no action, Athena notifies you in advance prior to upgrading your workgroups\.  If you let Athena decide, Athena upgrades your workgroups for you unless it finds incompatibilities\.

When you start using a new engine version, a small subset of queries may break due to incompatibilities\. You can use workgroups to test your queries in advance of the upgrade by creating a test workgroup that uses the new engine or by test upgrading an existing workgroup\. For more information, see [Testing queries in advance of an engine version upgrade](engine-versions-changing.md#engine-versions-testing)\.

**Topics**
+ [Changing Athena engine versions](engine-versions-changing.md)
+ [Athena engine version reference](engine-versions-reference.md)
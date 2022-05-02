# Querying Amazon GuardDuty findings<a name="querying-guardduty"></a>

[Amazon GuardDuty](https://aws.amazon.com/guardduty/) is a security monitoring service for helping to identify unexpected and potentially unauthorized or malicious activity in your AWS environment\. When it detects unexpected and potentially malicious activity, GuardDuty generates security [findings](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html) that you can export to Amazon S3 for storage and analysis\. After you export your findings to Amazon S3, you can use Athena to query them\. This article shows how to create a table in Athena for your GuardDuty findings and query them\.

For more information about Amazon GuardDuty, see the [Amazon GuardDuty User Guide](https://docs.aws.amazon.com/guardduty/latest/ug/)\.

## Prerequisites<a name="querying-guardduty-prerequisites"></a>
+ Enable the GuardDuty feature for exporting findings to Amazon S3\. For steps, see [Exporting findings](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_exportfindings.html) in the Amazon GuardDuty User Guide\.

## Creating a table in Athena for GuardDuty findings<a name="querying-guardduty-creating-a-table-in-athena-for-guardduty-findings"></a>

To query your GuardDuty findings from Athena, you must create a table for them\.

**To create a table in Athena for GuardDuty findings**

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Paste the following DDL statement into the Athena console\. Modify the values in `LOCATION 's3://findings-bucket-name/AWSLogs/account-id/GuardDuty/'` to point to your GuardDuty findings in Amazon S3\.

   ```
   CREATE EXTERNAL TABLE `gd_logs` (
     `schemaversion` string,
     `accountid` string,
     `region` string,
     `partition` string,
     `id` string,
     `arn` string,
     `type` string,
     `resource` string,
     `service` string,
     `severity` string,
     `createdate` string,
     `updatedate` string,
     `title` string,
     `description` string)
   ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION 's3://findings-bucket-name/AWSLogs/account-id/GuardDuty/'
    TBLPROPERTIES ('has_encrypted_data'='true')
   ```
**Note**  
The SerDe expects each JSON document to be on a single line of text with no line termination characters separating the fields in the record\. If the JSON text is in pretty print format, you may receive an error message like HIVE\_CURSOR\_ERROR: Row is not a valid JSON Object or HIVE\_CURSOR\_ERROR: JsonParseException: Unexpected end\-of\-input: expected close marker for OBJECT when you attempt to query the table after you create it\. For more information, see [JSON Data Files](https://github.com/rcongiu/Hive-JSON-Serde#json-data-files) in the OpenX SerDe documentation on GitHub\. 

1. Run the query in the Athena console to register the `gd_logs` table\. When the query completes, the findings are ready for you to query from Athena\.

## Example queries<a name="querying-guardduty-examples"></a>

The following examples show how to query GuardDuty findings from Athena\.

**Example – DNS data exfiltration**  
The following query returns information about Amazon EC2 instances that might be exfiltrating data through DNS queries\.  

```
SELECT
    title,
    severity,
    type,
    id AS FindingID,
    accountid,
    region,
    createdate,
    updatedate,
    json_extract_scalar(service, '$.count') AS Count,
    json_extract_scalar(resource, '$.instancedetails.instanceid') AS InstanceID,
    json_extract_scalar(service, '$.action.actiontype') AS DNS_ActionType,
    json_extract_scalar(service, '$.action.dnsrequestaction.domain') AS DomainName,
    json_extract_scalar(service, '$.action.dnsrequestaction.protocol') AS protocol,
    json_extract_scalar(service, '$.action.dnsrequestaction.blocked') AS blocked
FROM gd_logs
WHERE type = 'Trojan:EC2/DNSDataExfiltration'
ORDER BY severity DESC
```

**Example – Unauthorized IAM user access**  
The following query returns all `UnauthorizedAccess:IAMUser` finding types for an IAM Principal from all regions\.   

```
SELECT title,
         severity,
         type,
         id,
         accountid,
         region,
         createdate,
         updatedate,
         json_extract_scalar(service, '$.count') AS Count, 
         json_extract_scalar(resource, '$.accesskeydetails.username') AS IAMPrincipal, 
         json_extract_scalar(service,'$.action.awsapicallaction.api') AS APIActionCalled
FROM gd_logs
WHERE type LIKE '%UnauthorizedAccess:IAMUser%' 
ORDER BY severity desc;
```

## Tips for querying GuardDuty findings<a name="querying-guardduty-tips"></a>

When you create your query, keep the following points in mind\.
+ To extract data from nested JSON fields, use the Presto `json_extract` or `json_extract_scalar` functions\. For more information, see [Extracting data from JSON](extracting-data-from-JSON.md)\.
+ Make sure that all characters in the JSON fields are in lower case\.
+  For information about downloading query results, see [Downloading query results files using the Athena console](querying.md#saving-query-results)\.
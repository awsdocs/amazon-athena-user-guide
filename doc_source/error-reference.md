# Athena Error Catalog<a name="error-reference"></a>

Athena provides standardized error information to help you understand failed queries and take steps after a query failure occurs\. The `AthenaError` feature includes an `ErrorCategory` field and an `ErrorType` field\. `ErrorCategory` specifies whether the cause of the failed query is due to system error, user error, or other error\. `ErrorType` provides more granular information regarding the source of the failure\. By combining the two fields, you can get a better understanding of the circumstances surrounding and causes for the specific error that occurred\.

## Error Category<a name="error-reference-error-category"></a>

The following table lists the Athena error category values and their meanings\.


****  

| Error Category | Source | 
| --- | --- | 
| 1 | SYSTEM | 
| 2 | USER | 
| 3 | OTHER | 

## Error Type Reference<a name="error-reference-error-type-reference"></a>

The following table lists the Athena error type values and their meanings\.


****  

| Error Type | Description | 
| --- | --- | 
| 0 | Query exhausted resources at this scale factor | 
| 1 | Query exhausted resources at this scale factor | 
| 2 | Query exhausted resources at this scale factor | 
| 3 | Query exhausted resources at this scale factor | 
| 4 | Query exhausted resources at this scale factor | 
| 5 | Query exhausted resources at this scale factor | 
| 6 | Query exhausted resources at this scale factor | 
| 7 | Query exhausted resources at this scale factor | 
| 8 | Query exhausted resources at this scale factor | 
| 100 | Internal service error | 
| 200 | Query engine had an internal error | 
| 201 | Query engine had an internal error | 
| 202 | Query engine had an internal error | 
| 203 | Driver error | 
| 204 | The metastore had an error | 
| 205 | Query engine had an internal error | 
| 206 | Query timed out | 
| 207 | Query engine had an internal error | 
| 208 | Query engine had an internal error | 
| 209 | Failed to cancel query | 
| 210 | Query timed out | 
| 211 | Query engine had an internal error | 
| 212 | Query engine had an internal error | 
| 213 | Query engine had an internal error | 
| 214 | Query engine had an internal error | 
| 215 | Query engine had an internal error | 
| 216 | Query engine had an internal error | 
| 217 | Query engine had an internal error | 
| 218 | Query engine had an internal error | 
| 219 | Query engine had an internal error | 
| 220 | Query engine had an internal error | 
| 221 | Query engine had an internal error | 
| 222 | Query engine had an internal error | 
| 223 | Query engine had an internal error | 
| 224 | Query engine had an internal error | 
| 225 | Query engine had an internal error | 
| 226 | Query engine had an internal error | 
| 227 | Query engine had an internal error | 
| 228 | Query engine had an internal error | 
| 229 | Query engine had an internal error | 
| 230 | Query engine had an internal error | 
| 231 | Query engine had an internal error | 
| 232 | Query engine had an internal error | 
| 233 | Iceberg error | 
| 234 | Lake Formation error | 
| 235 | Query engine had an internal error | 
| 236 | Query engine had an internal error | 
| 237 | Serialization error | 
| 238 | Failed to upload metadata to Amazon S3 | 
| 239 | General persistence error | 
| 240 | Failed to submit query | 
| 300 | Internal service error | 
| 301 | Internal service error | 
| 302 | Internal service error | 
| 303 | Internal service error | 
| 400 | Internal service error | 
| 401 | Failed to write query results to Amazon S3 | 
| 402 | Failed to write query results to Amazon S3 | 
| 1000 | User error | 
| 1001 | Data error | 
| 1002 | Data error | 
| 1003 | DDL task failed | 
| 1004 | Schema error | 
| 1005 | Serialization error | 
| 1006 | Syntax error | 
| 1007 | Data error | 
| 1008 | Query rejected | 
| 1009 | Query failed | 
| 1010 | Internal service error | 
| 1011 | Query canceled by user | 
| 1012 | Query engine had an internal error | 
| 1013 | Query engine had an internal error | 
| 1014 | Query canceled by user | 
| 1100 | Invalid argument provided | 
| 1101 | Invalid property provided | 
| 1102 | Query engine had an internal error | 
| 1103 | Invalid table property provided | 
| 1104 | Query engine had an internal error | 
| 1105 | Query engine had an internal error | 
| 1106 | Invalid function argument provided | 
| 1107 | Invalid view | 
| 1108 | Failed to register function | 
| 1109 | Provided Amazon S3 path not found | 
| 1110 | Provided table or view does not exist | 
| 1200 | Query not supported | 
| 1201 | Provided decoder not supported | 
| 1202 | Query type not supported | 
| 1300 | General not found error | 
| 1301 | General entity not found | 
| 1302 | File not found | 
| 1303 | Provided function or function implementation not found | 
| 1304 | Query engine had an internal error | 
| 1305 | Query engine had an internal error | 
| 1306 | Amazon S3 bucket not found | 
| 1307 | Selected engine not found | 
| 1308 | Query engine had an internal error | 
| 1400 | Throttling error | 
| 1401 | Query failed due to AWS Glue throttling | 
| 1402 | Query failed due to too many table versions in AWS Glue | 
| 1403 | Query failed due to Amazon S3 throttling | 
| 1404 | Query failed due to Amazon Athena throttling | 
| 1405 | Query failed due to Amazon Athena throttling | 
| 1406 | Query failed due to Amazon Athena throttling | 
| 1500 | Permission error | 
| 1501 | Amazon S3 permission error | 
| 9999 | Internal service error | 
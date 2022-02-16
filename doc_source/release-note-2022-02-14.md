# February 14, 2022<a name="release-note-2022-02-14"></a>

Published on 2022\-02\-14

This release adds the `ErrorType` subfield to the [AthenaError](https://docs.aws.amazon.com/athena/latest/APIReference/API_AthenaError.html) response object in the Athena [GetQueryExecution](https://docs.aws.amazon.com/athena/latest/APIReference/API_GetQueryExecution.html) API action\.

While the existing `ErrorCategory` field indicates the general source of a failed query \(system, user, or other\), the new `ErrorType` field provides more granular information about the error that occurred\. Combine the information from both fields to gain insight into the causes of query failure\. 

For more information, see [Athena Error Catalog](error-reference.md)\.
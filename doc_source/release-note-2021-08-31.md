# August 31, 2021<a name="release-note-2021-08-31"></a>

Published on 2021\-08\-31

Athena announces the following feature enhancements and bug fixes\.
+ **Athena federation enhancements** – Athena has added support to map types and better support for complex types as part of the [Athena Query Federation SDK](https://github.com/awslabs/aws-athena-query-federation/releases)\. This version also includes some memory enhancements and performance optimizations\.
+ **New error categories** – Introduced the `USER` and `SYSTEM` error categories in error messages\. These categories help you distinguish between errors that you can fix yourself \(`USER`\) and errors that can require assistance from Athena support \(`SYSTEM`\)\.
+ **Federated query error messaging** – Updated `USER_ERROR` categorizations for federated query related errors\.
+ **JOIN** – Fixed spill\-to\-disk related bugs and memory issues to enhance performance and reduce memory errors in `JOIN` operations\.
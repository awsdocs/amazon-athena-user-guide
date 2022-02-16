# January 28, 2022<a name="release-note-2022-01-28"></a>

Published on 2022\-01\-28

Athena announces the following engine feature enhancements\.
+ **Apache Hudi** – Snapshot queries on Hudi Merge on Read \(MoR\) tables can now read timestamp columns that have the `INT64` data type\.
+ **UNION queries** – Performance improvement and data scan reduction for certain `UNION` queries that scan the same table multiple times\.
+ **Disjunct queries** – Performance improvement for queries that have only disjunct values for each partition column on the filter\.
+ **Partition projection enhancements**
  + Multiple disjunct values are now allowed on the filter condition for columns of the `injected` type\. For more information, see [Injected Type](partition-projection-supported-types.md#partition-projection-injected-type)\.
  + Performance improvement for columns of string\-based types like `CHAR` or `VARCHAR` that have only disjunct values on the filter\.
# Supported Types for Partition Projection<a name="partition-projection-supported-types"></a>

A table can have any combination of `enum`, `integer`, `date,` or `injected` partition column types\.

## Enum Type<a name="partition-projection-enum-type"></a>

Use the `enum` type for partition columns whose values are members of an enumerated set \(for example, airport codes or AWS Regions\)\.

Define the partition properties in the table as follows:


****  

| Property Name | Example Values | Description | 
| --- | --- | --- | 
| projection\.columnName\.type |  `enum`  | Required\. The projection type to use for column columnName\. The value must be enum \(case insensitive\) to signal the use of the enum type\. Leading and trailing white space is allowed\. | 
| projection\.columnName\.values |  `A,B,C,D,E,F,G,Unknown`  | Required\. A comma\-separated list of enumerated partition values for column columnName\. Any white space is considered part of an enum value\. | 

**Note**  
As a best practice we recommend limiting the use of `enum` based partition projections to a few dozen or less\. Although there is no specific limit for `enum` projections, the total size of your table’s metadata cannot exceed the AWS Glue limit of about 1MB when gzip compressed\. Note that this limit is shared across key parts of your table like column names, location, storage format, and others\. If you find yourself using more than a few dozen unique IDs in your `enum` projection, consider an alternative approach such as bucketing into a smaller number of unique values in a surrogate field\. By trading off cardinality, you can control the number of unique values in your `enum` field\. 

## Integer Type<a name="partition-projection-integer-type"></a>

Use the integer type for partition columns whose possible values are interpretable as integers within a defined range\. Projected integer columns are currently limited to the range of a Java signed long \(\-263 to 263\-1 inclusive\)\.


****  

| Property Name | Example Values | Description | 
| --- | --- | --- | 
| projection\.columnName\.type |  `integer`  | Required\. The projection type to use for column columnName\. The value must be integer \(case insensitive\) to signal the use of the integer type\. Leading and trailing white space is allowed\. | 
| projection\.columnName\.range |  `0,10` `-1,8675309` `0001,9999`  | Required\. A two\-element comma\-separated list that provides the minimum and maximum range values to be returned by queries on the column columnName\. These values are inclusive, can be negative, and can have leading zeroes\. Leading and trailing white space is allowed\. | 
| projection\.columnName\.interval |  `1` `5`  | Optional\. A positive integer that specifies the interval between successive partition values for the column columnName\. For example, a range value of "1,3" with an interval value of "1" produces the values 1, 2, and 3\. The same range value with an interval value of "2" produces the values 1 and 3, skipping 2\. Leading and trailing white space is allowed\. The default is 1\. | 
| projection\.columnName\.digits |  `1` `5`  | Optional\. A positive integer that specifies the number of digits to include in the partition value's final representation for column columnName\. For example, a range value of "1,3" that has a digits value of "1" produces the values 1, 2, and 3\. The same range value with a digits value of "2" produces the values 01, 02, and 03\. Leading and trailing white space is allowed\. The default is no static number of digits and no leading zeroes\. | 

## Date Type<a name="partition-projection-date-type"></a>

Use the date type for partition columns whose values are interpretable as dates \(with optional times\) within a defined range\.

**Important**  
Projected date columns are generated in Coordinated Universal Time \(UTC\) at query execution time\.


****  

| Property Name | Example Values | Description | 
| --- | --- | --- | 
| projection\.columnName\.type |  `date`  | Required\. The projection type to use for column columnName\. The value must be date \(case insensitive\) to signal the use of the date type\. Leading and trailing white space is allowed\. | 
| projection\.columnName\.range |  `201701,201812` `01-01-2010,12-31-2018` `NOW-3YEARS,NOW` `201801,NOW+1MONTH`  |  Required\. A two\-element, comma\-separated list which provides the minimum and maximum `range` values for the column *columnName*\. These values are inclusive and can use any format compatible with the Java `java.time.*` date types\. Both the minimum and maximum values must use the same format\. The format specified in the `.format` property must be the format used for these values\. This column can also contain relative date strings, formatted in this regular expression pattern: `\s*NOW\s*(([\+\-])\s*([0-9]+)\s*(YEARS?\|MONTHS?\|WEEKS?\|DAYS?\|HOURS?\|MINUTES?\|SECONDS?)\s*)?` White spaces are allowed, but in date literals are considered part of the date strings themselves\.  | 
| projection\.columnName\.format |  `yyyyMM` `dd-MM-yyyy` `dd-MM-yyyy-HH-mm-ss`  | Required\. A date format string based on the Java date format [DateTimeFormatter](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html)\. Can be any supported Java\.time\.\* type\. | 
| projection\.columnName\.interval |  `1` `5`  |  A positive integer that specifies the interval between successive partition values for column *columnName*\. For example, a `range` value of `2017-01,2018-12` with an `interval` value of `1` and an `interval.unit` value of `MONTHS` produces the values 2017\-01, 2017\-02, 2017\-03, and so on\. The same `range` value with an `interval` value of `2` and an `interval.unit` value of `MONTHS` produces the values 2017\-01, 2017\-03, 2017\-05, and so on\. Leading and trailing white space is allowed\. When the provided dates are at single\-day or single\-month precision, the `interval` is optional and defaults to 1 day or 1 month, respectively\. Otherwise, `interval` is required\.  | 
| projection\.columnName\.interval\.unit |  `YEARS` `MONTHS` `WEEKS` `DAYS` `HOURS` `MINUTES` `SECONDS` `MILLISECONDS`  |  A time unit word that represents the serialized form of a [ChronoUnit](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/ChronoUnit.html)\. Possible values are `YEARS`, `MONTHS`, `WEEKS`, `DAYS`, `HOURS`, `MINUTES`, `SECONDS`, or `MILLISECONDS`\. These values are case insensitive\. When the provided dates are at single\-day or single\-month precision, the `interval.unit` is optional and defaults to 1 day or 1 month, respectively\. Otherwise, the `interval.unit` is required\.  | 

## Injected Type<a name="partition-projection-injected-type"></a>

Use the injected type for partition columns with possible values that cannot be procedurally generated within some logical range but that are provided in a query's `WHERE` clause as a single value\.

It is important to keep in mind the following points:
+ Queries on injected columns fail if a filter expression is not provided for each injected column\.
+ Queries with multiple values for a filter expression on an injected column succeed only if the values are disjunct\.
+ Only columns of `string` type are supported\.


****  

| Property Name | Value | Description | 
| --- | --- | --- | 
| projection\.columnName\.type |  `injected`  | Required\. The projection type to use for the column columnName\. Only the string type is supported\. The value specified must be injected \(case insensitive\)\. Leading and trailing white space is allowed\. | 

For more information, see [Injection](partition-projection-dynamic-id-partitioning.md#partition-projection-injection)\.
# September 19, 2019<a name="release-note-2019-09-19"></a>

Published on 2019\-12\-17

Amazon Athena adds support for inserting new data to an existing table using the `INSERT INTO` statement\. You can insert new rows into a destination table based on a `SELECT` query statement that runs on a source table, or based on a set of values that are provided as part of the query statement\. Supported data formats include Avro, JSON, ORC, Parquet, and text files\.

`INSERT INTO` statements can also help you simplify your ETL process\. For example, you can use `INSERT INTO` in a single query to select data from a source table that is in JSON format and write to a destination table in Parquet format\.

`INSERT INTO` statements are charged based on the number of bytes that are scanned in the `SELECT` phase, similar to how Athena charges for `SELECT` queries\. For more information, see [Amazon Athena pricing](https://aws.amazon.com/athena/pricing/)\.

For more information about using `INSERT INTO`, including supported formats, SerDes and examples, see [INSERT INTO](https://docs.aws.amazon.com/athena/latest/ug/insert-into.html) in the Athena User Guide\.
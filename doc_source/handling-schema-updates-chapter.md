# Handling Schema Updates<a name="handling-schema-updates-chapter"></a>

The data in tables and schemas changes over time, as you add, remove, or change table columns\. When you change the table's schema, choose a data format that allows these updates and keeps Athena queries operational\.

Athena is a schema\-on\-read query engine that manages the schema and the underlying data differently from an RDMS\. When you load data into an RDMS, it enforces the schema on\-write, and when you later change schemas, it rewrites the underlying data\. When you create a table in Athena, it applies schemas when reading the data and does not change the data\.


+ [Types of Updates](types-of-updates.md)
+ [Best Practices for Handling Schema Updates](updates-best-practices.md)
+ [Updates in Tables with Partitions](updates-and-partitions.md)
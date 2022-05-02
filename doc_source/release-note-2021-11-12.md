# November 12, 2021<a name="release-note-2021-11-12"></a>

Published on 2021\-11\-12

You can now use Amazon Athena to run federated queries on data sources located in an AWS account other than your own\. Until today, querying this data required the data source and its connector to use the same AWS account as the user that queried the data\.

As a data administrator, you can enable cross\-account federated queries by sharing your data connector with a data analyst's account\. As a data analyst, you can add a data connector that a data administrator has shared with you to your account\. Configuration changes to the connector in the originating account apply automatically to the shared connector\.

For information about enabling cross\-account federated queries, see [Enabling cross\-account federated queries](xacct-fed-query-enable.md)\. To learn about querying federated sources, see [Using Amazon Athena Federated Query](connect-to-a-data-source.md) and [Writing federated queries](writing-federated-queries.md)\.
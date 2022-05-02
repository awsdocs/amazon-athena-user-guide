# March 11, 2020<a name="release-note-2020-03-11"></a>

Published on 2020\-03\-11

Amazon Athena now publishes Amazon CloudWatch Events for query state transitions\. When a query transitions between states \-\- for example, from Running to a terminal state such as Succeeded or Cancelled \-\- Athena publishes a query state change event to CloudWatch Events\. The event contains information about the query state transition\. For more information, see [Monitoring Athena queries with CloudWatch events](athena-cloudwatch-events.md)\.
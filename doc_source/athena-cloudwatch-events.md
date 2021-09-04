# Monitoring Athena Queries with CloudWatch Events<a name="athena-cloudwatch-events"></a>

You can use Amazon Athena with Amazon CloudWatch to receive real\-time notifications regarding the state of your queries\. When a query you have submitted transitions states, Athena publishes an event to CloudWatch Events containing information about that query state transition\. You can write simple rules for events that are of interest to you and take automated actions when an event matches a rule\. For example, you can create a rule that invokes an AWS Lambda function when a query reaches a terminal state\. Events are emitted on a best effort basis\.

Before you create event rules for Athena, you should do the following:
+ Familiarize yourself with events, rules, and targets in CloudWatch Events\. For more information, see [What Is Amazon CloudWatch Events?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html) For more information about how to set up rules, see [Getting Started with CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/CWE_GettingStarted.html)\.
+ Create the target or targets to use in your event rules\.

**Note**  
Athena currently offers one type of event, Athena Query State Change, but may add other event types and details\. If you are programmatically deserializing event JSON data, make sure that your application is prepared to handle unknown properties if additional properties are added\.

## Athena Event Format<a name="athena-cloudwatch-events-pattern"></a>

The following is the basic pattern for an Amazon Athena event\.

```
{
    "source":[
        "aws.athena"
    ],
    "detail-type":[
        "Athena Query State Change"
    ],
    "detail":{
        "currentState":[
            "SUCCEEDED"
        ]
    }
}
```

## Athena Query State Change Event<a name="athena-cloudwatch-events-athena-query-state-change"></a>

The following is the format of an Athena Query State Change event\.

```
{
    "version":"0",
    "id":"abcdef00-1234-5678-9abc-def012345678",
    "detail-type":"Athena Query State Change",
    "source":"aws.athena",
    "account":"123456789012",
    "time":"2019-10-06T09:30:10Z",
    "region":"us-east-1",
    "resources":[

    ],
    "detail":{
        "versionId":"0",
        "currentState":"SUCCEEDED",
        "previousState":"RUNNING",
        "statementType":"DDL",
        "queryExecutionId":"01234567-0123-0123-0123-012345678901",
        "workgroupName":"primary",
        "sequenceNumber":"3"
    }
}
```

### Output Properties<a name="athena-cloudwatch-events-query-state-change-output-properties"></a>

The JSON output includes the following properties\.


****  

| Property | Description | 
| --- | --- | 
| versionId | The version number for the detail object's schema\. | 
| currentState | The state that the query transitioned to at the time of the event\. | 
| previousState | The state that the query transitioned from at the time of the event\. | 
| statementType | The type of query statement that was run\. | 
| queryExecutionId | The unique identifier for the query that ran\. | 
| workgroupName | The name of the workgroup in which the query ran\. | 
| sequenceNumber | A monotonically increasing number that allows for deduplication and ordering of incoming events that involve a single query that ran\. When duplicate events are published for the same state transition, the sequenceNumber value is the same\. When a query experiences a state transition more than once, such as queries that experience rare requeuing, you can use sequenceNumber to order events with identical currentState and previousState values\. | 

## Example<a name="athena-cloudwatch-events-examples"></a>

The following example publishes events to an Amazon SNS topic to which you have subscribed\. When Athena is queried, you receive an email\. The example assumes that the Amazon SNS topic exists and that you have subscribed to it\.

**To publish Athena events to an Amazon SNS topic**

1. Create the target for your Amazon SNS topic\. Give the CloudWatch Events Service Principal `events.amazonaws.com` permission to publish to your Amazon SNS topic, as in the following example\.

   ```
   {
       "Effect":"Allow",
       "Principal":{
           "Service":"events.amazonaws.com"
       },
       "Action":"sns:Publish",
       "Resource":"arn:aws:sns:us-east-1:111111111111:your-sns-topic"
   }
   ```

1. Use the AWS CLI `events put-rule` command to create a rule for Athena events, as in the following example\.

   ```
   aws events put-rule --name {ruleName} --event-pattern '{"source": ["aws.athena"]}'
   ```

1. Use the AWS CLI `events put-targets` command to attach the Amazon SNS topic target to the rule, as in the following example\.

   ```
   aws events put-targets --rule {ruleName} --targets Id=1,Arn=arn:aws:sns:us-east-1:111111111111:your-sns-topic
   ```

1. Query Athena and observe the target being invoked\. You should receive corresponding emails from the Amazon SNS topic\.
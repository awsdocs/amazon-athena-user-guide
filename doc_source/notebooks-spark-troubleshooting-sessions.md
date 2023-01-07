# Troubleshooting sessions<a name="notebooks-spark-troubleshooting-sessions"></a>

Use the information in this topic to troubleshoot session issues\.

## Session in unhealthy state<a name="notebooks-spark-troubleshooting-sessions-unhealthy"></a>

If you receive the error message Session in unhealthy state\. Please create a new session, terminate your existing session and create a new one\.

## A connection to the notebook server could not be established<a name="notebooks-spark-troubleshooting-sessions-wss-blocked"></a>

When you open a notebook, you may see the following error message:

```
A connection to the notebook server could not be established.  
The notebook will continue trying to reconnect.  
Check your network connection or notebook server configuration.
```

### Cause<a name="notebooks-spark-troubleshooting-sessions-wss-blocked-cause"></a>

When Athena opens a notebook, Athena creates a session and connects to the notebook using a pre\-signed notebook URL\. The connection to the notebook uses the WSS \([WebSocket Secure](https://en.wikipedia.org/wiki/WebSocket)\) protocol\.

The error can occur for the following reasons:
+ A local firewall \(for example, a company\-wide firewall\) is blocking WSS traffic\.
+ Proxy or anti\-virus software on your local computer is blocking the WSS connection\.

### Solution<a name="notebooks-spark-troubleshooting-sessions-wss-blocked-solution"></a>

Assume you have a WSS connection in the `us-east-1` Region like the following:

```
wss://94c2bcdf-66f9-4d17-9da6-7e7338060183.analytics-gateway.us-east-1.amazonaws.com/
api/kernels/33c78c82-b8d2-4631-bd22-1565dc6ec152/channels?session_id=
7f96a3a048ab4917b6376895ea8d7535
```

To resolve the error, use one of the following strategies\.
+ Use wild card pattern syntax to allow list WSS traffic on port `443` across AWS Regions and AWS accounts\.

  ```
  wss://*amazonaws.com
  ```
+ Use wild card pattern syntax to allow list WSS traffic on port `443` in one AWS Region and across AWS accounts in the AWS Region that you specify\. The following example uses `us-east-1`\.

  ```
  wss://*analytics-gateway.us-east-1.amazonaws.com
  ```
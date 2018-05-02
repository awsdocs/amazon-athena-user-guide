# Code Samples<a name="code-samples"></a>

Use examples in this topic as a starting point for writing Athena applications using the AWS SDK for Java\.
+   
**Java Code Samples**  
  +  [Create a Client to Access Athena](#create-a-client-to-access-athena) 
  +   
**Working with Query Executions**  
    +  [Start Query Execution](#start-query-execution) 
    +  [Stop Query Execution](#stop-query-execution) 
    +  [List Query Executions](#list-query-executions) 
  +   
**Working with Named Queries**  
    +  [Create a Named Query](#create-a-named-query) 
    +  [Delete a Named Query](#delete-a-named-query) 
    +  [List Query Executions](#list-query-executions) 

**Note**  
These samples use constants \(for example, `ATHENA_SAMPLE_QUERY`\) for strings, which are defined in an `ExampleConstants` class declaration not shown in this topic\. Replace these constants with your own strings or defined constants\.

## Create a Client to Access Athena<a name="create-a-client-to-access-athena"></a>

```
package com.amazonaws.services.athena.sdksamples;

import com.amazonaws.ClientConfiguration;
import com.amazonaws.auth.InstanceProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.athena.AmazonAthena;
import com.amazonaws.services.athena.AmazonAthenaClientBuilder;

/**
* AthenaClientFactory
* -------------------------------------
* This code shows how to create and configure an Amazon Athena client.
*/
public class AthenaClientFactory
{
  /**
   * AmazonAthenaClientBuilder to build Athena with the following properties:
   * - Set the region of the client
   * - Use the instance profile from the EC2 instance as the credentials provider
   * - Configure the client to increase the execution timeout.
   */
  private final AmazonAthenaClientBuilder builder = AmazonAthenaClientBuilder.standard()
          .withRegion(Regions.US_EAST_1)
          .withCredentials(InstanceProfileCredentialsProvider.getInstance())
          .withClientConfiguration(new ClientConfiguration().withClientExecutionTimeout(ExampleConstants.CLIENT_EXECUTION_TIMEOUT));

  public AmazonAthena createClient()
  {
      return builder.build();
  }
}
```

## Start Query Execution<a name="start-query-execution"></a>

```
package com.amazonaws.services.athena.sdksamples;

import com.amazonaws.services.athena.AmazonAthena;
import com.amazonaws.services.athena.model.ColumnInfo;
import com.amazonaws.services.athena.model.GetQueryExecutionRequest;
import com.amazonaws.services.athena.model.GetQueryExecutionResult;
import com.amazonaws.services.athena.model.GetQueryResultsRequest;
import com.amazonaws.services.athena.model.GetQueryResultsResult;
import com.amazonaws.services.athena.model.QueryExecutionContext;
import com.amazonaws.services.athena.model.QueryExecutionState;
import com.amazonaws.services.athena.model.ResultConfiguration;
import com.amazonaws.services.athena.model.Row;
import com.amazonaws.services.athena.model.StartQueryExecutionRequest;
import com.amazonaws.services.athena.model.StartQueryExecutionResult;

import java.util.List;

/**
* StartQueryExample
* -------------------------------------
* This code shows how to submit a query to Athena for execution, wait till results
* are available, and then process the results.
*/
public class StartQueryExample
{
  public static void main(String[] args) throws InterruptedException
  {
      // Build an AmazonAthena client
      AthenaClientFactory factory = new AthenaClientFactory();
      AmazonAthena client = factory.createClient();

      String queryExecutionId = submitAthenaQuery(client);

      waitForQueryToComplete(client, queryExecutionId);

      processResultRows(client, queryExecutionId);
  }

  /**
   * Submits a sample query to Athena and returns the execution ID of the query.
   */
  private static String submitAthenaQuery(AmazonAthena client)
  {
      // The QueryExecutionContext allows us to set the Database.
      QueryExecutionContext queryExecutionContext = new QueryExecutionContext().withDatabase(ExampleConstants.ATHENA_DEFAULT_DATABASE);

      // The result configuration specifies where the results of the query should go in S3 and encryption options
      ResultConfiguration resultConfiguration = new ResultConfiguration()
              // You can provide encryption options for the output that is written.
              // .withEncryptionConfiguration(encryptionConfiguration)
              .withOutputLocation(ExampleConstants.ATHENA_OUTPUT_BUCKET);

      // Create the StartQueryExecutionRequest to send to Athena which will start the query.
      StartQueryExecutionRequest startQueryExecutionRequest = new StartQueryExecutionRequest()
              .withQueryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
              .withQueryExecutionContext(queryExecutionContext)
              .withResultConfiguration(resultConfiguration);

      StartQueryExecutionResult startQueryExecutionResult = client.startQueryExecution(startQueryExecutionRequest);
      return startQueryExecutionResult.getQueryExecutionId();
  }

  /**
   * Wait for an Athena query to complete, fail or to be cancelled. This is done by polling Athena over an
   * interval of time. If a query fails or is cancelled, then it will throw an exception.
   */

       private static void waitForQueryToComplete(AmazonAthena client, String queryExecutionId) throws InterruptedException
  {
      GetQueryExecutionRequest getQueryExecutionRequest = new GetQueryExecutionRequest()
              .withQueryExecutionId(queryExecutionId);

      GetQueryExecutionResult getQueryExecutionResult = null;
      boolean isQueryStillRunning = true;
      while (isQueryStillRunning) {
          getQueryExecutionResult = client.getQueryExecution(getQueryExecutionRequest);
          String queryState = getQueryExecutionResult.getQueryExecution().getStatus().getState();
          if (queryState.equals(QueryExecutionState.FAILED.toString())) {
              throw new RuntimeException("Query Failed to run with Error Message: " + getQueryExecutionResult.getQueryExecution().getStatus().getStateChangeReason());
          }
          else if (queryState.equals(QueryExecutionState.CANCELLED.toString())) {
              throw new RuntimeException("Query was cancelled.");
          }
          else if (queryState.equals(QueryExecutionState.SUCCEEDED.toString())) {
              isQueryStillRunning = false;
          }
          else {
              // Sleep an amount of time before retrying again.
              Thread.sleep(ExampleConstants.SLEEP_AMOUNT_IN_MS);
          }
          System.out.println("Current Status is: " + queryState);
      }
  }

  /**
   * This code calls Athena and retrieves the results of a query.
   * The query must be in a completed state before the results can be retrieved and
   * paginated. The first row of results are the column headers.
   */
  private static void processResultRows(AmazonAthena client, String queryExecutionId)
  {
      GetQueryResultsRequest getQueryResultsRequest = new GetQueryResultsRequest()
              // Max Results can be set but if its not set,
              // it will choose the maximum page size
              // As of the writing of this code, the maximum value is 1000
              // .withMaxResults(1000)
              .withQueryExecutionId(queryExecutionId);

      GetQueryResultsResult getQueryResultsResult = client.getQueryResults(getQueryResultsRequest);
      List<ColumnInfo> columnInfoList = getQueryResultsResult.getResultSet().getResultSetMetadata().getColumnInfo();

      while (true) {
          List<Row> results = getQueryResultsResult.getResultSet().getRows();
          for (Row row : results) {
              // Process the row. The first row of the first page holds the column names.
              processRow(row, columnInfoList);
          }
          // If nextToken is null, there are no more pages to read. Break out of the loop.
          if (getQueryResultsResult.getNextToken() == null) {
              break;
          }
          getQueryResultsResult = client.getQueryResults(
                  getQueryResultsRequest.withNextToken(getQueryResultsResult.getNextToken()));
      }
  }

  private static void processRow(Row row, List<ColumnInfo> columnInfoList)
  {
      for (int i = 0; i < columnInfoList.size(); ++i) {
          switch (columnInfoList.get(i).getType()) {
              case "varchar":
                  // Convert and Process as String
                  break;
              case "tinyint":
                  // Convert and Process as tinyint
                  break;
              case "smallint":
                  // Convert and Process as smallint
                  break;
              case "integer":
                  // Convert and Process as integer
                  break;
              case "bigint":
                  // Convert and Process as bigint
                  break;
              case "double":
                  // Convert and Process as double
                  break;
              case "boolean":
                  // Convert and Process as boolean
                  break;
              case "date":
                  // Convert and Process as date
                  break;
              case "timestamp":
                  // Convert and Process as timestamp
                  break;
              default:
                  throw new RuntimeException("Unexpected Type is not expected" + columnInfoList.get(i).getType());
          }
      }
  }
}
```

## Stop Query Execution<a name="stop-query-execution"></a>

```
package com.amazonaws.services.athena.sdksamples;

import com.amazonaws.services.athena.AmazonAthena;
import com.amazonaws.services.athena.model.GetQueryExecutionRequest;
import com.amazonaws.services.athena.model.GetQueryExecutionResult;
import com.amazonaws.services.athena.model.QueryExecutionContext;
import com.amazonaws.services.athena.model.ResultConfiguration;
import com.amazonaws.services.athena.model.StartQueryExecutionRequest;
import com.amazonaws.services.athena.model.StartQueryExecutionResult;
import com.amazonaws.services.athena.model.StopQueryExecutionRequest;
import com.amazonaws.services.athena.model.StopQueryExecutionResult;

/**
* StopQueryExecutionExample
* -------------------------------------
* This code runs an example query, immediately stops the query, and checks the status of the query to
* ensure that it was cancelled.
*/
public class StopQueryExecutionExample
{
  public static void main(String[] args) throws Exception
  {
      // Build an Athena client
      AthenaClientFactory factory = new AthenaClientFactory();
      AmazonAthena client = factory.createClient();

      String sampleQueryExecutionId = getExecutionId(client);

      // Submit the stop query Request
      StopQueryExecutionRequest stopQueryExecutionRequest = new StopQueryExecutionRequest()
              .withQueryExecutionId(sampleQueryExecutionId);

      StopQueryExecutionResult stopQueryExecutionResult = client.stopQueryExecution(stopQueryExecutionRequest);

      // Ensure that the query was stopped
      GetQueryExecutionRequest getQueryExecutionRequest = new GetQueryExecutionRequest()
              .withQueryExecutionId(sampleQueryExecutionId);

      GetQueryExecutionResult getQueryExecutionResult = client.getQueryExecution(getQueryExecutionRequest);
      if (getQueryExecutionResult.getQueryExecution().getStatus().getState().equals(ExampleConstants.QUERY_STATE_CANCELLED)) {
          // Query was cancelled.
          System.out.println("Query has been cancelled");
      }
  }

  /**
   * Submits an example query and returns a query execution ID of a running query to stop.
   */
  public static String getExecutionId(AmazonAthena client)
  {
      QueryExecutionContext queryExecutionContext = new QueryExecutionContext().withDatabase(ExampleConstants.ATHENA_DEFAULT_DATABASE);

      ResultConfiguration resultConfiguration = new ResultConfiguration()
              .withOutputLocation(ExampleConstants.ATHENA_OUTPUT_BUCKET);

      StartQueryExecutionRequest startQueryExecutionRequest = new StartQueryExecutionRequest()
              .withQueryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
              .withQueryExecutionContext(queryExecutionContext)
              .withResultConfiguration(resultConfiguration);

      StartQueryExecutionResult startQueryExecutionResult = client.startQueryExecution(startQueryExecutionRequest);

      return startQueryExecutionResult.getQueryExecutionId();
  }
}
```

## List Query Executions<a name="list-query-executions"></a>

```
package com.amazonaws.services.athena.sdksamples;

import com.amazonaws.services.athena.AmazonAthena;
import com.amazonaws.services.athena.model.ListQueryExecutionsRequest;
import com.amazonaws.services.athena.model.ListQueryExecutionsResult;

import java.util.List;

/**
* ListQueryExecutionsExample
* -------------------------------------
* This code shows how to obtain a list of query execution IDs.
*/
public class ListQueryExecutionsExample
{
  public static void main(String[] args) throws Exception
  {
      // Build an Athena client
      AthenaClientFactory factory = new AthenaClientFactory();
      AmazonAthena client = factory.createClient();

      // Build the request
      ListQueryExecutionsRequest listQueryExecutionsRequest = new ListQueryExecutionsRequest();

      // Get the list results.
      ListQueryExecutionsResult listQueryExecutionsResult = client.listQueryExecutions(listQueryExecutionsRequest);

      // Process the results.
      boolean hasMoreResults = true;
      while (hasMoreResults) {
          List<String> queryExecutionIds = listQueryExecutionsResult.getQueryExecutionIds();
          // process queryExecutionIds.

          //If nextToken is not null, then there are more results. Get the next page of results.
          if (listQueryExecutionsResult.getNextToken() != null) {
              listQueryExecutionsResult = client.listQueryExecutions(
                      listQueryExecutionsRequest.withNextToken(listQueryExecutionsResult.getNextToken()));
          }
          else {
              hasMoreResults = false;
          }
      }
  }
}
```

## Create a Named Query<a name="create-a-named-query"></a>

```
package com.amazonaws.services.athena.sdksamples;

import com.amazonaws.services.athena.AmazonAthena;
import com.amazonaws.services.athena.model.CreateNamedQueryRequest;
import com.amazonaws.services.athena.model.CreateNamedQueryResult;

/**
* CreateNamedQueryExample
* -------------------------------------
* This code shows how to create a named query.
*/
public class CreateNamedQueryExample
{
  public static void main(String[] args) throws Exception
  {
      // Build an Athena client
      AthenaClientFactory factory = new AthenaClientFactory();
      AmazonAthena client = factory.createClient();

      // Create the named query request.
      CreateNamedQueryRequest createNamedQueryRequest = new CreateNamedQueryRequest()
              .withDatabase(ExampleConstants.ATHENA_DEFAULT_DATABASE)
              .withQueryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
              .withDescription("Sample Description")
              .withName("SampleQuery");

      // Call Athena to create the named query. If it fails, an exception is thrown.
      CreateNamedQueryResult createNamedQueryResult = client.createNamedQuery(createNamedQueryRequest);
  }
}
```

## Delete a Named Query<a name="delete-a-named-query"></a>

```
package com.amazonaws.services.athena.sdksamples;

import com.amazonaws.services.athena.AmazonAthena;
import com.amazonaws.services.athena.model.CreateNamedQueryRequest;
import com.amazonaws.services.athena.model.CreateNamedQueryResult;
import com.amazonaws.services.athena.model.DeleteNamedQueryRequest;
import com.amazonaws.services.athena.model.DeleteNamedQueryResult;

/**
 * DeleteNamedQueryExample
 * -------------------------------------
 * This code shows how to delete a named query by using the named query ID.
 */
public class DeleteNamedQueryExample
{
    private static String getNamedQueryId(AmazonAthena athenaClient)
    {
        // Create the NameQuery Request.
        CreateNamedQueryRequest createNamedQueryRequest = new CreateNamedQueryRequest()
                .withDatabase(ExampleConstants.ATHENA_DEFAULT_DATABASE)
                .withQueryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
                .withName("SampleQueryName")
                .withDescription("Sample Description");

        // Create the named query. If it fails, an exception is thrown.
        CreateNamedQueryResult createNamedQueryResult = athenaClient.createNamedQuery(createNamedQueryRequest);
        return createNamedQueryResult.getNamedQueryId();
    }

    public static void main(String[] args) throws Exception
    {
        // Build an Athena client
        AthenaClientFactory factory = new AthenaClientFactory();
        AmazonAthena client = factory.createClient();

        String sampleNamedQueryId = getNamedQueryId(client);

        // Create the delete named query request
        DeleteNamedQueryRequest deleteNamedQueryRequest = new DeleteNamedQueryRequest()
                .withNamedQueryId(sampleNamedQueryId);

        // Delete the named query
        DeleteNamedQueryResult deleteNamedQueryResult = client.deleteNamedQuery(deleteNamedQueryRequest);
    }
}
```

## List Named Queries<a name="list-named-queries"></a>

```
package com.amazonaws.services.athena.sdksamples;

import com.amazonaws.services.athena.AmazonAthena;
import com.amazonaws.services.athena.model.ListNamedQueriesRequest;
import com.amazonaws.services.athena.model.ListNamedQueriesResult;

import java.util.List;

/**
 * ListNamedQueryExample
 * -------------------------------------
 * This code shows how to obtain a list of named query IDs.
 */
public class ListNamedQueryExample
{
    public static void main(String[] args) throws Exception
    {
        // Build an Athena client
        AthenaClientFactory factory = new AthenaClientFactory();
        AmazonAthena client = factory.createClient();

        // Build the request
        ListNamedQueriesRequest listNamedQueriesRequest = new ListNamedQueriesRequest();

        // Get the list results.
        ListNamedQueriesResult listNamedQueriesResult = client.listNamedQueries(listNamedQueriesRequest);

        // Process the results.
        boolean hasMoreResults = true;

        while (hasMoreResults) {
            List<String> namedQueryIds = listNamedQueriesResult.getNamedQueryIds();
            // process named query IDs

            // If nextToken is not null,  there are more results. Get the next page of results.
            if (listNamedQueriesResult.getNextToken() != null) {
                listNamedQueriesResult = client.listNamedQueries(
                        listNamedQueriesRequest.withNextToken(listNamedQueriesResult.getNextToken()));
            }
            else {
                hasMoreResults = false;
            }
        }
    }
}
```
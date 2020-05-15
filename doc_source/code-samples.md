# Code Samples<a name="code-samples"></a>

Use the examples in this topic as a starting point for writing Athena applications using the SDK for Java 2\.x\. For more information about running the Java code examples, see the [Amazon Athena Java Readme](https://github.com/awsdocs/aws-doc-sdk-examples/tree/master/javav2/example_code/athena) on the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples) on GitHub\.
+   
**Java Code Examples**  
  +  [Constants](#constants) 
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
These samples use constants \(for example, `ATHENA_SAMPLE_QUERY`\) for strings, which are defined in an `ExampleConstants.java` class declaration\. Replace these constants with your own strings or defined constants\.

## Constants<a name="constants"></a>

The `ExampleConstants.java` class demonstrates how to query a table created by the [Getting Started](getting-started.md) tutorial in Athena\.

```
package aws.example.athena;

public class ExampleConstants {

    public static final int CLIENT_EXECUTION_TIMEOUT = 100000;
    public static final String ATHENA_OUTPUT_BUCKET = "s3://mybucket"; //change the bucket name to match your environment
    // This example demonstrates how to query a table with a CSV  For information, see
    //https://docs.aws.amazon.com/athena/latest/ug/work-with-data.html
    public static final String ATHENA_SAMPLE_QUERY = "SELECT * FROM mydb;"; //change the Query statement to match your environment
    public static final long SLEEP_AMOUNT_IN_MS = 1000;
    public static final String ATHENA_DEFAULT_DATABASE = "mydatabase"; //Change the database to match your database

}
```

## Create a Client to Access Athena<a name="create-a-client-to-access-athena"></a>

The `AthenaClientFactory.java` class shows how to create and configure an Amazon Athena client\.

```
package aws.example.athena;

import software.amazon.awssdk.auth.credentials.InstanceProfileCredentialsProvider;
import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.athena.AthenaClient;
import software.amazon.awssdk.services.athena.AthenaClientBuilder;

/**
 * AthenaClientFactory
 * -------------------------------------
 * This code shows how to create and configure an Amazon Athena client.
 */
public class AthenaClientFactory {
    /**
     * AthenaClientClientBuilder to build Athena with the following properties:
     * - Set the region of the client
     * - Use the instance profile from the EC2 instance as the credentials provider
     * - Configure the client to increase the execution timeout.
     */
    private final AthenaClientBuilder builder = AthenaClient.builder()
            .region(Region.US_WEST_2)
            .credentialsProvider(InstanceProfileCredentialsProvider.create());

    public AthenaClient createClient() {
        return builder.build();
    }
}
```

## Start Query Execution<a name="start-query-execution"></a>

The `StartQueryExample` shows how to submit a query to Athena for execution, wait until the results become available, and then process the results\.

```
package aws.example.athena;

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.athena.AthenaClient;
import software.amazon.awssdk.services.athena.model.QueryExecutionContext;
import software.amazon.awssdk.services.athena.model.ResultConfiguration;
import software.amazon.awssdk.services.athena.model.StartQueryExecutionRequest;
import software.amazon.awssdk.services.athena.model.StartQueryExecutionResponse;
import software.amazon.awssdk.services.athena.model.AthenaException;
import software.amazon.awssdk.services.athena.model.GetQueryExecutionRequest;
import software.amazon.awssdk.services.athena.model.GetQueryExecutionResponse;
import software.amazon.awssdk.services.athena.model.QueryExecutionState;
import software.amazon.awssdk.services.athena.model.GetQueryResultsRequest;
import software.amazon.awssdk.services.athena.model.GetQueryResultsResponse;
import software.amazon.awssdk.services.athena.model.ColumnInfo;
import software.amazon.awssdk.services.athena.model.Row;
import software.amazon.awssdk.services.athena.model.Datum;
import software.amazon.awssdk.services.athena.paginators.GetQueryResultsIterable;
import java.util.List;

/**
 * StartQueryExample
 * -------------------------------------
 * This code shows how to submit a query to Athena for execution, wait till results
 * are available, and then process the results.
 */
public class StartQueryExample {
    public static void main(String[] args) throws InterruptedException {

        // Build an Athena client
        AthenaClient athenaClient = AthenaClient.builder()
                .region(Region.US_WEST_2)
                .build();

        String queryExecutionId = submitAthenaQuery(athenaClient);

        waitForQueryToComplete(athenaClient, queryExecutionId);

        processResultRows(athenaClient, queryExecutionId);
    }

    /**
     * Submits a sample query to Athena and returns the execution ID of the query.
     */
    public static String submitAthenaQuery(AthenaClient athenaClient) {

        try {

            // The QueryExecutionContext allows us to set the Database.
            QueryExecutionContext queryExecutionContext = QueryExecutionContext.builder()
                .database(ExampleConstants.ATHENA_DEFAULT_DATABASE).build();

            // The result configuration specifies where the results of the query should go in S3 and encryption options
            ResultConfiguration resultConfiguration = ResultConfiguration.builder()
                // You can provide encryption options for the output that is written.
                // .withEncryptionConfiguration(encryptionConfiguration)
                .outputLocation(ExampleConstants.ATHENA_OUTPUT_BUCKET).build();

            // Create the StartQueryExecutionRequest to send to Athena which will start the query.
            StartQueryExecutionRequest startQueryExecutionRequest = StartQueryExecutionRequest.builder()
                .queryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
                .queryExecutionContext(queryExecutionContext)
                .resultConfiguration(resultConfiguration).build();

            StartQueryExecutionResponse startQueryExecutionResponse = athenaClient.startQueryExecution(startQueryExecutionRequest);
            return startQueryExecutionResponse.queryExecutionId();

        } catch (AthenaException e) {
            e.printStackTrace();
            System.exit(1);
        }
        return "";
    }

    /**
     * Wait for an Athena query to complete, fail or to be cancelled. This is done by polling Athena over an
     * interval of time. If a query fails or is cancelled, then it will throw an exception.
     */

    public static void waitForQueryToComplete(AthenaClient athenaClient, String queryExecutionId) throws InterruptedException {
        GetQueryExecutionRequest getQueryExecutionRequest = GetQueryExecutionRequest.builder()
                .queryExecutionId(queryExecutionId).build();

        GetQueryExecutionResponse getQueryExecutionResponse;
        boolean isQueryStillRunning = true;
        while (isQueryStillRunning) {
            getQueryExecutionResponse = athenaClient.getQueryExecution(getQueryExecutionRequest);
            String queryState = getQueryExecutionResponse.queryExecution().status().state().toString();
            if (queryState.equals(QueryExecutionState.FAILED.toString())) {
                throw new RuntimeException("Query Failed to run with Error Message: " + getQueryExecutionResponse
                        .queryExecution().status().stateChangeReason());
            } else if (queryState.equals(QueryExecutionState.CANCELLED.toString())) {
                throw new RuntimeException("Query was cancelled.");
            } else if (queryState.equals(QueryExecutionState.SUCCEEDED.toString())) {
                isQueryStillRunning = false;
            } else {
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
    public static void processResultRows(AthenaClient athenaClient, String queryExecutionId) {

       try {

            GetQueryResultsRequest getQueryResultsRequest = GetQueryResultsRequest.builder()
                // Max Results can be set but if its not set,
                // it will choose the maximum page size
                // As of the writing of this code, the maximum value is 1000
                // .withMaxResults(1000)
                .queryExecutionId(queryExecutionId).build();

            GetQueryResultsIterable getQueryResultsResults = athenaClient.getQueryResultsPaginator(getQueryResultsRequest);

            for (GetQueryResultsResponse result : getQueryResultsResults) {
                List<ColumnInfo> columnInfoList = result.resultSet().resultSetMetadata().columnInfo();
                List<Row> results = result.resultSet().rows();
                processRow(results, columnInfoList);
            }

        } catch (AthenaException e) {
           e.printStackTrace();
           System.exit(1);
       }
    }

    private static void processRow(List<Row> row, List<ColumnInfo> columnInfoList) {

        //Write out the data
        for (Row myRow : row) {
            List<Datum> allData = myRow.data();
            for (Datum data : allData) {
                System.out.println("The value of the column is "+data.varCharValue());
            }
        }
    }
}
```

## Stop Query Execution<a name="stop-query-execution"></a>

The `StopQueryExecutionExample` runs an example query, immediately stops the query, and checks the status of the query to ensure that it was canceled\.

```
package aws.example.athena;

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.athena.AthenaClient;
import software.amazon.awssdk.services.athena.model.StopQueryExecutionRequest;
import software.amazon.awssdk.services.athena.model.StopQueryExecutionResponse;
import software.amazon.awssdk.services.athena.model.GetQueryExecutionRequest;
import software.amazon.awssdk.services.athena.model.GetQueryExecutionResponse;
import software.amazon.awssdk.services.athena.model.QueryExecutionState;
import software.amazon.awssdk.services.athena.model.AthenaException;
import software.amazon.awssdk.services.athena.model.QueryExecutionContext;
import software.amazon.awssdk.services.athena.model.ResultConfiguration;
import software.amazon.awssdk.services.athena.model.StartQueryExecutionRequest;
import software.amazon.awssdk.services.athena.model.StartQueryExecutionResponse;

/**
 * StopQueryExecutionExample
 * -------------------------------------
 * This code runs an example query, immediately stops the query, and checks the status of the query to
 * ensure that it was cancelled.
 */
public class StopQueryExecutionExample {
    public static void main(String[] args) throws Exception {

        // Build an Athena client
        AthenaClient athenaClient = AthenaClient.builder()
                .region(Region.US_WEST_2)
                .build();

        String sampleQueryExecutionId = submitAthenaQuery(athenaClient);
        stopAthenaQuery(athenaClient, sampleQueryExecutionId);
    }

    public static void stopAthenaQuery(AthenaClient athenaClient, String sampleQueryExecutionId){

       try {
            // Submit the stop query Request
            StopQueryExecutionRequest stopQueryExecutionRequest = StopQueryExecutionRequest.builder()
                .queryExecutionId(sampleQueryExecutionId).build();

            StopQueryExecutionResponse stopQueryExecutionResponse = athenaClient.stopQueryExecution(stopQueryExecutionRequest);

            // Ensure that the query was stopped
            GetQueryExecutionRequest getQueryExecutionRequest = GetQueryExecutionRequest.builder()
                .queryExecutionId(sampleQueryExecutionId).build();

            GetQueryExecutionResponse getQueryExecutionResponse = athenaClient.getQueryExecution(getQueryExecutionRequest);
            if (getQueryExecutionResponse.queryExecution()
                .status()
                .state()
                .equals(QueryExecutionState.CANCELLED)) {

                // Query was cancelled.
                System.out.println("Query has been cancelled");
        }

       } catch (AthenaException e) {
        e.printStackTrace();
        System.exit(1);
        }
    }

    /**
     * Submits an example query and returns a query execution ID of a running query to stop.
     */
    public static String submitAthenaQuery(AthenaClient athenaClient) {

        try {
            QueryExecutionContext queryExecutionContext = QueryExecutionContext.builder()
                .database(ExampleConstants.ATHENA_DEFAULT_DATABASE).build();

            ResultConfiguration resultConfiguration = ResultConfiguration.builder()
                .outputLocation(ExampleConstants.ATHENA_OUTPUT_BUCKET).build();

            StartQueryExecutionRequest startQueryExecutionRequest = StartQueryExecutionRequest.builder()
                .queryExecutionContext(queryExecutionContext)
                .queryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
                .resultConfiguration(resultConfiguration).build();

            StartQueryExecutionResponse startQueryExecutionResponse = athenaClient.startQueryExecution(startQueryExecutionRequest);

            return startQueryExecutionResponse.queryExecutionId();

        } catch (AthenaException e) {
            e.printStackTrace();
         System.exit(1);
        }
        return null;

    }
}
```

## List Query Executions<a name="list-query-executions"></a>

The `ListQueryExecutionsExample` shows how to obtain a list of query execution IDs\.

```
package aws.example.athena;

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.athena.AthenaClient;
import software.amazon.awssdk.services.athena.model.AthenaException;
import software.amazon.awssdk.services.athena.model.ListQueryExecutionsRequest;
import software.amazon.awssdk.services.athena.model.ListQueryExecutionsResponse;
import software.amazon.awssdk.services.athena.paginators.ListQueryExecutionsIterable;
import java.util.List;

/**
 * ListQueryExecutionsExample
 * -------------------------------------
 * This code shows how to obtain a list of query execution IDs.
 */
public class ListQueryExecutionsExample {

    public static void main(String[] args) throws Exception {

        // Build an Athena client
        AthenaClient athenaClient = AthenaClient.builder()
                .region(Region.US_WEST_2)
                .build();

        listQueryIds(athenaClient);
    }

    public static void listQueryIds(AthenaClient athenaClient) {

       try {
           // Build the request
            ListQueryExecutionsRequest listQueryExecutionsRequest = ListQueryExecutionsRequest.builder().build();

            // Get the list results.
            ListQueryExecutionsIterable listQueryExecutionResponses = athenaClient.listQueryExecutionsPaginator(listQueryExecutionsRequest);

            for (ListQueryExecutionsResponse listQueryExecutionResponse : listQueryExecutionResponses) {
                List<String> queryExecutionIds = listQueryExecutionResponse.queryExecutionIds();
                System.out.println("\n" +queryExecutionIds);
        }
    } catch (AthenaException e) {
        e.printStackTrace();
        System.exit(1);
    }
  }
}
```

## Create a Named Query<a name="create-a-named-query"></a>

The `CreateNamedQueryExample` shows how to create a named query\.

```
package aws.example.athena;

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.athena.AthenaClient;
import software.amazon.awssdk.services.athena.model.AthenaException;
import software.amazon.awssdk.services.athena.model.CreateNamedQueryRequest;
import software.amazon.awssdk.services.athena.model.CreateNamedQueryResponse;

/**
 * CreateNamedQueryExample
 * -------------------------------------
 * This code shows how to create a named query.
 */
public class CreateNamedQueryExample {
    public static void main(String[] args) throws Exception {

        final String USAGE = "\n" +
                "Usage:\n" +
                "    CreateNamedQueryExample <name>\n\n" +
                "Where:\n" +
                "    name - the name of the query \n\n" +
                "Example:\n" +
                "    DescribeTable SampleQuery\n";

        if (args.length < 1) {
            System.out.println(USAGE);
            System.exit(1);
        }

        /* Read the name from command args */
        String name = args[0];

        // Build an Athena client
       AthenaClient athenaClient = AthenaClient.builder()
                .region(Region.US_WEST_2)
                .build();

        createNamedQuery(athenaClient, name);
    }

    public static void createNamedQuery(AthenaClient athenaClient, String name) {

        try {
            // Create the named query request.
            CreateNamedQueryRequest createNamedQueryRequest = CreateNamedQueryRequest.builder()
                    .database(ExampleConstants.ATHENA_DEFAULT_DATABASE)
                    .queryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
                    .description("Sample Description")
                    .name(name)
                    .build();

            // Call Athena to create the named query. If it fails, an exception is thrown.
            CreateNamedQueryResponse createNamedQueryResult = athenaClient.createNamedQuery(createNamedQueryRequest);
            System.out.println("Done");
        } catch (AthenaException e) {
            e.printStackTrace();
            System.exit(1);
        }
    }
}
```

## Delete a Named Query<a name="delete-a-named-query"></a>

The `DeleteNamedQueryExample` shows how to delete a named query by using the named query ID\.

```
package aws.example.athena;

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.athena.AthenaClient;
import software.amazon.awssdk.services.athena.model.DeleteNamedQueryRequest;
import software.amazon.awssdk.services.athena.model.DeleteNamedQueryResponse;
import software.amazon.awssdk.services.athena.model.AthenaException;
import software.amazon.awssdk.services.athena.model.CreateNamedQueryRequest;
import software.amazon.awssdk.services.athena.model.CreateNamedQueryResponse;

/**
 * DeleteNamedQueryExample
 * -------------------------------------
 * This code shows how to delete a named query by using the named query ID.
 */
public class DeleteNamedQueryExample {

    public static void main(String[] args) {

        final String USAGE = "\n" +
                "Usage:\n" +
                "    DeleteNamedQueryExample <name>\n\n" +
                "Where:\n" +
                "    name - the name of the query \n\n" +
                "Example:\n" +
                "    DeleteNamedQueryExample SampleQuery\n";

        if (args.length < 1) {
            System.out.println(USAGE);
            System.exit(1);
        }

        /* Read the name from command args */
        String name = args[0];

        // Build an Athena client
        AthenaClient athenaClient = AthenaClient.builder()
                .region(Region.US_WEST_2)
                .build();
        String sampleNamedQueryId = getNamedQueryId(athenaClient, name);
        deleteQueryName(athenaClient, sampleNamedQueryId);
         }

   public static void deleteQueryName(AthenaClient athenaClient, String sampleNamedQueryId) {

       try {
            // Create the delete named query request
            DeleteNamedQueryRequest deleteNamedQueryRequest = DeleteNamedQueryRequest.builder()
               .namedQueryId(sampleNamedQueryId).build();

            // Delete the named query
            DeleteNamedQueryResponse deleteNamedQueryResponse = athenaClient.deleteNamedQuery(deleteNamedQueryRequest);
       } catch (AthenaException e) {
           e.printStackTrace();
           System.exit(1);
       }
     }

    public static String getNamedQueryId(AthenaClient athenaClient, String name) {
        try {
            // Create the NameQuery Request.
            CreateNamedQueryRequest createNamedQueryRequest = CreateNamedQueryRequest.builder()
                .database(ExampleConstants.ATHENA_DEFAULT_DATABASE)
                .queryString(ExampleConstants.ATHENA_SAMPLE_QUERY)
                .name(name)
                .description("Sample Description").build();

            // Create the named query. If it fails, an exception is thrown.
            CreateNamedQueryResponse createNamedQueryResponse = athenaClient.createNamedQuery(createNamedQueryRequest);
            return createNamedQueryResponse.namedQueryId();

    } catch (AthenaException e) {
        e.printStackTrace();
        System.exit(1);
    }
        return null;
    }
}
```

## List Named Queries<a name="list-named-queries"></a>

The `ListNamedQueryExample` shows how to obtain a list of named query IDs\.

```
package aws.example.athena;

import software.amazon.awssdk.regions.Region;
import software.amazon.awssdk.services.athena.AthenaClient;
import software.amazon.awssdk.services.athena.model.AthenaException;
import software.amazon.awssdk.services.athena.model.ListNamedQueriesRequest;
import software.amazon.awssdk.services.athena.model.ListNamedQueriesResponse;
import software.amazon.awssdk.services.athena.paginators.ListNamedQueriesIterable;

import java.util.List;

/**
 * ListNamedQueryExample
 * -------------------------------------
 * This code shows how to obtain a list of named query IDs.
 */
public class ListNamedQueryExample {

    public static void main(String[] args) throws Exception {
        // Build an Athena client
        AthenaClient athenaClient = AthenaClient.builder()
                .region(Region.US_WEST_2)
                .build();

        listNamedQueries(athenaClient) ;
    }

    public static void listNamedQueries(AthenaClient athenaClient) {

       try{

           // Build the request
           ListNamedQueriesRequest listNamedQueriesRequest = ListNamedQueriesRequest.builder().build();

           // Get the list results.
            ListNamedQueriesIterable listNamedQueriesResponses = athenaClient.listNamedQueriesPaginator(listNamedQueriesRequest);

            // Process the results.
            for (ListNamedQueriesResponse listNamedQueriesResponse : listNamedQueriesResponses) {
                List<String> namedQueryIds = listNamedQueriesResponse.namedQueryIds();
                // process named query IDs
                System.out.println(namedQueryIds);
            }

       } catch (AthenaException e) {
           e.printStackTrace();
           System.exit(1);
       }
    }
}
```
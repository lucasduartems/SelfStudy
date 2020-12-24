####  Acessing a single-column table in DynamoDB to store user email addresses

##### *Gradle* dependencies

```gradle
// AWS
implementation('com.amazonaws:aws-java-sdk-core:1.11.479')
implementation('com.amazonaws:aws-java-sdk-dynamodb:1.11.559')
```

##### Main code

```java
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.*;
import org.apache.http.HttpStatus;

import java.util.HashMap;
import java.util.List;
import java.util.stream.Collectors;

public class DynamoDBConnection {

    // ----- AWS Credentials (obtained from the AWS Console > My Security Credentials) -----
    private static final String AWS_ACCESS_KEY     = "##### REDACTED #####";
    private static final String AWS_SECRET_KEY     = "##### REDACTED #####";

    // ----- DynamoDB endpoint and region -----
    private static final String DYNAMO_DB_ENDPOINT = "dynamodb.us-east-2.amazonaws.com";
    private static final Regions AWS_REGION        = Regions.US_EAST_2;

    // ----- Name of the DynamoDB single-column table -----
    private static final String TABLE_NAME         = "UserEmails";

    // ----- Name of the column -----
    private static final String TABLE_KEY          = "userEmail";

    // ----- HTTP OK status code -----
    private static final int OK                    = HttpStatus.SC_OK;  // 200

    private AmazonDynamoDB dynamoDBClient;

    public DynamoDBConnection() {
        // Instantiating the DynamoDB client
        dynamoDBClient = AmazonDynamoDBClientBuilder.standard()
                                                    .withCredentials(
                                                        new AWSStaticCredentialsProvider(
                                                            new BasicAWSCredentials(
                                                                AWS_ACCESS_KEY,
                                                                AWS_SECRET_KEY
                                                            )
                                                        )
                                                    )
                                                    .withRegion(AWS_REGION)
                                                    .build();
    }

    /**
     * Returns {@code null} if users listing fails. Otherwise, returns the {@link List} of user emails.
     */
    public List<String> listUsers() {
        ScanResult scanResult;
        int scanResultHttpStatusCode;

        try {
            scanResult               = dynamoDBClient.scan(new ScanRequest(TABLE_NAME));
            scanResultHttpStatusCode = scanResult.getSdkHttpMetadata().getHttpStatusCode();
        }
        catch (Exception e) {
            scanResult               = null;
            scanResultHttpStatusCode = -1;
        }

        System.out.println(
            scanResultHttpStatusCode == OK ? "DynamoDB users listing returned code 200 [OK]"
                                           : "DynamoDB users listing failed"
        );

        return scanResultHttpStatusCode != OK ? null : scanResult.getItems()
                                                                 .stream()
                                                                 .map(item -> item.get(TABLE_KEY).getS())
                                                                 .collect(Collectors.toList());
    }

    /**
     * Returns {@code true} if the user is added successfully, {@code false} otherwise.
     */
    public boolean addNewUser(String userEmail) {
        PutItemResult putItemResult;
        int putHttpStatusCode;

        try {
            putItemResult = dynamoDBClient.putItem(
                TABLE_NAME,
                new HashMap<String, AttributeValue>() {{ put(TABLE_KEY, new AttributeValue(userEmail)); }}
            );
            putHttpStatusCode = putItemResult.getSdkHttpMetadata().getHttpStatusCode();
        }
        catch (Exception e) {
            putItemResult     = null;
            putHttpStatusCode = -1;
        }

        System.out.println(
            putHttpStatusCode == OK ? "DynamoDB user insertion returned code 200 [OK] for " + userEmail
                                    : "DynamoDB user insertion failed for " + userEmail
        );

        return putHttpStatusCode == OK;
    }

    /**
     * Returns {@code true} if the user is deleted successfully, {@code false} otherwise.
     */
    public boolean deleteUser(String userEmail) {
        DeleteItemResult deleteItemResult;
        int deleteHttpStatusCode;

        try {
            deleteItemResult = dynamoDBClient.deleteItem(
                TABLE_NAME,
                new HashMap<String, AttributeValue>() {{ put(TABLE_KEY, new AttributeValue(userEmail)); }}
            );
            deleteHttpStatusCode = deleteItemResult.getSdkHttpMetadata().getHttpStatusCode();
        }
        catch (Exception e) {
            deleteItemResult     = null;
            deleteHttpStatusCode = -1;
        }

        System.out.println(
            deleteHttpStatusCode == OK ? "DynamoDB user deletion returned code 200 [OK] for " + userEmail
                                       : "DynamoDB user deletion failed for " + userEmail
        );

        return deleteHttpStatusCode == OK;
    }

    public static void main(String[] args) {
        DynamoDBConnection dynamoDBConnection = new DynamoDBConnection();

        // List table items
        System.out.println("LIST: " + dynamoDBConnection.listUsers());

        // Add 2 items and list table items again
        dynamoDBConnection.addNewUser("mail@example.com");
        dynamoDBConnection.addNewUser("mail2@example.com");
        System.out.println("LIST: " + dynamoDBConnection.listUsers());

        // Remove item and list table items again
        dynamoDBConnection.deleteUser("mail@example.com");
        System.out.println("LIST: " + dynamoDBConnection.listUsers());
    }

}
```

##### Output

```
DynamoDB users listing returned code 200 [OK]
LIST: []
DynamoDB user insertion returned code 200 [OK] for mail@example.com
DynamoDB user insertion returned code 200 [OK] for mail2@example.com
DynamoDB users listing returned code 200 [OK]
LIST: [mail2@example.com, mail@example.com]
DynamoDB user deletion returned code 200 [OK] for mail@example.com
DynamoDB users listing returned code 200 [OK]
LIST: [mail2@example.com]
```

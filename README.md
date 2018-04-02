# Smartsheet SDK for Java [![Build Status](https://travis-ci.org/smartsheet-platform/smartsheet-java-sdk.svg?branch=master)](https://travis-ci.org/smartsheet-platform/smartsheet-java-sdk) [![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.smartsheet/smartsheet-sdk-java/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.smartsheet/smartsheet-sdk-java/)

This is a Java SDK to simplify connecting to [Smartsheet API](http://www.smartsheet.com/developers/api-documentation) in Java applications.

## System Requirements

The SDK supports Java version 1.6 or later.

## Installation
There are three different ways to install the SDK. Select the one that fits your environment best:

* [Install By Using Maven](#install-by-using-maven)
* [Install By Downloading the Jar File](#install-by-downloading-the-jar-file)
* [Install By Compiling Directly From Source](#install-by-compiling-directly-from-source)

### Install By Using Maven
Add the SDK as a dependency in your project.

```xml
<dependency>
  <groupId>com.smartsheet</groupId>
  <artifactId>smartsheet-sdk-java</artifactId>
  <version>2.2.3</version>
</dependency>
```

### Install By Downloading the Jar File
<!--* [The SDK packaged in a jar with Dependencies](https://oss.sonatype.org/service/local/artifact/maven/redirect?r=releases&g=com.smartsheet&a=smartsheet-sdk-java&v=LATEST) built in.-->
[SDK packaged as a jar](https://oss.sonatype.org/service/local/artifact/maven/redirect?r=releases&g=com.smartsheet&a=smartsheet-sdk-java&v=LATEST). This jar requires that all of the following dependencies are manually added to the path:

    Apache HttpComponents 4.5
    Simple Logging Facade for Java 1.7.12
    Jackson FasterXML 2.6.2
    Jackson Core 2.6.2

You can also navigate to the [Sonatype Library Page](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22smartsheet-sdk-java%22) instead of directly downloading the Jar file.

### Install By Compiling Directly From Source
The source code for the jar can be downloaded from Github and then compiled. This can be accomplished using [git](http://git-scm.com/) and [maven](http://maven.apache.org/) with the following 3 steps.

```bash
git clone https://github.com/smartsheet-platform/smartsheet-java-sdk.git
cd smartsheet-java-sdk
mvn package
```

## Example Usage
To call the API, you will need an *access token*, which looks something like this example: ll352u9jujauoqz4gstvsae05. You can find the access token in the UI at Account > Personal Settings > API Access.

The following is a brief sample that shows you how to:

* Initialize the client
* List all sheets
* Load one sheet

To initialize the client, you'll need to include the appropriate **import** directives in your code. For example, the code examples in this section require the following **import** directives:

```java
import com.smartsheet.api.*;
import com.smartsheet.api.models.*;
import com.smartsheet.api.oauth.*;
import java.io.FileInputStream;
import java.util.*;
```

```java
// Initialize client
String accessToken = "ll352u9jujauoqz4gstvsae05";

Smartsheet smartsheet = SmartsheetFactory.createDefaultClient(accessToken);

// List all sheets
PagedResult<Sheet> sheets = smartsheet.sheetResources().listSheets(
    null,           // EnumSet<SourceInclusion> includes
    null,           // PaginationParameters
    null            // Date modifiedSince
);

System.out.println("Found " + sheets.getTotalCount() + " sheets");

long sheetId = sheets.getData().get(0).getId();      // Default to first sheet

// sheetId = 567034672138842L;                       // TODO: Uncomment if you wish to read a specific sheet

System.out.println("Loading sheet id: " + sheetId);

// Load the entire sheet
Sheet sheet = smartsheet.sheetResources().getSheet(
    sheetId,                // long sheetId
    null,                   // EnumSet<SheetInclusion> includes
    null,                   // EnumSet<ObjectExclusion> excludes
    null,                   // Set<Long> rowIds
    null,                   // Set<Integer> rowNumbers
    null,                   // Set<Long> columnIds
    null,                   // Integer pageSize
    null                    // Integer page
);
System.out.println("Loaded " + sheet.getTotalRowCount() + " rows from sheet: " + sheet.getName());
```

A simple, but complete sample application is here: https://github.com/smartsheet-samples/java-read-write-sheet

More Java examples available [here](https://github.com/smartsheet-samples/).

## Logging
There are two types of logging used by the Smartsheet Java SDK:

### Console Logger
The console logger logs REST API traffic directly to the console. The console logger is verbose, and as such is best 
used when developing new code or features. To use the console logger, set two system properties:
```java
System.setProperty("Smartsheet.trace.parts", "RequestBodySummary, ResponseBodySummary");
System.setProperty("Smartsheet.trace.pretty", "true");
```
*Smartsheet.trace.pretty* - if ```true```, formats log messages for improved JSON readability. 
If ```Smartsheet.trace.pretty``` is ```false``` the console logger will use a compact format. 

*Smartsheet.trace.parts* - determines what portions of the API traffic are logged. Valid trace parts entries include:
- RequestHeaders
- RequestBody
- RequestBodySummary
- ResponseHeaders
- ResponseBody
- ResponseBodySummary
- Request (RequestHeaders + RequestBodySummary)
- Response (ResponseHeaders + ResponseBodySummary)

By default, console log entries are truncated at 1024 characters. You can change the truncation limit by defining a 
system property ```Smartsheet.trace.truncateLen``` and setting it equal to the desired truncation limit, for example:
```java
System.setProperty("Smartsheet.trace.truncateLen", "512");
```

### Logging Framework
The Smartsheet Java SDK also has a dependency on the SLF4J facade. SLF4J is configurable at or post distribution and
is meant for production environments. More information about SLF4J and the supported logging frameworks is available 
[here](https://www.slf4j.org). 

Using SLF4J, the Smartsheet Java SDK logs API calls that return an HTTP status other than 200. A SLF4J log 
level of *INFO* is sufficient to retrieve all API logging details. 

The POM for the Smartsheet Java SDK also includes a test only dependency on the ```slf4j-simple``` logging framework.
Details on how to configure logging are framework dependant, however, a usage example for the Simple logger can be 
found in the *simplelogger.properties* file in the Sample folder. Alternately, a usage example for Log4j can 
be found in the java-read-write-sheet example [here](https://github.com/smartsheet-samples/java-read-write-sheet).


## Documentation
The full Smartsheet API documentation is here: http://smartsheet-platform.github.io/api-docs/?java

The generated SDK javadoc is here: [http://smartsheet-platform.github.io/smartsheet-java-sdk](http://smartsheet-platform.github.io/smartsheet-java-sdk) (Download as a jar file [here](http://oss.sonatype.org/service/local/artifact/maven/redirect?r=releases&g=com.smartsheet&a=smartsheet-sdk-java&v=LATEST&c=javadoc).)

## Passthrough Option

If there is an API feature that is not yet supported by the Java SDK, there is a passthrough option that allows you to 
pass and receive raw JSON objects.

To invoke the passthrough, your code can call one of the following four methods:

`jsonResponse = smartsheet.passthroughResources().postRequest(endpoint, payload, parameters);`

`jsonResponse = smartsheet.passthroughResources().getRequest(endpoint, parameters);`

`jsonResponse = smartsheet.passthroughResources().putRequest(endpoint, payload, parameters);`

`jsonResponse = smartsheet.passthroughResources().deleteRequest(endpoint);`

* `endpoint (String)`: The specific API endpoint you wish to invoke. The client object base URL gets prepended to the caller’s 
endpoint URL argument, e.g., if endpoint is 'sheets' an HTTP GET is requested from the URL https://api.smartsheet.com/2.0/sheets
* `payload (String)`: The data to be passed through in the request payload as a string.
* `query_params (Hashmap<String, Object>)`: An optional list of query parameters.

All calls to passthrough methods return a JSON string result.

#### Passthrough Example

The following example shows how to POST data to https://api.smartsheet.com/2.0/sheets using the passthrough method and 
a JSON string payload:
```
String payload =
    "{\"name\": \"my new sheet\"," +
        "\"columns\": [" +
            "{\"title\": \"Favorite\", \"type\": \"CHECKBOX\", \"symbol\": \"STAR\"}," +
            "{\"title\": \"Primary Column\", \"primary\": true, \"type\": \"TEXT_NUMBER\"}" +
        "]" +
    "}";
String jsonResponse = smartsheet.passthroughResources().postRequest("sheets", payload, null);
```
  
## Support
If you have any questions or issues with this SDK please post on [StackOverflow using the tag "smartsheet-api"](http://stackoverflow.com/questions/tagged/smartsheet-api) or contact us directly at api@smartsheet.com.

## Contributing
If you would like to contribute a change to the SDK, please fork a branch and then submit a pull request. [More info here](https://help.github.com/articles/using-pull-requests).

#### Running the tests
Unit tests:
1. `mvn test`

Integration tests:
1. Set up an api access token in `src/integration-test/resources/config.properties`
2. `mvn integration-test`

Mock API tests:
1. Clone the [Smartsheet sdk tests](https://github.com/smartsheet-platform/smartsheet-sdk-tests) repo and follow the 
instructions from the readme to start the mock server.
2. `mvn test -Dtest=com.smartsheet.api.sdk_test.*`

## Overriding HTTP Client Behavior

You can provide a number of customizations to the default HTTP behavior by extending the DefaultHttpClient class and 
overriding one or more methods (examples below). If required, you can remove use of the Apache HTTP Client 
by implementing the HttpClient interface in a custom client (see 
[Android QRScanner](https://github.com/smartsheet-samples/QRScanner)). 

Common customizations may include:
- implementing an HTTP proxy
- injecting additional HTTP headers
- overriding default timeout or retry behavior
 
#### Sample ProxyHttpClient
The following example shows how to enable a proxy by providing the SmartsheetBuilder with an HttpClient which extends 
DefaultHttpClient.  

Invoke the SmartsheetBuilder with a custom HttpClient:

```java
ProxyHttpClient proxyHttpClient = new ProxyHttpClient("localhost", 8080);
Smartsheet smartsheet = SmartsheetFactory.custom().setHttpClient(proxyHttpClient).build();
``` 

```java
import com.smartsheet.api.internal.http.DefaultHttpClient;
import com.smartsheet.api.internal.http.HttpRequest;
import org.apache.http.HttpHost;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.methods.HttpRequestBase;

public class ProxyHttpClient extends DefaultHttpClient {

    private String proxyHost;
    private Integer proxyPort;

    public ProxyHttpClient(String proxyHost, Integer proxyPort) {
        this.proxyHost = proxyHost;
        this.proxyPort = proxyPort;
    }

    /** Override this method to inject additional headers, or setup proxy information
     * on the request.
     */
    @Override
    public HttpRequestBase createApacheRequest(HttpRequest smartsheetRequest) {
        HttpRequestBase apacheHttpRequest = super.createApacheRequest(smartsheetRequest);

        RequestConfig.Builder builder = RequestConfig.custom();
        if (apacheHttpRequest.getConfig() != null) {
            builder = RequestConfig.copy(apacheHttpRequest.getConfig());
        }
        HttpHost proxy = new HttpHost(proxyHost, proxyPort, "http");
        builder.setProxy(proxy);
        RequestConfig config = builder.build();
        apacheHttpRequest.setConfig(config);
        return apacheHttpRequest;
    }
}
```
#### Sample RetryHttpClient
The following example shows how to override the default retry/timeout logic.  

Invoke the SmartsheetBuilder with a custom HttpClient:
```java
Smartsheet smartsheet = SmartsheetFactory.custom().setHttpClient(new RetryHttpClient()).build();
smartsheet.setMaxRetryTimeMillis(30000);
```

```java
import com.smartsheet.api.internal.http.DefaultHttpClient;
import com.smartsheet.api.internal.http.HttpResponse;
import com.smartsheet.api.models.Error;

import java.io.IOException;

public class RetryHttpClient extends DefaultHttpClient {

    /**
     * Override this method to perform API requests for special cases
     */
    @Override
    public boolean shouldRetry(int previousAttempts, long totalElapsedTimeMillis, HttpResponse response) {

        // HTTP Status available as response.getStatusCode()
        int httpStatus = response.getStatusCode();

        String contentType = response.getEntity().getContentType();
        if (contentType != null && !contentType.startsWith(JSON_MIME_TYPE)) {
            // it's not JSON; don't even try to parse it
            return false;
        }
        Error error;
        try {
            // Details about the Smartsheet API error condition
            error = jsonSerializer.deserialize(Error.class, response.getEntity().getContent());
        }
        catch (IOException e) {
            return false;
        }
        switch(error.getErrorCode()) {
            // The default shouldRetry, retries 4001, 4002, 4003, 4004 codes
            case 4001:
            case 4002:
            case 4003:
            case 4004:
            case 9999: // adding my fictional error code
                break;
            default:
                return false;
        }

        // The default calcBackoff uses exponential backoff, add custom behavior by overriding calcBackoff
        long backoffMillis = calcBackoff(previousAttempts, totalElapsedTimeMillis, error);
        if(backoffMillis < 0)
            return false;

        logger.info("HttpError StatusCode=" + response.getStatusCode() + ": Retrying in " + backoffMillis + " milliseconds");
        try {
            Thread.sleep(backoffMillis);
        }
        catch (InterruptedException e) {
            logger.warn("sleep interrupted", e);
            return false;
        }
        return true;
    }
}
```

## Release Notes

Each specific release is available for download via [Github](https://github.com/smartsheet-platform/smartsheet-java-sdk/tags) or the [Maven repository](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.smartsheet%22%20AND%20a%3A%22smartsheet-sdk-java%22).

See https://github.com/smartsheet-platform/smartsheet-java-sdk/releases

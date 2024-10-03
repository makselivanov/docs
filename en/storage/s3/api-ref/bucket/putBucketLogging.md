# putBucketLogging method


Enables and disables [bucket actions logging](../../../concepts/server-logs.md).

{% include [s3-api-intro-include](../../../../_includes/storage/s3-api-intro-include.md) %}

## Request {#request}

```http
PUT /{bucket}?logging HTTP/2
```

### Path parameters {#path-parameters}

Parameter | Description
--- | ---
`bucket` | [Bucket](../../../concepts/bucket.md) name.

### Query parameters {#request-params}

Parameter | Description
--- | ---
`logging` | Required parameter that indicates the type of operation.

### Data schema {#request-scheme}

**To enable bucket actions logging**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<BucketLoggingStatus xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
   <LoggingEnabled>
      <TargetBucket>bucket-logs</TargetBucket>
      <TargetPrefix>logs/</TargetPrefix>
   </LoggingEnabled>
</BucketLoggingStatus>
```

Element | Description
--- | ---
`BucketLoggingStatus` | Root element.
`TargetBucket` | Name of the target bucket where the log [objects](../../../concepts/object.md) are saved.<br>Type: String.
`TargetPrefix` | [Log object key prefix](../../../concepts/server-logs.md#key-prefix).<br>Type: String.

**To disable bucket actions logging**:

```xml
<BucketLoggingStatus xmlns="http://doc.s3.amazonaws.com/2006-03-01" />
```

### Headers {#request-headers}

Use only [common headers](../common-request-headers.md) in your requests.

## Response {#response}

### Headers {#response-headers}

Responses can only contain [common headers](../common-response-headers.md).

### Response codes {#response-codes}

For a list of possible responses, see [{#T}](../response-codes.md).

A successful response does not contain any additional data.

{% include [the-s3-api-see-also-include](../../../../_includes/storage/the-s3-api-see-also-include.md) %}
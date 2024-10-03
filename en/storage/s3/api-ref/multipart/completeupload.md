# completeUpload method

The request completes multipart upload.

When receiving the {{ objstorage-name }} request:

- Combines the parts obtained during the upload in the order of their numbering to form a target object.
- Deletes the upload ID, which means that any subsequent requests with that ID will return the `NoSuchUpload` error.

When completing the upload, the client must provide the list of the parts it sent. The description of each part must contain an `ETag` the client gets in response to each uploaded part. See [{#T}](uploadpart.md).

The operation may take several minutes, depending on the object size and the number of parts.

If the request failed, the client app must be ready to repeat the request.

{% include [s3-api-intro-include](../../../../_includes/storage/s3-api-intro-include.md) %}

## Request {#request}

```http
POST /{bucket}/{key}?uploadId=UploadId HTTP/2
```

### Path parameters {#path-parameters}

Parameter | Description
----- | -----
`bucket` | Bucket name.
`key` | Object key.


### Query parameters {#request-parameters}

Parameter | Description
----- | -----
`uploadId` | ID of the multipart upload returned by {{ objstorage-name }} at [startup](startupload.md).


### Headers {#request-headers}

Use the appropriate [common headers](../common-request-headers.md) in your request.

### Data schema {#request-scheme}

The list of parts of a multipart upload is passed as an XML file in the following format:

```xml
<CompleteMultipartUpload>
  <Part>
    <PartNumber>PartNumber</PartNumber>
    <ETag>ETag</ETag>
  </Part>
  ...
</CompleteMultipartUpload>
```

Tag | Description
----- | -----
`CompleteMultipartUpload` | Request data.<br/><br/>Path: `/CompleteMultipartUpload`.
`Part` | Data on the object uploaded part.<br/><br/>Path: `/CompleteMultipartUpload/Part`.
`PartNumber` | Part number.<br/><br/>A unique ID that determines the part position among other parts in the upload.<br/><br/>Path: `/CompleteMultipartUpload/Part/PartNumber`.
`ETag` | ID the client received from {{ objstorage-name }} in response to the upload of a part.<br/><br/>Path: `/CompleteMultipartUpload/Part/ETag`.

## Response {#response}

### Headers {#response-headers}

Responses can only contain [common response headers](../common-response-headers.md).

### Response codes {#response-codes}

For a list of possible responses, see [{#T}](../response-codes.md).

Additionally, {{ objstorage-name }} may return errors described in the table below.

Error | Description | HTTP code
----- | ----- | -----
`NoSuchUpload` | The specified upload does not exist. The specified upload ID may be incorrect or the upload was completed or deleted. | 404 Not Found
`InvalidPart` | Some of the specified parts were not found.<br/><br/>Possible causes:<br/>- The parts have not been uploaded.<br/>- The provided `ETag` does not match the saved one. | 400 Bad Request
`InvalidPartOrder` | The list of parts was not provided in the ascending order.<br/><br/>The list must be sorted by part number in ascending order. | 400 Bad Request


A successful response contains additional data in XML format with the schema described below.

### Data schema {#request-scheme}

```xml
<CompleteMultipartUploadResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
  <Location>http://Example-Bucket.{{ s3-storage-host }}/Example-Object</Location>
  <Bucket>Example-Bucket</Bucket>
  <Key>Example-Object</Key>
  <ETag>"3858f62230ac3c915f300c664312c11f-9"</ETag>
</CompleteMultipartUploadResult>
```

Tag | Description
----- | -----
`CompleteMultipartUploadResult` | Response data.<br/><br/>Path: `/CompleteMultipartUploadResult`.
`Location` | URI of the object created as a result of uploading.<br/><br/>Path: `/CompleteMultipartUploadResult/Location`.
`Bucket` | Name of the bucket where the object resides.<br/><br/>Path: `/CompleteMultipartUploadResult/Bucket`.
`Key` | Key of the created object.<br/><br/>Path: `/CompleteMultipartUploadResult/Key`.
`ETag` | Object hash.<br/><br/>ETag may or may not be MD5.<br/><br/>Path: `/CompleteMultipartUploadResult/ETag`.

{% include [the-s3-api-see-also-include](../../../../_includes/storage/the-s3-api-see-also-include.md) %}
# x-yc-apigateway-integration:http extension

The `x-yc-apigateway-integration:http` extension redirects a request to the relevant URL.

{% include [add-extentions-console](../../../_includes/api-gateway/add-extentions-console.md) %}

## Supported parameters {#parameters}

{% include [param-table](../../../_includes/api-gateway/parameters-table.md) %}

| Parameter | Type | Description |
----|----|----
| `url` | `string` | URL to redirect the invocation to. <br>`url` is used for parameter substitution. |
| `method` | `enum` | This is an optional parameter. It sets the HTTP method used for the invocation. If it is not specified, the method to send a request to {{ api-gw-short-name }} is used by default. |
| `headers` | `map[string](string \| string[])` | HTTP headers to provide. By default, the headers of the original request are not provided. <br>`headers` is used for parameter substitution. |
| `query` | `map[string](string \| string[])` | Query parameters to provide. By default, the query parameters of the original request are not provided. <br>`query` is used for parameter substitution. |
| `timeouts` | `object` | This is an optional parameter. It sets the `read` and `connect` invocation timeouts in seconds. |
| `omitEmptyHeaders` | `boolean` | This is an optional parameter. If the value is `true`, empty headers are not provided. |
| `omitEmptyQueryParameters` | `boolean` | This is an optional parameter. If the value is `true`, empty query parameters are not provided. |
| `serviceAccountId` | `string` | Service account ID. It is used for authorization when accessing the specified URL. The value of the `serviceAccountId` [parent parameter](index.md#top-level) is ignored. |

## Extension specification {#spec}

Specification example:

```yaml
x-yc-apigateway-integration:
  type: http
  url: https://example.com/backend1
  method: POST
  headers:
    Authorization: Basic ZjTqBB3f$IF9gdYAGlMrs2********
  query:
    my_param: myInfo
  timeouts:
    connect: 0.5
    read: 5
```

Extension features:
* If the value of a header or query parameter is an array, it is provided as a single row separated by commas.
* By default, any headers other than `User-Agent` and the original request query parameters are not provided. To send all original request headers and query parameters that are not overridden in the specification, add `'*': '*'` to the `query` and `headers` sections. If you need to prevent certain headers from being provided, set their values to empty and the `omitEmptyHeaders` field value, to `true`. Similarly, you can omit certain query parameters by using the `omitEmptyQueryParameters` field.
* The `User-Agent` header is provided by default unless it is overridden in the specification.
* To redirect all requests, use [greedy parameters](./greedy-parameters.md) and the [generalized HTTP method](./any-method.md).

Below, there is an example of proxying all requests to `https://example.com` with the `Content-Type` header and the `param` query parameter provided:
```yaml
paths:
  /{path+}:
    x-yc-apigateway-any-method:
      x-yc-apigateway-integration:
        type: http
        url: https://example.com/{path}
        headers:
          Content-Type: '{Content-Type}'
        query:
          param: '{param}'
        timeouts:
          connect: 0.5
          read: 5
      parameters:
      - name: Content-Type
        in: header
        required: false
        schema:
          type: string
      - name: path
        in: path
        required: false
        schema:
          type: string
      - name: param
        in: query
        required: false
        schema:
          type: string
```

Here is another example of proxying all requests to `https://example.com`, where:
* All headers, except `Foo-Header`, and all query parameters, except `foo_param`, are provided.
* The `Bar-Header` header and the `bar_param` query parameter with an array as a value are added.
```yaml
paths:
  /{path+}:
    x-yc-apigateway-any-method:
      x-yc-apigateway-integration:
        type: http
        url: https://example.com/{path}
        query:
          '*': '*'
          foo_param: ""
          bar_param: [ "one", "two" ]
          single_param: three
        headers:
          Host: example.com
          '*': '*'
          Foo-Header: ""
          Bar-Header: [ "one", "two" ]
          Single-header: three
        omitEmptyHeaders: true
        omitEmptyQueryParameters: true
      parameters:
      - name: path
        in: path
        required: false
        schema:
          type: string
```

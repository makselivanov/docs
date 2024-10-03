# Using a top-level function for a handler in Kotlin

To create a [handler](../handler.md) in Kotlin, you can set a [top-level function](https://kotlinlang.org/docs/functions.html#function-scope). This is a function declared within a package and requiring no class for its creation.

Here is an example of a handler that accepts and returns a number:

```kotlin
fun handle(s: Int): Int = s
```

{% note warning %}

Use only one parameter to provide in top-level functions.

{% endnote %}

You can test the function from the above example in the following ways:

* In the [management console]({{ link-console-main }}), in the **{{ ui-key.yacloud.serverless-functions.item.testing.label_title }}** tab on the function page.
* Using the following HTTPS request with the [?integration=raw](../../../concepts/function-invoke.md#http) parameter:

   ```bash
   curl -H "Authorization: Bearer <IAM_token>" \
        --data "<number>" \
        "https://functions.yandexcloud.net/<function_ID>?integration=raw"
   ```

   In the `--data` parameter, specify the number for the function to return.

## Examples {#examples}

### Processing an HTTP request {#handle-http-request}

The script below processes an [incoming HTTP request](../../../concepts/function-invoke.md#http) and outputs the results: HTTP response code and response body.

The `Handler.kt` file:

```kotlin
data class Request(
    val httpMethod: String?,
    val headers: Map<String, String> = mapOf(),
    val body: String = ""
)

data class Response(
    val statusCode: Int,
    val body: String
)

fun handle(request: Request): Response {
    return Response(200, "Hello World!")
}
```

The result format depends on whether the user provided the `?integration=raw` parameter in the request:

* With `?integration=raw` provided:

   ```json
   {
       "statusCode": 200,
       "body": "Hello World!"
   }
   ```

* With no `?integration=raw` provided:

   ```text
   "Hello World!"
   ```

   The `200` code will be delivered as an HTTP status code, not as part of the response body as it would be with the `?integration=raw` parameter provided.

### HTTP request structure output {#http-request-structure}

The script below processes an incoming HTTP request and outputs its structure and HTTP response code.

The `Handler.kt` file:

```kotlin
data class Response(
    val statusCode: Int,
    val body: String
)

fun handle(request: String): Response {
    return Response(200, request)
}
```

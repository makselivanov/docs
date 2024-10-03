# Specification parameterization

With specification parameterization, you can change the way {{ api-gw-short-name }} works by setting different values for individual variables rather than the entire specification. For that purpose, you can use `x-yc-apigateway:variables`.

## X-yc-apigateway:variables extension

The `x-yc-apigateway:variables` extension allows you to declare variables that are used for specification parameterization.

### VariableObject

`VariableObject` is a set of OpеnAPI specification parameters that define a variable and its possible values.

#### Parameters

The table below lists the `VariableObject` parameters.

| Parameter | Type | Required | Description |
-----------|-----------------------------------------|-----------------------|---------
| `default` | `string`, `int`, `double`, `boolean` | Yes | Default variable value. It is used if the `enum` parameter is skipped. |
| `enum` | `string[]`, `int[]`, `double[]`, `boolean[]` | No | List of acceptable variable values. If it is not specified, the variable may take any value. |
| `description` | `string` | No | Text description of the variable. |

### How to use

After you declare the variable, you can use a `${var.name}` statement in the specification below, where `name` is the name of the variable. {{ api-gw-short-name }} will replace the variable name with the value you set when creating or updating an API gateway, if required.

### Extension specification

Sample extension specification:
```yaml
openapi: 3.0.0
info:
  title: Sample API
  version: 1.0.0

x-yc-apigateway:
  variables:
    environment:
      default: "prod"
      enum:
        - "prod"
        - "testing"
        - "dev"
      description: "API Gateway installation"

paths:
  /env/info:
    get:
      x-yc-apigateway-integration:
        type: dummy
        content:
          '*': It is ${var.environment} environment!
        http_code: 200
        http_headers:
          Content-Type: text/plain
```

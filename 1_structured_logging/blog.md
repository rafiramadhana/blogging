# Structured Logging

## What is Logging?

Logging is a practice to expose events that happened within our app's runtime. Those events could be info, warning, or error.

We can divide logging into 2 categories:

- Unstructured logging
- Structured logging

### Unstructured Logging

Unstructured logging is written in a plain text. It does not have a specific format like JSON, XML, etc. This log is human-readable, but it is difficult to do data query.

```
2021-08-08 18:04:14.721  INFO 12402 --- [           main] i.r.s.StructuredLoggingApplication       : Started StructuredLoggingApplication in 0.395 seconds (JVM running for 0.552)
```

### Structured Logging

In contrary, structured logging is written in a specific format like JSON, XML, etc.

```
{ "time": "2010-01-01 12:34:56.0000", "level": "ERROR", "message": "hello, world" }
```

## Why Do We Need Structured Logging?

With a specific format like JSON, we can utilise tools like `jq` to do data query.

## Querying Structured Logging With JQ

As mentioned, one of the benefits of structured logging is easier data query. Here, we are going to use `jq` to do data query.

Consider we are having these logs (see [log.txt](./example/log.txt))

```
{"request_id":"q5XhRI","user_id":"1","message":"Pay book","level":"INFO"}
{"request_id":"q5XhRI","user_id":"1","message":"pay book success","level":"INFO"}
{"request_id":"wrgHOx","user_id":"2","message":"Pay book","level":"INFO"}
{"request_id":"wrgHOx","user_id":"2","message":"Pay book failed","level":"ERROR"}
{"request_id":"Xq3F0s","user_id":"1","message":"Add book to cart","level":"INFO"}
{"request_id":"Xq3F0s","user_id":"1","message":"Add book to cart success","level":"INFO"}
```

To check for a failed request, we can do

```
➜  example git:(main) ✗ cat log.txt | jq '. | select(.level == "ERROR")'
{
  "request_id": "wrgHOx",
  "user_id": "2",
  "message": "Pay book failed",
  "level": "ERROR"
}
```

Then, we can proceed to check for the corresponding request with ID `wrgHOx`.

```
➜  example git:(main) ✗ cat log.txt | jq '. | select(.request_id == "wrgHOx")'
{
  "request_id": "wrgHOx",
  "user_id": "2",
  "message": "Pay book",
  "level": "INFO"
}
{
  "request_id": "wrgHOx",
  "user_id": "2",
  "message": "Pay book failed",
  "level": "ERROR"
}
```

As another example, let's try to check for all requests done by `user_id` of `1`

```
➜  example git:(main) ✗ cat log.txt | jq '. | select(.user_id == "1")'
{
  "request_id": "q5XhRI",
  "user_id": "1",
  "message": "Pay book",
  "level": "INFO"
}
{
  "request_id": "q5XhRI",
  "user_id": "1",
  "message": "pay book success",
  "level": "INFO"
}
{
  "request_id": "Xq3F0s",
  "user_id": "1",
  "message": "Add book to cart",
  "level": "INFO"
}
{
  "request_id": "Xq3F0s",
  "user_id": "1",
  "message": "Add book to cart success",
  "level": "INFO"
}
```

This only one of the many benefits of having a strucured logging. We might unlock more benefits by providing fields, such as, `timestamp` or `http_status`.

* [Requests and Responses](#requests-and-responses)
  * [Return Values](#return-values)
  * [Other Parameters](#other-parameters)
  * [Queries, Files and Bodies](#queries-files-and-bodies)
* [Next Up...](#next-up)

# Requests and Responses
Angel is inspired by Express, and such, request handlers in general represent those from Express. Request handlers can be functions, or plain Dart objects (see [how they are handled](#return-values)). Basic request handlers accept two parameters:
* `RequestContext` - Contains vital information about the client requesting a resource, such as request method, request body, IP address, etc. The request object can also be used to pass information from one handler to the next. 
* `ResponseContext` - Allows you to send headers, write data, and more, to be sent to the client. To prevent a response from being modified by future handlers, call `res.end()` to prevent further writing.

Both requests and responses contain a Map of `properties` that can be filled with arbitrary data and read/modified at any point during the [request lifecycle](https://github.com/angel-dart/angel/wiki/Request-Lifecycle).

## Return Values
Request handlers can return any Dart value. Return values are handled as follows:
* If you return a `bool`: Request handling will end prematurely if you return `false`, but it will continue if you return `true`.
* If you return `null`: Request handling will continue, unless you closed the response object by calling `res.end()`. Some response methods, such as `res.redirect()` or `res.serialize()` automatically close the response.
* Anything else: Whatever other Dart value you return will be serialized as a response. The default method is to encode responses as JSON, and to do so using reflection (see `package:json_god`). However, you can change a response's serialization method by setting `res.serializer = foo;`. If you want to assign the same serializer to all responses, call `app.injectSerializer` on your Angel instance. If you are only returning JSON-compatible Dart objects, like Maps or Lists, you might consider injecting `JSON.encode` as a serializer, to improve runtime performance.

## Other Parameters
Request handlers can take other parameters, instead of just a `RequestContext` and `ResponseContext`. All parameters will be [injected](https://github.com/angel-dart/angel/wiki/Dependency-Injection) into a response, whether from `req.injections`, `req.params`, or `req.properties`.

Request handlers do not even have to be functions at all. You can provide singleton values as request handlers, and they will always be sent to clients without running any functions.

```dart
main() {
  Angel app = new Angel();

  // String will be JSON-encoded
  app.get('/', (req, res) async => "Hello, world!");

  // Access params
  app.get('/:id', (req, res) async => "ID: ${req.params['id']}");

  app.post('/', ["More", "arbitrary", "data"]);

  app.get('/todos/:id', (String id) => fetchTodoById(id));
}
```

## Queries, Files and Bodies
`req.query` and `req.body` are Maps, and are available on each request. `req.files` is a List of files uploaded to the server. 

For more information, see the API docs:

[RequestContext](https://www.dartdocs.org/documentation/angel_framework/latest/angel_framework/RequestContext-class.html)

[ResponseContext](https://www.dartdocs.org/documentation/angel_framework/latest/angel_framework/ResponseContext-class.html)

# Next Up...
Now, let's learn about Angel's [flexible router](https://github.com/angel-dart/angel/wiki/Basic-Routing).
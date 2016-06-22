# Requests and Responses

Angel is inspired by Express, and such, request handlers in general represent those from Express. However, you can return any arbitrary value from a handler, and it will be serialized as JSON.

```dart
main() {
  Angel app = new Angel();

  // String will be JSON-encoded
  app.get('/', (req, res) async => "Hello, world!");

  // Access params
  app.get('/:id', (req, res) async => "ID: ${req.params['id']}");

  app.post('/', ["More", "arbitrary", "data"]);
}
```

# Queries, Files and Bodies
`req.query` and `req.body` are Maps, and are available on each request. `req.files` is a List of files uploaded to the server. When the `body_parser` module this framework depends on is updated, multiple file uploads will be supported.

For more information, see the API docs.

[RequestContext](https://www.dartdocs.org/documentation/angel_framework/1.0.0-dev/angel_framework/RequestContext-class.html)

[ResponseContext](https://www.dartdocs.org/documentation/angel_framework/1.0.0-dev/angel_framework/ResponseContext-class.html)
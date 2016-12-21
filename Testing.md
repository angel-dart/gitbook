Dart already has fantastic testing support, so the Angel framework did not need to add any real testing functionality. However, there is a library of [testing helpers](https://github.com/angel-dart/test) that will make test writing faster.

# connectTo(app, {bool saveSession: false})

[Full definition](https://www.dartdocs.org/documentation/angel_test/latest/angel_test/connectTo.html)

This function will start `app` on an available port, and return an [`angel_client`]() instance configured to send requests to the server. The client also supports session manipulation.

```dart
main() {
  TestClient client;

  setUp(() async {
    client = await connectTo(myApp);
  });

  // Shut down server, and cancel pending requests
  tearDown(() => client.close());

  test('hello', () async {
    // The server URL is automatically prepended to paths.
    // This returns an http.Response. :)
    var response = await client.get('/hello');
  });
}
```

# isJson(value)
A `Matcher` that asserts that the given `http.Response` equals `value` when decoded as JSON. This uses `test.equals` internally, so anything that would pass that matcher passes this one.

# hasStatus(status)
A `Matcher` that asserts the given `http.Response` has the given `status` code.
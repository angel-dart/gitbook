# Error-Handling

* [Error Handling](error-handling.md#error-handling)
* [Next Up...](error-handling.md#next-up)

## Error Handling

Error handling is one of the most important concerns in building Web applications. The easiest way to throw an HTTP exception is to actually `throw` one. Angel provides an `AngelHttpException` class to take care of this.

```dart
app.get('/this-page-does-not-exist', (req, res) async {
  // 404 Not Found
  throw new AngelHttpException.notFound();
});
```

Of course, you will probably want to handle these errors, and potentially render views upon catching them.

Fortunately, Angel runs every request in its own [`zone`](https://api.dartlang.org/stable/dart-async/Zone-class.html).
This enables Angel to catch errors on every request, and not crash the server.
Unhandled errors are wrapped in instances of `AngelHttpException`, which can be handled as follows.

To provide custom error handling logic:

```dart
// Typically, you want to preserve the old error handler, unless you are
// completely replacing the functionality.
var oldErrorHandler = app.errorHandler;

app.errorHandler = (AngelHttpException e, RequestContext req, ResponseContext res) {
  if (someCondition) {
    // Do something else special...
  } else {
    // Otherwise, use the default functionality.
    return oldErrorHandler(e, req, res);
  }
}
```


## Next Up...

Congratulations! You have completed the basic Angel tutorials. Take what you've learned on a spin in a small side project, and then move on to learning about [services](../services/service-basics.md).


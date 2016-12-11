Error handling is one of the most important concerns in building Web applications. The easiest way to throw an HTTP exception is to actually throw one. Angel provides an `AngelHttpException` class to take care of this.

```dart
app.get('/this-page-does-not-exist', (req, res) async {
  // 404 Not Found
  throw new AngelHttpException.NotFound();
});
```

Of course, you will probably want to handle these errors, and potentially render views upon catching them. There are a few ways of doing this.

## 1. Using the provided plugin

The [`angel_errors`](https://github.com/angel-dart/errors) plugin provides a simple abstraction over the complications of catching errors within Angel.

```dart
final errors = new ErrorHandler({
  404: (req, res) async => render404Page(),
  403: (req, res) async => renderForbidden(),
  500: (req, res) async => renderGenericErrorPage()
});

// The following line is necessary to render the
// above pages on AngelHttpException instances.
await app.configure(errors);

app.get('/hello', (req, res) async => foo());

// This middleware simply sets a request's status, and
// passes it to the next handler.
app.after.add(errors.throwError(status: 404));

// This middleware will respond to any unanswered request
// with the
app.all('*', errors.middleware());
```



## 2. Manual error handling
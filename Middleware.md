# Middleware

Sometimes, it becomes to recycle code to run on multiple routes. Angel allows for this in the form of *middleware*. Middleware are frequently used as authorization filters, or to serialize database data for use in subsequent routes. Middleware in Angel can be any route handler, whether a function or arbitrary data. You can also throw exceptions in middleware.

## Denying Requests via Middleware
A middleware should return either `true` or `false`. If `false` is returned, no further routes will be executed. If `true` is returned, route evaluation will continue.

As you can imagine, this is perfect for authorization filters.

## Declaring Middleware

```dart
app.get('/', 'world!', middleware: [(req, res) {
  res.write("Hello, ");
  return true;
}]);
```

## Named Middleware
`Router` instances allow you to assign names to middleware via `registerMiddleware`. After registering a middleware, you can include it in a route just by passing its name into the `middleware` array. If you are `mount`-ing another `Routable` or `Angel` instance, you can map all its middleware into a namespace by passing it to the `use` call.

```dart
app.registerMiddleware('deny', (req, res) async => false);
app.get('/no', 'This will never show', middleware: ['deny']);

// Using annotation
@Middleware(const ['deny'])
Future never(RequestContext req, ResponseContext res) async {
  return "This will never show either";
}

app.get('/yes', never);

// Using a middleware namespace
Angel parent = new Angel();
parent.use('/child', app, middlewareNamespace: 'child');
parent.get('/foo', 'Never shown', middleware: ['child.deny']);
```

## Global Middleware
`Routable` instances contain two arrays, `before` and `after`. You can add middleware to these to run before and after every requests, respectively. This also supports using named middleware, of course.

```dart
app.before.add((req, res) async => res.end());
```

For more complicated middleware, implement the `AngelMiddleware` class.

```dart
class MyMiddleware implements AngelMiddleware {
  Future<bool> call(Angel app) async {
    // Do something...
  }
}
```
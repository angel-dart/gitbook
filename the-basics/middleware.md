# Middleware

* [Middleware](middleware.md#middleware)
  * [Denying Requests via Middleware](middleware.md#denying-requests-via-middleware)
  * [Declaring Middleware](middleware.md#declaring-middleware)
  * [Named Middleware](middleware.md#named-middleware)
  * [Global Middleware](middleware.md#global-middleware)
  * [`waterfall([...])`](middleware.md#waterfall)
* [Next Up...](middleware.md#next-up)

## Middleware

Sometimes, it becomes to recycle code to run on multiple routes. Angel allows for this in the form of _middleware_. Middleware are frequently used as authorization filters, or to serialize database data for use in subsequent routes. Middleware in Angel can be any route handler, whether a function or arbitrary data. You can also throw exceptions in middleware.

### Denying Requests via Middleware

A middleware should return either `true` or `false`. If `false` is returned, no further routes will be executed. If `true` is returned, route evaluation will continue. \(more on request handler return values [here](requests-and-responses.md#return-values)\).

As you can imagine, this is perfect for authorization filters.

### Declaring Middleware

You can call a router's `chain` method \(**recommended!**\), or assign middleware in the `middleware` parameter of a route method.

```dart
// Both ways ultimately accomplish the same thing

app
  .chain((req, res) {
    res.write("Hello, ");
    return true;
  }).get('/', 'world!');

app.get('/', 'world!', middleware: [someListOfMiddleware]);
```

### Named Middleware

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

### Global Middleware

To add a handler that handles *every* request, call `app.use`. This is equivalent to calling `app.all('*', <handler>)`. \(more info on request lifecycle [here](request-lifecycle.md)\). 

This pattern is seen throughout many Angel plugins, such as `VirtualDirectory` or `Proxy`.

```dart
app.use((req, res) async => res.end());
```

For more complicated middleware, you can also create a class:

```dart
class MyMiddleware {
  Future<bool> call(Angel app) async {
    // Do something...
  }
}
```

### waterfall

You can chain middleware \(or any request handler together\), if you do not feel like making multiple `chain` calls, or if it is impossible to call chain multiple times, using the [`waterfall`](https://www.dartdocs.org/documentation/angel_framework/latest/angel_framework/waterfall.html) function:

```dart
app.chain(waterfall([
  banIp('127.0.0.1'),
  'auth',
  ensureUserHasAccess(),
  (req, res) async => true,
  takeOutTheTrash()
])).get(...);
```

## Next Up...

Take a good look at [controllers](controllers.md) in Angel!


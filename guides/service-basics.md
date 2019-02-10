# Service-Basics

* [Services](service-basics.md#services)
  * [Service Parameters and Middleware](service-basics.md#service-parameters-and-middleware)
  * [Mounting Services](service-basics.md#mounting-services)
* [Next Up...](service-basics.md#next-up)

## Services

One of the main concepts within Angel, which is borrowed from FeathersJS, is a _service_. You more than likely have already dealt with another implementation of the service concept. In Angel, a _service_ is a class that acts as a Web interface and exposes CRUD actions operating on a set of data. Angel services extend `Routable`, and thus can be mounted on a certain path and become REST endpoints.

The Angel core includes the `Service` base class, as well as two in-memory service classes. Database adapter packages, such as [`package:angel_mongo`](https://github.com/angel-dart/mongo) include service classes that let you interact with a database without writing complex code yourself.

Services can also be filtered or reacted to with [service hooks](hooks.md).

A service looks like this:

```dart
class MyService extends Service<String, Map<String, dynamic>> {
  // GET /
  // Fetch all resources. Usually returns a List.
  @override
  Future<List<Map<String, dynamic>>> index([Map<String, dynamic> params]);

  // GET /:id
  // Fetch one resource, by its ID
  @override
  Future<Map<String, dynamic>> read(String id, [Map<String, dynamic> params]);

  // POST /
  // Create a resource. This endpoint should return
  // the created resource.
  @override
  Future<Map<String, dynamic>> create(Map<String, dynamic> data, [Map<String, dynamic> params]);

  // PATCH /:id
  // Modifies a resource. Clients can submit only the data
  // they want to change, and the corresponding resource will
  // have only those fields changed. This endpoint should return
  // the modified resource.
  @override
  Future<Map<String, dynamic>> modify(String id, Map<String, dynamic> data, [Map<String, dynamic> params]);

  // POST /:id
  // Overwrites a resource. The existing resource is completely
  // replaced by the new data. This endpoint should return the
  // new resource.
  @override 
  Future<Map<String, dynamic>> update(String id, Map<String, dynamic> data, [Map<String, dynamic> params]);

  // DELETE /:id
  // Deletes a resource. This endpoint should return the
  // deleted resource.
  @override
  Future<Map<String, dynamic>> remove(String id, [Map<String, dynamic> params]);
}
```

There are meta-methods that default to delegating to the above:
* `findOne`
* `readMany`

You can override these for your service, if it will improve performance.

### Service Parameters and Middleware

You might notice that each service method accepts an optional `Map` of parameters. When accessed via HTTP \(i.e., not over Websockets\), `req.query` or `req.bodyAsMap` is passed here \(`query` for `index`, `read` and `delete`, `bodyAsMap` for `create`, `update` and `modify`\). To pass custom parameters to a service, you should create a middleware to do so. `@Middleware` annotations can be prepended to service classes or service methods. For example, the following will pass `foo='bar'` to every method in the service:

```dart
Future<bool> myMiddleware(RequestContext req, res) async {
  req.queryParameters['foo'] = 'bar';
  return true;
}

@Middleware(const [myMiddleware])
class MyService extends Service {
  // Responds with "['bar']"
  @override index([Map params]) async => [params['query']['foo']];
}
```

Additionally, when accessed by a client, `params` will contain a field called `provider`.

```dart
class MyService extends Service {
  @override
  create(data, [Map params]) async {
    if (params == null || params['provider'] == null) {
       // Accessed via server
    }
  }
}
```

`provider` will be a `Providers` class, whose `String via` will tell you where the service is being accessed from, i.e. `'rest'`, `'graphql'` or `'websocket'`.

### Mounting Services

As mentioned above, services extend `Routable`, so you can simply `app.use()` them. You can also supplement them with additional routes or middleware, placed _before_ the mounting of a service:

```dart
app.get("/user/:id/todos", ioc((id) => fetchUserTodos(id))));

// Another way to apply a middleware to a service
app.all("/user/*", [someMiddleware], middleware: ['some', 'more', 'middleware']);

app.use('/user', TypedService<User>(MongoService(db.collection("users"))));

// Access app services. Returns a HookedService if there is one, otherwise just the plain service.
// Leading and trailing slashes are ignored.
var service = app.findService('user'); // The user service
var service = app.service<String, Map<String, dynamic>>('secret'); 
```

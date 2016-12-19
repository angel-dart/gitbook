# Routing
It is very to attach handlers to dynamic paths. Routes are resolved in the order they are declared.

The simplest routes look like this, and are self-explanatory:
```dart
app.get('/', someRequestHandler);
app.post('/', someRequestHandler);
app.patch('/', someRequestHandler);
app.delete('/', someRequestHandler);
app.addRoute('PUT', '/', someRequestHandler);
```

# Route parameters
Say you're building an API, or an MVC application. You typically want to serve the same view template on multiple paths, corresponding to different ID's. You can do this as follows, and all parameters will be available via `req.params`.

```dart
app.get('/todos/:id', (RequestContext req, res) async => {'id': req.params['id']});
```

# RegExp routes
You can also use a RegExp as a route pattern, but you will have to parse the URI yourself.

```dart
app.post(new RegExp(r'\/todos/([A-Za-z0-9]+)'), (req, res) async => "RegExp");
```

# Sub-Apps
You can `mount` routers, or `use` entire sub-apps.

```dart
Angel app = new Angel();
app.get('/', 'Hello!');

var subRouter = new Router()..get('/', 'Subroute');
app.mount('/sub', subApp);
// Now, you can visit /sub and receive the message "Subroute"

var subApp = new Angel()..get('/hello', 'world');
app.use('/api', subApp);

// GET /api/hello returns "world"
```
# Routing
It is very to attach handlers to dynamic paths. Routes are resolved in the order they are declared.

The simplest routes look like this, and are self-explanatory:
```dart
app.get('/', 'GET');
app.post('/', 'POST');
app.patch('/', 'PATCH');
app.delete('/', 'DELETE');
app.addRoute('PUT', '/', 'PUT');
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
With Angel, several classes, including `Angel` and `Service`, extend a base class called `Routable`. A `Routable` contains a method, `use`, that maps all requests to a sub-path to another set of routes. For example:

```dart
Angel app = new Angel();
app.get('/', 'Hello!');

Routable subApp = new Routable();
subApp.get('/, 'Subroute');

app.use('/sub', subApp);

// Now, you can visit /sub and receive the message "Subroute"
```
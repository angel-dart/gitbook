# Routing
It is very to attach handlers to dynamic paths.

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
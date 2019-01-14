# Dependency-Injection

Angel uses Emil Persson's [Container](https://pub.dartlang.org/packages/container) for DI. Dependency injection makes it easier to build applications with multiple moving parts, because logic can be contained in one location and reused at another place in your application.

## Adding a Singleton

```dart
class MyPlugin extends AngelPlugin {
  @override
  call(Angel app) async {
    app.container.singleton(new SomeClass("foo"));
  }
}

class SomeClass {
  String text;
  SomeClass(this.text);
}
```

You can also inject within a `RequestContext`.

```dart
// Inject types
req.inject(Todo, someTodoInstanceSingleton);

// Or by name
req.inject('database', await databaseProvider.connect('proto://conn-string'));

// Inject into *every* request
app.inject('foo', bar);
```

## In Routes and Controllers

```dart
app.get("/some/class/text", (SomeClass singleton) => singleton.text); // Always "foo"

app.post("/foo", (SomeClass singleton, {Foo optionalInjection});

@Expose("/my/controller")
class MyController extends Controller {

  @Expose("/bar")
  // Inject classes from container, request parameters or the request/response context :)
  bar(SomeClass singleton, RequestContext req) => "${singleton.text} bar"; // Always "foo bar"

  @Expose("/baz")
  baz({Foo optionalInjection});
}
```

As you can imagine, this is very useful for managing things such as database connections.

```dart
configureServer(Angel app) async {
  var db = new Db("mongodb://localhost:27017/db");
  await db.open();
  app.container.singleton(db);
}

@Expose("/users")
class ApiController extends Controller {
  @Expose("/:id")
  fetchUser(String id, Db db) => db.collection("users").findOne(where.id(new ObjectId.fromHexString(id)));
}
```

## Dependency-Injected Controllers

`Controller`s have dependencies injected without any additional configuration by you. However, you might want to inject dependencies into the constructor of your controller.

```dart
@Expose('/controller')
class MyController {
  final AngelAuth auth;
  final Db db;

  MyController(this.auth, this.db);

  @Expose('/login')
  login() => auth.authenticate('local');
}

main() async {
  // At some point in your application, register necessary dependencies as singletons...
  app.container.singleton(auth);
  app.container.singleton(db);

  // Create the controller with injected dependencies
  await app.configure(app.container.make(MyController));
}
```


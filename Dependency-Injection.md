# Dependency Injection

Angel uses Emil Persson's [Container](https://pub.dartlang.org/packages/container) for DI.

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
```

## In Routes and Controllers

```dart
app.get("/some/class/text", (SomeClass singleton) => singleton.text); // Always "foo"

@Expose("/my/controller")
class MyController extends Controller {

  @Expose("/bar")
  // Inject classes from container, request parameters or the request/response context :)
  bar(SomeClass singleton, RequestContext req) => "${singleton.text} bar"; // Always "foo bar"
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
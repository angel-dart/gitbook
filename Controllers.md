# Controllers

Angel has built-in, reflection-based support for MVC controllers. This is yet another way to define routes in a manageable group. You can also use a `Service`, an `Angel` instance, or the base `Routable` class.

```dart
import 'package:angel_framework/angel_framework.dart';

@Expose("/todos")
class TodoController extends Controller {

  @Expose("/:id")
  getTodo(int id) async {
    return await someAsyncAction();
  }
}

main() async {
  Angel app = new Angel();
  await app.configure(new TodoController());
}
```

Rather than extending from `Routable`, controllers return an `AngelConfigurer` when called. This configurer will wire all your routes for you.

# @Expose()
The glue that holds it all together is the `Expose` annotation:

```dart
class Expose {
  final String method;
  final Pattern path;
  final List middleware;
  final String as;
  final List<String> allowNull;

  const Expose(Pattern this.path,
      {String this.method: "GET",
      List this.middleware: const [],
      String this.as: null,
      List<String> this.allowNull: const[]});
}
```

# Allowing Null Values
Most fields are self-explanatory, save for `as` and `allowNull`. See, request parameters are mapped to function parameters on each handler. If a parameter is `null`, an error will be thrown. To prevent this, you can pass its name to `allowNull`.

```dart
@Expose("/foo/:id?", allowNull: const["id"])
```

# Named Controllers and Actions

The other is `as`. This allows you to specify a custom name for a controller class or action. `ResponseContext` contains a method, `redirectToAction` that can redirect to a controller action.

```dart
@Expose("/foo")
class FooController extends Controller {
  @Expose("/some/strange/url", as: "bar")
  someActionWithALongNameThatWeWouldLikeToShorten(int id) async {
  }
}

main() async {
  Angel app = new Angel();

  app.get("/some/path", (req, res) async => res.redirectToAction("FooController@bar", {"id": 1337}));
}
```

If you do not specify an `as`, then controllers and actions will be available by their names in code. Reflection is cool, huh?

# Interacting with Requests and Responses

Controllers can also interact with requests and responses. All you have to do is declare a `RequestContext` or `ResponseContext` as a parameter, and it will be passed to the function.

```dart
@Expose("/hello")
class HelloController extends Controller {
  @Expose("/")
  Future getIndex(ResponseContext res) async {
    await res.render("hello");
  }
}
```

# Transforming Data

You can use middleware to de/serialize data to be processed in a controller method.

```dart
deserializeUser(RequestContext req, res) async {
  var id = req.params['id'].toString();

  return await asyncFetchUser(id);
}

@Expose("/user", middleware: const["deserialize_user"]
class UserController extends Controller {

  @Expose("/:id/name")
  Future<String> getUserName(User user) async {
    return user.username;
  }

}

main() async {
  Angel app = new Angel()..registerMiddleware("deserialize_user", deserializeUser);
  await app.configure(new UserController());
}
```
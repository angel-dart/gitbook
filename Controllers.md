# Controllers

Angel has built-in, reflection-based support for MVC controllers. This is yet another way to define routes in a manageable group. You can also use a `Service`, an `Angel` instance, or the base `Routable` class.

Rather than extending from `Routable`, controllers return an `AngelConfigurer` when called. This configurer will wire all your routes for you.

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

Most fields are self-explanatory, save for `as` and `allowNull`. See, request parameters are mapped to function parameters on each handler. If a parameter is `null`, an error will be thrown. To prevent this, you can pass its name to `allowNull`.

```dart
@Expose(path: "/foo/:id?", allowNull: const["id"])
```

The other is `as`. This allows you to specify a custom name for a controller class or action. `ResponseContext` contains a method, `redirectToAction` that can redirect to a controller action.

```dart
@Expose(path: "/foo")
class FooController extends Controller {
  @Expose(path: "/some/strange/url", as: "bar")
  someActionWithALongNameThatWeWouldLikeToShorten(int id) async {
  }
}

main() async {
  Angel app = new Angel();

  app.get("/some/path", (req, res) async => res.redirectToAction("FooController@bar", {"id": 1337}));
}
```

If you do not specify an `as`, then controllers and actions will be available by their names in code. Reflection is cool, huh?

```dart
import 'package:angel_framework/angel_framework.dart';

@Expose(path: "/todos")
class TodoController extends Controller {

  @Expose(path: "/:id")
  getTodo(int id) async {
    return await someAsyncAction();
  }
}

main() async {
  Angel app = new Angel();
  await app.configure(new TodoController());
}
```

Controllers can also interact with requests and responses. All you have to do is declare a `RequestContext` or `ResponseContext` as a parameter, and it will be passed to the function.

```dart
@Expose(path: "/hello")
class HelloController extends Controller {
  @Expose(path: "/") Future getIndex(ResponseContext res) async => await res.render("hello");
}
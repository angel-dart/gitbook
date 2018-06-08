# Without-the-Boilerplate

It's very easy to setup a bare-bones Angel server.

Any Dart project needs a project file, called `pubspec.yaml`. This file almost always contains a `dependencies` section, where you will install the Angel framework libraries.

```yaml
dependencies:
    angel_framework: ^1.1.0
```

You might also want to install packages such as `angel_static`, `angel_cache`, `angel_jael`, and `angel_cors`.

Next, run `pub get` on the command line, or in your IDE if it has Dart support. This will install the framework and all of its dependencies.

Next, create a file, `bin/server.dart`. Put this code in it:

```dart
import 'dart:io';
import 'package:angel_framework/angel_framework.dart';

main() async {
  Angel app = new Angel();

  app.get("/", "Hello, world!");

  var server = await app.startServer();
  print("Angel server listening on port ${server.port}");
}
```

The specifics are not that important, but there are three important calls here:

1. `Angel app = new Angel()` - The base Angel server is a simple class, and we need an instance of it to run our server. The name `app` is a convention adopted from Express. In general, call an Angel instance `app`. This has no effect on functionality, but it makes it easier for other developers to understand your code.
2. `app.get("/", "Hello, world!");` - This is a [route](basic-routing.md), and tells our server to respond to all GET requests at our server root with `"Hello, world!"`. The response will automatically be encoded as JSON. Head over to the [Basic Routing](basic-routing.md) tutorial to learn about routes, and how they work.
3. `await app.startServer(...)` - This asynchronous call is what actually starts the server listening. Without it, your application won't be accessible over HTTP \(as it won't ever listen for requests\).

That's it! Your server is ready to serve requests. You can easily start it from the command line like this:

```text
dart bin/server.dart
```


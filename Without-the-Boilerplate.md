# Without the Boilerplate
It's very easy to setup a bare-bones Angel server.

Any Dart project needs a project file, called `pubspec.yaml`. This file almost always contains a `dependencies` section, where you will install the Angel framework libraries. In most cases, you will want to import `package:angel_common`, which contains all of the functionality you need to build an Angel application. However, if you only want the HTTP server, install `package:angel_framework` instead. `package:angel_common` bundles `package:angel_framework`, along with other core packages (see [here](https://github.com/angel-dart/common)).

```yaml
dependencies:
    angel_common: ^1.0.0-beta
```

Next, run `pub get` on the command line, or in your IDE if it has Dart support. This will install the framework and all of its dependencies.

Next, create a file, `bin/server.dart`. Put this code in it:

```dart
import 'dart:io';
import 'package:angel_common/angel_common.dart';

main() async {
  Angel app = new Angel();

  app.get("/", "Hello, world!");

  var server = await app.startServer();
  print("Angel server listening on port ${server.port}");
}
```

The specifics are not that important, but there are three important calls here:

1. `Angel app = new Angel()` - The base Angel server is a simple class, and we need an instance of it to run our server. The name `app` is a convention adopted from Express. In general, call an Angel instance `app`. This has no effect on functionality, but it makes it easier for other developers to understand your code.
2. `app.get("/", "Hello, world!");` - This is a [route](https://github.com/angel-dart/angel/wiki/Basic-Routing), and tells our server to respond to all GET requests at our server root with `"Hello, world!"`. The response will automatically be encoded as JSON. Head over to the [Basic Routing](https://github.com/angel-dart/angel/wiki/Basic-Routing) tutorial to learn about routes, and how they work.
3. `await app.startServer(...)` - This asynchronous call is what actually starts the server listening. Without it, your application won't be accessible over HTTP (as it won't ever listen for requests).

That's it! Your server is ready to serve requests. You can easily start it from the command line like this:

    dart bin/server.dart
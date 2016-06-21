# Getting Started
Let's get it started, ha!

It's very easy to setup a bare-bones Angel server.

## Installation

Firstly, ensure you have the [Dart SDK](https://www.dartlang.org/downloads/) installed.

Create a file called `pubspec.yaml`. In it, include lines that look something like this. Feel free to replace 'app' with the name of your app:

```yaml
name: app
dependencies:
    angel_framework: ^1.0.0-dev
```

Next, run `pub get` on the command line, or in your IDE if it has Dart support. This will install the framework and all of its dependencies.

Next, create a file, `bin/server.dart`. Put this code in it:

```dart
import 'dart:io';
import 'package:angel_framework/angel_framework.dart';

main() async {
  Angel app = new Angel();

  await app.startServer(InternetAddress.LOOPBACK_IP_V4, 3000);
  print("Angel server listening on localhost:3000");
}
```

The specifics are not that important, but there are two important calls here:

1. `Angel app = new Angel()` - The Angel API is manifested a class, and we need an instance of it to run our server.
2. `await app.startServer(...)` - This asynchronous call is what actually starts the server listening.

You might consider wrapping this in a call to `runZoned`, so your server does not crash on errors.

That's it! Your server is ready to serve requests. You can easily start it from the command line like this:

    dart bin/server.dart
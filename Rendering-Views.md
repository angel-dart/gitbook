# Rendering Views
Just like `res.render` in Express, Angel's `ResponseContext` exposes a `Future` called `render`. This invokes whichever function is assigned to your server's `viewGenerator`.

There are several templating plug-ins for Angel, including Mustache and Jade, as well as Hart.

## Example

```dart
app.get('/view', (req, res) async => await res.render('hello', {'locals': ['foo', 'bar']});
```

## ViewGenerator
Angel declares the following typedef:

```dart
/// A function that asynchronously generates a view from the given path and data.
typedef Future<String> ViewGenerator(String path, [Map data]);
```

A templating plug-in can assign one of these to `Angel@viewGenerator` to set itself up.

```dart
import 'dart:io';
import 'package:angel_framework/angel_framework.dart';

Future plugin(Angel app) async {
  app.viewGenerator = (String path, [Map data]) async {
    return "Requested view $path with locals: $data";
  };
}

main() async {
  Angel app = new Angel();
  await app.configure(plugin);

  await app.startServer();
}
```
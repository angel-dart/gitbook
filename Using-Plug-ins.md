# Using Plug-ins
Angel is designed to be extensible. As such, it exposes a typedef, `AngelConfigurer`. These can be called via `Angel@configure`.

```dart
typedef Future AngelConfigurer(Angel app);
```

Angel plug-ins should be hooked up **before** the call to `startServer`.

```dart
import 'dart:io';
import 'package:angel_framework/angel_framework';

plugin(Angel app) async {
  print("Do stuff here");
}

main() async {
  Angel app = new Angel();
  await app.configure(plugin);
  await app.startServer();
}
```
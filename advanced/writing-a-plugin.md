# Writing-a-Plugin

[Guidelines](writing-a-plugin.md#guidelines)

Writing a [plug-in](../the-basics/using-plug-ins)
is easy. You can provide plug-ins as either functions, or classes:

```dart
AngelConfigurer awesomeify() => (Angel app) async {
  app.before.add((req, res) async {
    req.write('This request was intercepted by an awesome plug-in.');
    return false;
  });
}

class MyAwesomePlugin extends AngelPlugin {
  @override
  Future call(Angel app) async {
    app.responseFinalizers.add((req, res) async {
      res.headers['Be-Awesome'] = 'All the time';
    });
  }
}
```

## Guidelines

* Plugins should only do one thing, or serve one purpose.
* Functions are preferred to classes.
* Always need to be well-documented and thoroughly tested.
* Make sure no other plugin already serves the purpose.
* Use the provided Angel API's whenever possible. This will help your plugin resist breaking change in the future.
* Try to get it [added to main organization](https://github.com/angel-dart/roadmap/blob/master/CONTRIBUTING.md).
* Plugins should generally be small.
* Plugins should _NEVER_ modify app configuration!!!
  * i.e. Do _NOT_ set `app.lazyParseBodies`, `app.storeOriginalBuffer`, etc.
* Stay away from `req.io` and `res.io` if possible. Using these will doom your plugin to a life of only working on HTTP servers. Future versions of Angel may be server-agnostic, and this will keep your plugin firmly lodged in the past.
* If your plugin is development-only or production-only, it should automatically configure itself. Prefer [`app.isProduction`](https://www.dartdocs.org/documentation/angel_framework/latest/angel_framework/Angel/isProduction.html) to manually checking the environment for `ANGEL_ENV`.
* Use `req.lazyBody()`, `req.lazyFiles()`, etc. if you are running in an `async` context. Otherwise, your plugin may crash applications that lazy-parse request bodies.
* If you use `req.lazyQuery()`, refrain from using `forceParse`. Never force any additional side effects on the user.

Finally, your plugin should expose common options in a simple way. For example, the \(deprecated\) [compress](https://github.com/angel-dart/compress) plugin has a shortcut function, `gzip`, to set up GZIP compression, whereas for any other codec, you would manually have to specify additional options.

```dart
main() {
  var app = new Angel();

  // Calling gzip()
  app.responseFinalizers.add(gzip());

  // Easier than:
  app.responseFinalizers.add(compress('gzip', GZIP));
}
```


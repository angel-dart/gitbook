Angel can optionally run in "production mode," where several optimizations are applied to the base server,
such as running reflective dependency injection before server startup, and flattening the server's route tree.

Production mode is considered a global setting, and thus affects the behavior of several plug-ins:
* `angel_configuration` will load a `config/production.yaml` file to read configuration.
* `angel_proxy` - The `PubServeLayer` class does not serve in production mode (because you wouldn't be using `pub serve` in production)
* Many more...

To run your application in production mode, set `ANGEL_ENV` in your environment to `production`.
If you are writing a plug-in with production mode-specific code, query its value as follows:

```dart
if (app.isProduction) {
  // Do some production-only stuff...
}
```
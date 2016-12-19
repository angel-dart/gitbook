# Hooks
Another concept borrowed from FeathersJS is the concept of *hooking services*. This is a mechanism that allows you to separate concerns within your application. For example, many sites send their users confirmation e-mails after successful registration. The logic to do this is often included in the same place as the code to create the user. Hooks allow you to keep the logic for these two tasks, which are more or less unrelated, in two separate places. And what's more, this frees you up to change your service code without having to update the confirmation code in multiple places. For example, you can easily use an in-memory user store in development, and a MongoDB one in production, and use the same confirmation code for each service. So, let's take a look.

When you `use` a service class, Angel can optionally wrap it in a `HookedService` class. A `HookedService` fires events before and after its inner service runs. This opens the opportunity for events to be canceled, or have parameters modified. `use` takes a named parameter `{bool hooked: true}`. You can also affix a `@Hooked` annotation to your service class for the same effect.

This is similar to middleware, but whereas middleware only runs before requests, hooks can run before and after.

```dart
class HookedService extends Service {
  HookedServiceEventDispatcher beforeIndexed;
  HookedServiceEventDispatcher afterIndexed;

  // And so on...
}
```

A `HookedServiceEventDispatcher` has one key method you will use: `listen`. In a way, this is similar to a broadcast stream, but it has a few catches. These dispatch `HookedServiceEvent` instances, which look like this:

```dart
/// Fired when a hooked service is invoked.
class HookedServiceEvent {
  static const String INDEXED = "indexed";
  static const String READ = "read";
  static const String CREATED = "created";
  static const String MODIFIED = "modified";
  static const String UPDATED = "updated";
  static const String REMOVED = "removed";

  /// The inner service whose method was hooked.
  Service service;

  /// The name of the event being fired. This class exposes
  /// some constant Strings you can check for, to prevent typos.
  String eventName;

  /// Read-only: The `id` passed to this event, if any.
  var id;

  /// Same as `id`.
  var data;

  /// Same as `id`. Keep in mind, although this is read-only,
  /// you can still assign values within it.
  Map params;

  /// Read-only. After an event completes, this will hold whatever
  /// value the service method returned.
  var result;

  /// If you call this, no further event callbacks will be fired.
  /// If called on a 'before' hook, no further 'before' events will fire.
  /// Same for an 'after' hook.
  ///
  /// If you call this on a 'before' hook, the actual service method
  /// will never be called. Instead, `result` will be returned as the
  /// response, and any 'after' hooks will see this value as the `result`.
  ///
  /// This is very useful, and allows another way to filter or deny access to
  /// services than traditional middleware.
  void cancel(result);
}
```

The `Hooks` annotation can be used to assign hooks to service methods.

```dart
helloHook(e) => print('Hello, world!');
fooHook(e) => print('Bar');

@Hooks(const [helloHook])
class MyService extends Service {
  @Hooks(const [fooHook])
  index([params]) async {
    return ['world'];
  }
}
The simplest data store to set up is an in-memory one, as it does not require external database setup.
It only stores Maps, but it can be wrapped in a [`TypedService`](https://github.com/angel-dart/angel/wiki/TypedService).

```dart
// routes.dart
app.use('/todos', new TypedService<Todo>(new MapService()));

// todo.dart
class Todo extends Model {
  String title;
  bool completed;

  Todo({String id, this.title, this.completed : false}) {
    this.id = id;
  }
}
```
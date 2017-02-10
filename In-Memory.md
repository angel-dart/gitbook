The simplest data store to set up is an in-memory one, as it does not require external database setup.

```dart
app.use('/todos', new MemoryService<Todo>());

// todo.dart
class Todo extends Model {
  String title;
  bool completed;

  Todo({String id, this.title, this.completed : false}) {
    this.id = id;
  }
}
```

By version `1.1.0`, memory services will be able to run on both the server and client sides.
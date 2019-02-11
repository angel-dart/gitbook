Relational modeling is one of the most commonly-used features of sql databases -
after all, it *is* the namesake of the term "relational database."

Angel supports the following kinds of relations by means of annotations on fields:
* `@hasOne` (one-to-one)
* `@hasMany` (one-to-many)
* `@belongsTo` (one-to-one)

By default, the keys for columns are inferred automatically.
In the following case:

```dart
@orm
@serializable
abstract class _Wheel extends Model {
    @belongsTo
    Car get car;
}
```

The local key defaults to `car_id`, and the foreign key defaults to `id`.
You can manually override these:

```dart
@BelongsTo(localKey: 'carId', foreignKey: 'licenseNumber')
Car get car;
```

The ORM computes relationships by performing `JOIN`s, so that even complex
relationships can be fetched using just one query, rather than multiple.

## Many-to-many Relationships
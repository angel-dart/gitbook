Angel, like many other Web server frameworks, features support for object-relational mapping,
or *ORM*. ORM tools allow for conversion from database results to Dart classes.

Angel's ORM uses Dart's `build` system to generate query builder classes from your `Model` classes,
and takes advantage of Dart's strong typing to prevent errors at runtime.

Take, for example, the following class:

```dart
@orm
class _Pokemon extends Model {
    String nickName;

    int level;

    int experiencePoints;

    @belongsTo
    PokemonTrainer trainer;

    @belongsTo
    PokemonSpecies species;

    @belongsTo
    PokemonAttack attack0;

    @belongsTo
    PokemonAttack attack2;

    @belongsTo
    PokemonAttack attack3;

    @belongsTo
    PokemonAttack attack4;
}
```

`package:angel_orm_generator` will generate code that lets
you do the following:

```dart
app.get('/trainer/int:id/first_moves', (req, res) async {
    var id = req.params['id'] as int;
    var executor = req.container.make<QueryExecutor>();
    var trainer = await findTrainer(id);
    var query = PokemonQuery()..where.trainerId.equals(id);
    var pokemon = await query.get(executor);
    return pokemon.map((p) => p.attack0.name).toList();
});
```

This section of the Angel documentation consists mostly of
guides, rather than technical documentation.

For more in-depth documentation, see the actual
`angel_orm` project on Github:

https://github.com/angel-dart/orm
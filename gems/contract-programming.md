# Programmation par contrats

La programmation par contrats en D inclue un ensemble de fonctionnalités qui permettent d'améliorer la qualité du code en implémentant des tests pour s'assurer que le code se comporte comme on le souhaite. Les contrats ne sont disponibles qu'en mode **debug** et ne seront pas exécutés en mode **release**. Ils ne doivent donc pas être utilisés pour valider une entrée de l'utilisateur.

### `assert`

La forme la plus simple de programmation par contrat en D est l'utilisation d'expressions `assert(...)` qui vérifie qu'une certaine condition est remplie, et déclenchent une `AssertionError` sinon;

```d
assert(sqrt(4) == 2);
// on peut associer un message d'erreur à une expression assert
assert(sqrt(16) == 4, "sqrt ne fonctionne pas !");
```

### Les contrats de fonctions

`in` et `out` permettent de formaliser des contrats pour vérifier les entrées les sorties de fonctions.

```d
long racineCarree(long x)
in
{
    assert(x >= 0);
}
out
{
    assert((result*result) <= x
        && (result+1) * (result+1) > x);
}
body
{
    return cast(long)std.math.sqrt(cast(real)x);
}
```

Le contenu du bloc `in` pourrait aussi être placé au début du corps de la fonction, mais c'est bien plus clair de cette façon. Le bloc `out` peut capturer la valeur de la retour de la fonction peut être capturée en utilisant la syntaxe `out(result)`.

### Vérification d'invariants

`invariant()` est une méthode spéciale des `struct` et des `class` qui permet d'assurer l'intégrité d'un objet au cours de son existence.

* Elle est appellée après que le constructeur ai été appelé et avant l'appel au destructeur.
* C'est appellé avant l'appel à une méthode
* C'est appellé après l'appel à une méthode

### Valider des entrées utilisateur

Si tous les contrats sont retirés dans une version `release`, les entrées utilisateurs ne devraient pas être vérifiées dans les contrats. De plus, il est possible de mettre des expressions `assert` dans des fonctions `nothrow` puisqu'elles déclenchent des `Error`s et non des `Exception`s. L'équivalent de `assert` à l'exécution est [`std.exception.enforce`](https://dlang.org/phobos/std_exception.html#.enforce), qui lance des `Exception`s que l'ont peut intercepter et traiter.

### Pour aller plus loin

- [`assert` et `enforce` dans _Programming in D_](http://ddili.org/ders/d.en/assert.html)
- [La programmation par contrats dans _Programming in D_](http://ddili.org/ders/d.en/contracts.html)
- [La programmation par contrats dans les structs et les classes _Programming in D_](http://ddili.org/ders/d.en/invariant.html)
- [Spécification de la programmation par contrats en D](https://dlang.org/spec/contracts.html)
- [`std.exception`](https://dlang.org/phobos/std_exception.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Type qui représente une 
date façon simplifiée
Utilisez plutôt std.datetime
*/
struct Date {
    private {
        int year;
        int month;
        int day;
    }

    this(int year, int month, int day) {
        this.year = year;
        this.month = month;
        this.day = day;
    }

    invariant() {
        assert(year >= 1900);
        assert(month >= 1 && month <= 12);
        assert(day >= 1 && day <= 31);
    }

    /**
    Représentation d'une date dans le format
    AAAA-MM-JJ en chaîne de caractères
    */
    void fromString(string date)
    in {
        assert(date.length == 10);
    }
    body {
        import std.format : formattedRead;
        // formattedRead parse le format
        // fourni en entrée et écrit le résultat
        // dans des variables
        formattedRead(date, "%d-%d-%d",
            &this.year,
            &this.month,
            &this.day);
    }

    /**
    Représentation d'une date dans le format
    AAAA-JJ-MM en chaîne de caractères

    Returns: Une représentation en `string` de `Date`
    /*
    string toString() const
    out (result) {
        import std.algorithm : all, count,
                              equal, map;
        import std.string : isNumeric;
        import std.array : split;

        // vérifie qu'on retourne AAAA-MM-JJ
        assert(result.count("-") == 2);
        auto parts = result.split("-");
        assert(parts.map!`a.length`
                    .equal([4, 2, 2]));
        assert(parts.all!isNumeric);
    }
    body {
        import std.format : format;
        return format("%.4d-%.2d-%.2d",
                      year, month, day);
    }
}

void main() {
    auto date = Date(2016, 2, 7);

    // Cela fera échouer l'appel à invariant()
    // Ne validez pas les entrées utilisateurs avec
    // des contrats, utilisez plutôt des exceptions
    date.fromString("2016-13-7");

    date.writeln;
}
```

# Types fondamentaux

Un certains nombre de types fondamentaux sont définis en D, et ils ont la particularité de tous avoir la même taille **quelle que soit** la plateforme (à l'exception du type `real` qui est un nombre à virgule flottante de la plus grande précision possible).
Il n'y a pas de différences entre la taille d'un entier, que le programme soit compilé sur un système 32-bits ou 64-bits.


| type                          | taille
|-------------------------------|------------
|`bool`                         | 8-bit
|`byte`, `ubyte`, `char`        | 8-bit
|`short`, `ushort`, `wchar`     | 16-bit
|`int`, `uint`, `dchar`         | 32-bit
|`long`, `ulong`                | 64-bit

#### Types flottants:

| type    | taille
|---------|--------------------------------------------------
|`float`  | 32-bit
|`double` | 64-bit
|`real`   | >= 64-bit (généralement 64-bit, mais 80-bit sur les processeurs Intel x86 32-bits)

Le préfixe `u` désigne les types *non-signés*. `char` représente un caractère UTF-8, `wchar` est utilisé dans les chaînes de caractères UTF-16 et `dchar` est utilisé dans les chaînes de caractères UTF-32.

Une conversion entre deux variables de types différents n'est autorisée par le compilateur que si elle est possible sans perte de précision. Cependant, la conversion entre les types flottants (par exemple de `double` à `float`) est autorisée.

Une conversion vers un autre type peut être forcée en utilisant l'expression `cast(TYPE) myVar`. Il faut l'utiliser prudemment car l'expression `cast` est autorisée à casser le système de typage.

Le mot-clé `auto` crée une variable et déduit son type à partir de l'expression qu'on lui assigne. `auto myVar = 7` va déduire que `myVar` est de type `int`. Notez que comme n'importe quelle variable avec un type explicite, le type est assigné au moment de la compilation et ne peut pas être changé.

### Propriétés des types

Tous les types de données ont une propriété `.init` qui est leur valeur d'initialisation. Pour tous les entiers, cette valeur est `0` et pour tous les flottants, c'est `nan` (*not a number*).

Les types entiers et flottants ont une propriété `.max` pour la plus haute valeur qu'ils peuvent représenter. Les entiers ont également une propriété `.min` pour la plus petite valeur qu'ils peuvent représenter, là où les flottants ont une propriété `.min_normal` qui est définie comme la plus petite valeur représentable qui n'est pas 0.

Les flottants ont également une propriété `.nan` (valeur indéfinie), `.infinity` (valeur infinie), `.dig` (nombre de chiffres après la virgule de précision), `.mant_dig` (nombres de bits de la mantisse) et d'autres.

Chaque type a également une propriété `.stringof` qui retourne son nom sous la forme d'une chaîne de caractères.

### Indexes en D

En D, les indexes ont généralement le type `size_t`, un type assez grand pour représenter n'importe quelle adresse en mémoire, c'est à dire un `uint` sur les systèmes 32-bits et un `ulong` sur les systèmes 64-bits.

### Les expressions assert

`assert` est une expression qui vérifie une condition en mode `debug` et arrête l'exécution avec une `AssertionError` lorsque cette condition n'est pas remplie.
`assert(0)` est utilisée pour marquer un emplacement du code qui ne devrait pas être atteint.

### Approfondissement

#### Références basiques

- [Assignement](http://ddili.org/ders/d.en/assignment.html)
- [Variables](http://ddili.org/ders/d.en/variables.html)
- [Arithmétique](http://ddili.org/ders/d.en/arithmetic.html)
- [Flottants](http://ddili.org/ders/d.en/floating_point.html)
- [Les types fondamentaux dans _Programming in D_ (en anglais)](http://ddili.org/ders/d.en/types.html)

#### Références avancées

- [Présentation de tous les types fondamentaux en D](https://dlang.org/spec/type.html)
- [`auto` et `typeof` dans _Programming in D_ (en anglais)](http://ddili.org/ders/d.en/auto_and_typeof.html)
- [Les propriétés des types](https://dlang.org/spec/property.html)
- [Les expressions `assert`](https://dlang.org/spec/expression.html#AssertExpression)


```d
import std.stdio : writeln;

void main()
{
    // Les grands nombres peuvent être séparés
    // par un tiret du bas "_"
    // pour améliorer la lisibilité.
    int b = 7_000_000;
    short c = cast(short) b; // cast nécessaire
    uint d = b; // autorisé
    int g;
    assert(g == 0);

    auto f = 3.1415f; // f est un float

    // typeid(VAR) retourne des informations sur
    //  le type d'une expression
    writeln("le type de  f est ", typeid(f));
    double pi = f; // autorisé
    // pour les types flottants le cast
    // implicité vers des précisions inférieures
    // est autorisé.
    float demoted = pi;

    // accès aux propriétés des types
    assert(int.init == 0);
    assert(int.sizeof == 4);
    assert(bool.max == 1);
    writeln(int.min, " ", int.max);
    writeln(int.stringof); // "int"
}
```

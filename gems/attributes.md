# Attributs

On peut associer des attributs aux fonctions de plusieurs façon en D. Nous allons jeter un coup d'œil à deux attributs directement intégrés au langage mais aussi à des attributs personnalisés. Les attributs `@safe`, `@trusted` et `@system` ont déjà été mentionnés dans la première partie de la visite.

### `@property`

Une méthode marquée avec l'attribut `@property` se comporte comme un membre normal depuis l'extérieur de la classe ou de la structure:

```d
struct Foo
{
    @property bar() { return 10; }
    @property bar(int x) { writeln(x); }
}

Foo foo;
writeln(foo.bar); // appelle en fait foo.bar()
foo.bar = 10; // appelle foo.bar(10);
```

### `@nogc`

Quand une fonction est marquée avec l'attribut `@nogc`, le compilateur D nous garantit qu'aucune allocation mémoire impliquant ne sera faite dans cette fonction. Une fonction `@nogc` ne peut appeller que des fonctions `@nogc`

```d
void foo() @nogc
{
    // ERREUR:
    auto a = new A;
}
```

### Used-defined attributes (UDAs)

N'importe quelle fonction ou type en D peut être marquée par un attribut personnalisé:

```d
struct Bar { this(int x) {} }

struct Foo
{
    @("Hello") 
    {
        @Bar(10) void foo()
        {
            //...
        }
    }
}
```

N'importe quel type, fondamental ou non, peut être associé à une fonction. Dans cet exemple, la fonction `foo()` aura les attributs "Hello" (de type `string`) et `Bar` (de type `Bar` et de valeur `10`). Pour accéder à ces attributs, on utilise le *trait* (une fonctionnalité qui permet de récuper des informations sur notre programme pendant la compilation) intégré au compilateur `__traits(getAttributs, Foo)`  qui retourne un [`TypeTuple`](https://dlang.org/phobos/std_typetuple.html).

Les attributs personnalisés permettent d'améliorer la généricité du code en donnant la possibilité de générer le code le plus adapté à l'utilisation de la fonction ou de l'objet, et ce à la compilation.

### Pour aller plus loin

- [User Defined Attributes dans _Programming in D_](http://ddili.org/ders/d.en/uda.html)
- [Les attributs en D](https://dlang.org/spec/attribute.html)

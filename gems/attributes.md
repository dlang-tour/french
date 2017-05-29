# Attributs

On peut associer des attributs aux fonctions de plusieurs façon en D. Jetons un coup d'oeil à deux attributs intégrés dans le langage mais aussi à des attributs personnalisés. Il y a également les attributs `@safe`, `@trusted` et `@system` qui ont été mentionnés dans la première partie de la visite.

### `@property`

Une fonction marquée avec l'attribut `@property` ressemble à un membre normal depuis l'extérieur:

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

Quand le compilateur D rencontre une fonction qui a un attribut `@nogc`, il s'assure qu'aucune allocation de mémoire n'est faite dans cette fonction. Une fonction `@nogc` ne peut appeller que des fonctions `@nogc`

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
    @("Hello) 
    {
        @Bar(10) void foo()
        {
            //...
        }
    }
}
```

N'importe que type, fondamental ou non, peut être associé à des fonctions. La fonction `foo()` dans cet exemple aure les attributs "Hello" (de type `string`) et `Bar` (de type `Bar` et de valeur `10`). Pour accéder à ces attributs, on utilise le *trait* intégré au compilateur `__traits(getAttributs, Foo)` qui retourne un [`TypeTuple`](https://dlang.org/phobos/std_typetuple.html).

Les types personnalisés permettent d'améliorer du code générique en donnant aux types personnalisés une autre dimension, qui aide à générer du code adapté au moment de la compilation.

### Pour aller plus loin

- [User Defined Attributes dans _Programming in D_](http://ddili.org/ders/d.en/uda.html)
- [Attributs en D](https://dlang.org/spec/attribute.html)
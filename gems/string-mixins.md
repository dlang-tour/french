# String Mixins

L'expression `mixin` (__NdT: "mélanger dans"__) prend une chaîne de caractères, la compile et génère des instructions en fonction. C'est purement une mécanique de **compilation** et ne peut fonctionner que quand les chaînes de caractères sont disponibles à la compiltion. Il ne faut pas confondre cette expression avec l'instruction javascript démoniaque `eval`.

```d
mixin("int b = 5");
assert(b == 5"); // fonctionne parfaitement
```

`mixing` fonctionne aussi avec des chaînes construite dynamiquement tant qu'elles ne dépendent pas de valeurs connues uniquement à l'exécution.

`mixin` associé avec la **CTFE**, que nous verrons dans le prochain chapitre, permet d'écrire des librairies impressionantes, comme [Pegged](https://github.com/PhilippeSigaud/Pegged) qui génère un parser de grammaire à partir d'une grammaire sous forme de chaîne de caractère dans le code source.

### Pour aller plus loin

- [Les mixins en D](https://dlang.org/spec/template-mixin.html)

## {SourceCode}

```d
import std.stdio : writeln;

auto calculate(string op, T)(T lhs, T rhs)
{
    return mixin("lhs " ~ op ~ " rhs");
}

void main()
{
    // Une toute nouvelle façon de dire bonjour !
    mixin(`writeln("Hello World");`);

    // passe l'opération à effectuer en paramètre
    // de schéma.
    writeln("5 + 12 = ", calculate!"+"(5,12));
    writeln("10 - 8 = ", calculate!"-"(10,8));
    writeln("8 * 8 = ", calculate!"*"(8,8));
    writeln("100 / 5 = ", calculate!"/"(100,5));
}
```
# Alias et chaînes de caractères

Maintenant que nous savons ce que sont les tableaux, les `immutable` et que nous avons jeté un coup d'œil aux types fondamentaux, il est temps d'introduire deux nouvelles constructions en une ligne:

    alias string = immutable(char)[];

Le terme `string` est défini par une instruction `alias` qui le défini comme une tranche de `immutable(char)`s. Cela signifie que, une fois qu'une `string` a été construite, son contenu ne changera plus. Et c'est d'ailleurs la deuxième nouveauté: bienvenue à UTF-8 `string`!

Étant immutables, les `string`s peuvent parfaitement être partagées entre différents threads. Une `string` est une tranche, qu'on peut retrancher sans réallouer de mémoire. La fonction de la bibliothèque standard `std.algorithm.splitter` par exemple, peut séparer une chaîne de caractères en différentes parties séparées par un retour à ligne sans allouer de mémoire.

En plus de la chaîne de caractères UTF-8 `string`, il y a deux autres types:

```d
alias wstring = immutable(wchar)[]; // UTF-16
alias dstring = immutable(dchar)[]; // UTF-32
```

Les variantes sont facilement convertibles de l'une à l'autre en utilisant la fonction `to` de `std.conv`:

```d
dstring maDstring = to!dstring(maString);
string maString = to!string(maString);
```

### Chaînes de caractères unicode

Cela signifie qu'une `string` est définie comme un tableau de [codets](http://unicode.org/glossary/#code_unit) unicode 8-bits. Tous les opérations sur les tableaux peuvent être utilisées sur les chaînes de caractères, mais elles travailleront au niveau des codets et pas au niveau des caractères.
De la même façon, les algorithmes de la bibliothèque standard traiteront les `string`s comme des séquences de [points de code](http://unicode.org/glossary/#code_point), ou éventuellement comme une séquence de [graphèmes](http://unicode.org/glossary/#grapheme) si on utilise [`std.uni.byGrapheme`](https://dlang.org/library/std/uni/by_grapheme.html).

Ce petit example illustre ces différences:

```d
string s = "\u0041\u0308"; // Ä

writeln(s.length); // 3

import std.range : walkLength;
writeln(s.walkLength); // 2

import std.uni : byGrapheme;
writeln(s.byGrapheme.walkLength); // 1
```

Ici, la longueur du tableau `s` est 3, parce qu'il contient 3 codets : `0x41`, `0x03` et `0x08`. Ces deux derniers définissent un seul point de code et [`walkLength`](https://dlang.org/library/std/range/primitives/walk_length.html) (fonction de la librairie standard qui permet de calculer la longueur d'une range quelconque) compte deux points de code au total. Finalement, `byGrapheme` réalise des calculs plutôt couteux pour déterminer que ces deux points de code forme un seul caractère affiché.

Traiter correctement Unicode peut être très compliqué, mais la plupart du temps, les développeurs D considèrent les variables `string` comme des tableaux magiques et se basent sur la librairie standard. Si l'on veut itérer sur les codets d'une `string`, on peut utiliser la fonction [`byCodeUnit`](http://dlang.org/phobos/std_utf.html#.byCodeUnit).

L'auto-décodage en D est expliqué plus en détail dans le chapitre dédié à l'unicode, plus loin dans ce tutoriel.

### `string`s multi-lignes

Pour créer une `string` multi-ligne, on peut utiliser la syntaxe `string chaine = q{...}`:

```d
string multiligne = q{ Ceci
    pourrait être un
    long document
};
```

### `string`s brutes

Il est également possible d'utiliser les `string`s brutes pour éviter le laborieux travail d'échappement des caractères spéciaux. Les `string`s brutes peuvent être déclarées soit avec des *backticks* (`` ` ... ` ``) ou avec avec le préfixe "r" (`r"..."`).

```d
string brute = `"string" brute`; // "string" brute
string brute2 = r""string" brute"; // "string" brute
```

### Aller plus loin

- [Caractères dans _Programming in D_](http://ddili.org/ders/d.en/characters.html)
- [`string` dans  _Programming in D_](http://ddili.org/ders/d.en/strings.html)
- [std.utf](http://dlang.org/phobos/std_utf.html) - Algorithmes d'encodage et de décodage UTF
- [std.uni](http://dlang.org/phobos/std_uni.html) - Algorithmes Unicode

## {SourceCode}

```d
import std.stdio : writeln, writefln;
import std.range : walkLength;
import std.uni : byGrapheme;
import std.string : format;

void main() {
    // format génère une `string` en utilisant
    // une syntaxe similaire à celle de printf.
    // D permet de gérer les chaînes de 
    // caractères UTF nativement
    string str = format("%s %s", "Hellö",
        "Wörld");
    writeln("Ma chaîne: ", str);
    writeln("Longueur du tableau " ~
        "(nombre de codets)"
        ~ " de la chaîne: ", str.length);
    writeln("Longueur de la range "
        ~ "(nombre de points de code)"
        ~ " de la chaîne: ", str.walkLength);
    writeln("Nombre de caractères " 
        ~ "(nombre de graphèmes)"
        ~ " de la chaîne: ",
        str.byGrapheme.walkLength);

    // Les strings sont des tableaux normaux, 
    // donc n'importe quelle opération qui 
    // fonctionne sur les tableaux fonctionne 
    // ici aussi !
    import std.array : replace;
    writeln(replace(str, "lö", "lo"));
    import std.algorithm : endsWith;
    writefln("Est-ce que %s fini par 'rld'? %s",
        str, endsWith(str, "rld"));

    import std.conv : to;
    // Converti en UTF-32
    dstring dstr = to!dstring(str);
    // Qui bien sûr semble identique!
    writeln("Ma dstring: ", dstr);
}
```

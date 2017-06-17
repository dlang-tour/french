# Algorithmes de ranges

Les modules de la librairie standard [std.range](http://dlang.org/phobos/std_range.html) et [std.algorithm](http://dlang.org/phobos/std_algorithm.html) fournissent une multitude de fonctions qui peuvent être composées pour former des opérations complexes de façon lisible, et ce en se basant sur les *ranges*.

Le grand avantage de ces algorithmes est qu'il suffit de définir votre propre range et vous pourrez directement profiter de toute la puissance de la librairie standard.

### std.algorithm

`filter` — À partir d'un lambda en paramètre de modèle, génère une nouvelle range d'éléments qui retournent vrai quand passés au lambda:

```d
filter!"a > 20"(range);
filter!(a => a > 20)(range);
```

`map` — Génère une nouvelle range où le prédicat passé en paramètre de schéma est appliqué à tous les éléments:

```d
[1,2,3].map!(x => to!string(x));
```

`each` — Une alternative à `foreach` pour appliquer une opération sur chaque élément d'une range:

```d
[1,2,3].each!(a => writeln(a))
```

### std.range

`take` — retourne *N* éléments d'une range:

```d
laTresTresGrandeRange.take(10);
```

`zip` — itère sur deux ranges en parallèle et retourne un tuple des deux éléments pendant son itération:

```d
assert(zip([1,2], ["coucou", "vous"]).front
    == tuple(1, "hello"));
```

`generate` — prend une fonction et crée une range qui appelle la fonction à chaque itération, par exemple:

```d
alias RandomRange = generate!(x => uniform(1, 1000));
```

`cycle` — retourne une range qui répète la range fournie en entrée à l'infinie:

```d
auto c = cycle([1]);
// la range ne sera jamais vide!
assert(!c.empty);
```

### La documentation est la référence absolue pour découvrir d'autres fonctions

### Pour aller plus loin

- [Les ranges dans _Programming in D_](http://ddili.org/ders/d.en/ranges.html)
- [Plus sur les ranges dans _Programming in D_](http://ddili.org/ders/d.en/ranges_more.html)

## {SourceCode}

```d
// Allez, on récupère toute l'armada !
import std.algorithm : canFind, map,
  filter, sort, uniq, joiner, chunkBy, splitter;
import std.array : array, empty;
import std.range : zip;
import std.stdio : writeln;
import std.string : format;

void main()
{
    string text = q"<Ceci vous donnera un aperçu
de la puissance et de l'expressivité de ce
langage de programmation système, qui compile
vers des exécutables binaires performants>";

    // prédicat de séparation
    alias pred = c => canFind(" ,.\n", c);
    // comme n'importe quel bon algorithme,
    // il fonctionne paresseusement sans
    // allouer de mémoire!
    auto words = text.splitter!pred
      .filter!(a => !a.empty);

    auto wordCharCounts = words
      .map!"a.count";

    // Affiche le nombre de caractères
    // par mot d'une belle façon
    // en commençant par ceux qui ont
    // le moins de caractères
    zip(wordCharCounts, words)
      // conversion en tableau pour trier
      .array()
      .sort()
      // on n'a pas besoin de mots dupliqués
      // hein ?
      .uniq()
      // met tous les mots qui ont le même 
      // nombre de caractères dans une
      // seule colonne. 
      // chunkBy nous aide en générant des 
      // ranges de ranges.
      .chunkBy!(a => a[0])
      // Ces éléments seront liés en
      // une seule ligne
      .map!(chunk => format("%d -> %s",
          chunk[0],
          // juste les mots
          chunk[1]
            .map!(a => a[1])
            .joiner(", ")))
      // joiner lie, mais parésseusement
      // et maintenant les lignes avec
      // la générateur de ligne
      .joiner("\n")
      // imprimer vers stdout
      .writeln();
}
```

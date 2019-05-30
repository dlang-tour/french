# Traits

Une des forces de D est son système d'évaluation de fonction à la compilation (CTFE). En le combinant avec de l'introspection, on peut écrire des programmes très génériques et très optimisés.

## Contrats explicites

Les traits nous permettent de spécifier explicitement quel type de paramètres de schémas sont acceptés. Par exemple, `separerEnMots` peut travailler avec n'importe quel type de `string`:

```d
S[] splitIntoWord(S)(S input)
if (isSomeString!S)
```

Cela s'applique également à d'autres types de paramètres de schémas et `maSurcouche` peut s'assurer que la fonction passée en paramètre est une fonction appelable:

```d
void myWrapper(alias f)
if (isCallable!f)
```

Nous allons examiner un exemple simple: [`commonPrefix`](https://dlang.org/phobos/std_algorithm_searching.html#.commonPrefix) de `std.algorithm.searching`, qui retourne le préfixe commun de deux ranges.

```d
auto commonPrefix(alias pred = "a == b", R1, R2)(R1 r1, R2, r2)
if (isForwardRange!R1
    isInputRange!R2 &&
    is(typeof(binaryFun!pred(r1.front, r2.front)))) &&
    !isNarrowString!R1)
```

Cela signifie que la fonction ne peut être appelée (et donc instanciée et compilée) si:

- `r1` est copiable (garanti par `isForwardRange`)
- `r2` est itérable (garanti par `isInputRange`)
- `pred` est appelable avec `r1` et `r2` en paramètres
- `r1` n'est pas une chaîne de caractères (`char[]`, `string`, `wchar[]` ou `wstring`) — pour la simplicité, puisqu'il y aurait peut être besoin de décodage

### Spécialisation

Beaucoup d'APIs veulent être les plus générales possibles, mais elles ne veulent pas que cette généralisation rende leur code plus lent à s'exécuter.
Avec la puissance de l'introspection et de la CTFE, il est possible de spécialiser une méthode à la compilation pour obtenir les meilleures performances en fonction du type d'entrée.

Un problème courant consiste à différencier les tableaux dont on connait exactement la longueur, et les suites d'éléments que l'on doit parcourir pour en connaître la longueur.
Voilà une implémentation simple de la fonction `walkLength` de `std.range` qui s'applique à n'importe quel type itérable:

```d
static if (hasMember!(r, "length"))
    return r.length; // O(1)
else
    return r.walkLength; // O(n)
```

### `commonPrefix`

L'utilisation de l'introspection se retrouve partout dans Phobos. Par exemple, `commonPrefix` fait la différence entre les `RandomAccessRange`s et les objets sur lesquels on peut itérer linéairement, parce qu'avec une `RandomAccessRange`, on peut passer d'une position à une autre, et donc accélerer l'algorithme.

### Encore plus de magie CTFE

[std.traits](https://dlang.org/phobos/std_traits.html) englobe la majorité des [*traits*](https://dlang.org/spec/traits.html) de D, à l'exception de certains qui ne peuvent pas être englobés dans la librairie, comme `compiles` par exemple, qui peut causer des erreurs de compilation:

```d
__traits(compiles, obvious error - $%42); // false;
```

#### Mot-clés

Pour aider au débugage, D fournit quelques mots clés spéciaux:

```d
void test(string file = __FILE__, size_t line = __LINE__, string mod = __MODULE__,
          string func = __FUNCTION__, string pretty = __PRETTY_FUNCTION__)
{
    writefln("file: '%s', line: '%s', module: '%s',\nfunction: '%s', pretty function: '%s'",
             file, line, mod, func, pretty);
}
```

Avec l'évaluation de code D en ligne de commande, on peut même jeter son horloge !

```d
rdmd --force --eval='pragma(msg, __TIMESTAMP__);'
```

## Pour aller plus loin

- [std.range.primitives](https://dlang.org/phobos/std_range_primitives.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [std.meta](https://dlang.org/phobos/std_meta.html)
- [Spécifications des *traits* en D](https://dlang.org/spec/traits.html)

## {SourceCode}

```d
import std.functional : binaryFun;
import std.range.primitives : empty, front,
    popFront,
    isInputRange,
    isForwardRange,
    isRandomAccessRange,
    hasSlicing,
    hasLength;
import std.stdio : writeln;
import std.traits : isNarrowString;

 /**
 Retourne le préfixe commun de deux ranges
 sans décoder dans les cas particuliers

 Params:
    pred = prédicat pour comparer les éléments
    r1 = Une forward range d'éléments
    r2 = une input range d'éléments

 Returns:
 Une tranche de r1 qui contient les caractères
 par lesquels commencent les deux ranges
 */
auto commonPrefix(alias pred = "a == b", R1, R2)
                 (R1 r1, R2 r2)
if (isForwardRange!R1 && isInputRange!R2 &&
    !isNarrowString!R1 &&
    is(typeof(binaryFun!pred(r1.front,
                             r2.front))))
{
    import std.algorithm.comparison : min;
    static if (isRandomAccessRange!R1 &&
               isRandomAccessRange!R2 &&
               hasLength!R1 && hasLength!R2 &&
               hasSlicing!R1)
    {
        immutable limit = min(r1.length,
                              r2.length);
        foreach (i; 0 .. limit)
        {
            if (!binaryFun!pred(r1[i], r2[i]))
            {
                return r1[0 .. i];
            }
        }
        return r1[0 .. limit];
    }
    else
    {
        import std.range : takeExactly;
        auto result = r1.save;
        size_t i = 0;
        for (;
             !r1.empty && !r2.empty &&
             binaryFun!pred(r1.front, r2.front);
             ++i, r1.popFront(), r2.popFront())
        {}
        return takeExactly(result, i);
    }
}

void main()
{
    // affiche: "hello, "
    writeln(commonPrefix("hello, world"d,
                         "hello, there"d));
}
```

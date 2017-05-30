# Traits

Une des force de D est sont système d'évaluation de fonction à la compilation (CTFE). En le combinant avec de l'introspection, on peut écrire des programmes très génériques et très optimisés.

## Contrats expicites

Les traits nous permettent de spécifier explicitement quel type de paramètres de schémas sont acceptés. Par exemple, `separerEnMots` peut travailler avec n'importe quel type de `string`:

```d
S[] splitIntoWord(S)(S input)
if (isSomeString!S)
```

Cela s'applique également à d'autres types de paramètres de schémas et `maSurcouche` peut s'assurer que la fonction passée en paramètre est une fonction appellable:

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

Cela signifie que la fonction ne peut être appellée (et donc instanciée et compilée) si:

- `r1` est copiable (garanti par `isForwardRange`)
- `r2` est itérable (garanti par `isInputRange`)
- `pred` est appelable avec `r1` et `r2` en paramètres
- `r1` n'est pas une chaîne de caractères (`char[]`, `string`, `wchar[]` ou `wstring`) — pour la simplicité, puisqu'il y aurait peut être besoin de décodage
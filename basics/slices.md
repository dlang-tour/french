# Tranches

Les tranches sont des objets de type `T[]` pour n'importe quel type `T`.
Les tranches sont une abstraction qui représentent une partie d'un tableau de valeur de type `T`, ou le tableau en entier.
**Les tranches et les tableaux dynamiques sont identiques.**

Une tranche contient deux membres : un pointeur vers l'élément de départ et la longueur de la tranche :

```d
T* ptr;
size_t length;
```

### Allouer une tranche avec `new`

Si un nouveau tableau dynamique est créé, une tranche qui pointe vers son espace mémoire est retournée:

```d
auto tab = new int[5];
assert(tab.length == 5); // mémoire référencée par tab.ptr
```

Dans ce cas, la mémoire allouée est gérée par le ramasse-miettes. La tranche retournée agit comme une "vue" des éléments sous-jacents.

### Récupérer une tranche vers une zone mémoire existante

Utiliser l'opérateur de *tranchage* permet de récupérer une tranche pointant vers une zone mémoire qui a déjà été allouée. L'opérateur de tranchage peut être appliqué à une autre tranche, à un tableau statique, à des `structs` ou des classes qui implémentent la méthode `opSlice` et à quelques autres entités.

Dans une expression `origine[start..end]`, l'opérateur de tranchage est utilisé pour obtenir une tranche de tous les élements d'`origine` de `start` à l'élément **avant** `end`:

```d
auto newTab = tab[1 .. 4]; // l'élément d'indexe 4 n'est PAS inclus
assert(newTab.length == 3);
newTab[0] = 10; // change newTab[0], soit tab[1]
```

Ces tranches créent une nouvelle vue sur une zone de mémoire déjà existante. Elles ne créent pas une nouvelle copie. S'il n'y a plus de tranche qui référence cette zone de mémoire, ou sur une partie de cette zone, elle sera libérée par le ramasse-miettes.

En utilisant les tranches, il est possible d'écrire du code très efficace pour des choses (les parsers par exemple) qui n'opèrent que sur une zone mémoire, et tranche uniquement la partie sur laquelle elle doit travailler. De cette façon, on évite d'allouer des nouveaux blocs en mémoire.

Comme nous l'avons vu dans la partie précédente, l'expression `[$]` est une forme raccourcie de `tab.length`. Donc `tab[$]` représente l'élément juste après le dernier de la tranche, et générerait une `RangeError` (si les vérifications d'accès par indexe n'ont pas été désactivées).

### Pour aller plus loin

- [Introduction aux tranches (slices) en D](http://dlang.org/d-array-article.html)
- [Les tranches dans _Programming in D_](http://ddili.org/ders/d.en/slices.html)

## {SourceCode}

```d
import std.stdio : writefln;

/**
Calcule le minimum de toutes les valeurs
de la tranche récursivement. À chaque appel
récursif, une sous-tranche est utilisée et donc
on ne créé pas de copies et on n'alloue pas
de mémoire.
*/
int minimum(int[] slice)
{
    assert(slice.length > 0);
    if (slice.length == 1)
        return slice[0];
    auto otherMin = minimum(slice[1 .. $]);
    return slice[0] < otherMin ?
        slice[0] : otherMin;
}

void main()
{
    int[] test = [ 3, 9, 11, 7, 2, 76, 90, 6 ];
    auto min = minimum(test);
    writefln("Le minimum de %s est %d",
        test, min);
    assert(min == 2);
}
```

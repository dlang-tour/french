# Foreach

{{#img-right}}dman-teacher-foreach.jpg{{/img-right}}

En D, on peut utiliser les boucles `foreach` qui sont
plus lisibles et réduisent les possibilités d'erreurs.

### Itération sur des éléments

Avec un tableau `tab` de type `int[]`, il est possible
d'itérer sur ses éléments en utilisant une boucle 
`foreach`:

```d
foreach(int e; tab)
{
    writeln(e);
}
```
 Le premier champ de l'appel à `foreach` est le nom de
 la variable qui sera utilisée dans l'itération. Son 
 type peut être déduit automatiquement:

 ```d
 foreach(e; tab)
 {
     assert(typeof(e).stringof == "int"); // e est un int
 }
 ```

 Le second champ doit être un tableau, ou un objet spécial
 sur lequel on peut itérer, qu'on appelle **range** et
 qu'on introduira dans le prochain chapitre.

 ### Accès par référence

 Les éléments seront copiés depuis le tableau ou la range
 pendant l'itération. C'est acceptable pour des types
 fondamentaux mais peut devenir très problématique pour des
 types plus grands. Pour empêcher la copie ou pour pouvoir
 modifier les éléments dans la boucle, `ref` peut être 
 utilisé:

 ```d
 foreach(ref e; tab)
 {
     e = 10; // overwrite value
 }
 ```

 ### Itérer `n` fois

 D permet d'écrire des boucles qui doivent être exécutés
 `n` fois de manière concise avec la syntaxe `..`:

 ```d
 foreach(i; 0..3)
 {
     writeln(i);
 }
 // 0 1 2
 ```

 Le dernier nombre dans `a..b` est exclu de l'intervalle,
 cette boucle est donc éxecuté `3` fois.

 ### Itération avec compteur d'index

 Pour les tableaux, il est également possible d'accéder
 à l'index en le stockant dans une variable:

 ```d
 foreach(i, e; [4,5,6])
 {
     writeln(i, ":", e);
 }
 // 0:4 1:5 2:6
 ```

 ### Itération inversée avec `foreach_reverse`

 On peut itérer sur une collection à l'envers avec
 `foreach_reverse`:

 ```d
 foreach_reverse (e; [1,2,3])
 {
     writeln(e);
 }
 // 3 2 1
 ```

 ### Pour aller plus loin

 - [`foreach` dans _Programming in D_](http://ddili.org/ders/d.en/foreach.html)
- [`foreach` avec des `structs` and des classes  _Programming in D_](http://ddili.org/ders/d.en/foreach_opapply.html)
- [spécification de `foreach`](https://dlang.org/spec/statement.html#ForeachStatement)

## {SourceCode}

```d
import std.stdio : writefln;

void main() {
    auto tab = [
        [5, 15],      // 20
        [2, 3, 2, 3], // 10
        [3, 6, 2, 9], // 20
    ];

    foreach (i, ligne; tab)
    {
        double total = 0.0;
        foreach (e; ligne)
            total += e;

        auto moyenne = total / ligne.length;
        writefln("moyenne [ligne=%d]: %.2f", i, moyenne);
    }
}
```
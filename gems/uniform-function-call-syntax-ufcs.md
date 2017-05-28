# Uniform Function Call Syntax (UFCS)

L'**UFCS** est une des fonctionnalités clés de D, qui permet la réutilisabilité du code avec une bonne encapsulation.

L'UFCS permet d'appeller n'importe quelle fonction `fun(a)` avec la syntaxe `a.fun()`.

Si le compilateur évalue l'expression `a.fun()` et que le type de `a` n'a pas de méthode appellée `fun()`, il essaye de trouver une fonction globale dont le premier paramètre a le même type que `a`.

Cette fonctionnalité est particulièrement utile quand on veut enchainer les appels à des fonctions. Au lieu d'écrire

```d
foo(bar(a));
```

On peut écrire

```d
a.bar().foo();
```

De plus, les parenthèses sont optionelles quand une fonction est appellée sans arguments, ce qui signifie que _n'importe quelle_ fonction peut être utilisée comme une propriété:

```d
import std.uni : toLower;
"D est génial !".toLower; // "d est génial !"
```

L'UFCS est particulièrmeent important quand on travaille avec les *ranges*, sur lesquelles on peut enchainer plusieurs opérations complexes, avec un style clair et maintenable.

```d
import std.algorithm : group;
import std.range : chain, retro, front;

[1,2].chain([3,4]).retro; // 4, 3, 2, 1
[1,1,2,2,2].group.dropOne.front; // tuple(2, 3u);
```

### Pour aller plus loin

- [L'UFCS dans _Programming in D_](http://ddili.org/ders/d.en/ufcs.html)
- [_Uniform Function Call Syntax_](http://www.drdobbs.com/cpp/uniform-function-call-syntax/232700394) par Walter Bright
- [`std.range`](http://dlang.org/phobos/std_range.html)

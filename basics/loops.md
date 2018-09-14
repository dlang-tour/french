# Boucles

Il existe 4 types de boucles en D.

### 1) `while`

Une boucle `while` exécute un bloc d'instructions tant qu'une certaine condition est remplie :

```d
while (condition)
{
    nimportequoi();
}
```

### 2) `do ... while`

Une boucle `do ... while` exécute un bloc d'instructions tant qu'une certaine condition est remplie, mais contrairement à une boucle `while`, le bloc de code est exécuté au moins une fois avant que la condition ne soit vérifiée.

```d
do
{
    nimportequoi();
} while (condition);
```

### 3) Boucle `for` classique

La boucle classique `for` que l'on peut retrouver en C/C++ ou en Java avec un _initialiseur_, une _condition de boucle_ et une _instruction de boucle_:

```d
for(int i = 0; i < tab.length; i++)
{
    //...
}
```

### 4) `foreach`

La boucle `foreach` qui sera expliquée plus en détail dans un prochain chapitre :

```d
foreach(elem; tab)
{
    //...
}
```

#### Mot-clés et labels

La mot-clé `break` arrête immédiatement la boucle courante. Dans une boucle imbriquée, un _label_ peut être utilisé pour arrêter une boucle externe :

```d
outer: for(int i = 0; i < 10; i++)
{
    for(int j = 0; j < 5; j++)
    {
        //...
        break outer;
    }
}
```

Le mot-clé `continue` commence la prochaine itération de la boucle.

### Pour aller plus loin

- La boucle `for` dans [_Programming in D_](http://ddili.org/ders/d.en/for.html), [spécification](https://dlang.org/spec/statement.html#ForStatement)
- La boucle `while` dans [_Programming in D_](http://ddili.org/ders/d.en/while.html), [spécification](https://dlang.org/spec/statement.html#WhileStatement)
- La boucle `do-while` dans [_Programming in D_](http://ddili.org/ders/d.en/do_while.html), [sp"cification](https://dlang.org/spec/statement.html#do-statement)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Calcule la moyenne des
éléments d'un tableau.
*/
double average(int[] array) {
    // La propriété .empty pour les tableaux
    // n'est pas accessible par défaut en D
    // mais est contenue dans le package 
    // std.array
    import std.array : empty, front;

    double accumulator = 0.0;
    auto length = array.length;
    while (!array.empty) {
        // On pourrait aussi faire ça 
        // avec .front en l'important avec :
        // import std.array : front;
        accumulator += array[0];
        array = array[1 .. $];
    }

    return accumulator / length;
}

void main()
{
    auto testers = [ [5, 15], // 20
          [2, 3, 2, 3], // 10
          [3, 6, 2, 9] ]; // 20

    for (auto i = 0; i < testers.length; ++i) {
      writeln("Le moyenne de ", testers[i],
        " = ", average(testers[i]));
    }
}
```

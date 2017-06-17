# Les fibres

**Les fibres** sont un moyen d'implémenter la concurrence de façon *collaborative*. La classe `Fiber` est définie dans le module [`core.thread`](https://dlang.org/phobos/core_thread.html).

L'idée de base est que quand une fibre n'a rien à faire ou qu'elle attend qu'on lui passe des données, elle le signale explicitement en appelant `Fiber.yield()`. Le contexte parent reprend alors le contrôle de l'exécution, mais l'état de la fibre, et de toutes ses variables est sauvegardé et continuera après l'appel à `Fiber.yield()`. Magique ? Oui.

```d
void foo()
{
    writeln("Bonjour");
    Fiber.yield();
    writeln("Tout le monde");
}
//...
auto f = new Fiber(&foo);
f.call(); // affiche Bonjour
f.call(); // affiche Tout le monde
```

Cette fonctionnalité peut être utilisée pour implémenter de la concurrence où plusieurs fibres collaborent sur un seul coeur. L'avantage des fibres comparées aux threads est qu'elles consomment moins de ressources, puisqu'il n'y a pas besoin de passer d'un contexte à un autre.

Un très bon usage de cette technique peut être vu dans la bibliothèque [vibe.d](http://vibed.org) qui implémente des opérations d'E/S asynchrones avec des fibres, ce qui rend le code très propre.

### Pour aller plus loin

- [Les fibres dans _Programming in D_](http://ddili.org/ders/d.en/fibers.html)
- [La documentation de core.thread.Fiber](https://dlang.org/library/core/thread/fiber.html)

## {SourceCode}

```d
import core.thread : Fiber;
import std.stdio : write;
import std.range : iota;

/*
Itère sur une range et applique la fonction
Fnc à tous les éléments de x et retourne le
résultat dans result. La fibre libère
l'exécution après chaque application
*/
void fiberedRange(alias Fnc, R, T)(
    R range,
    ref T result)
{
    for(; !range.empty; range.popFront) {
        result = Fnc(range.front);
        Fiber.yield();
    }
}

void main()
{
    int squareResult, cubeResult;
    // Crée une fibre qui est initialisée
    // avec un delegate qui génère une range
    // de carrés
    auto squareFiber = new Fiber({
        fiberedRange!(x => x*x)(
            iota(1,11), squareResult);
    });
    // .. et ici qui génère des cubes!
    auto cubeFiber = new Fiber({
        fiberedRange!(x => x*x*x)(
            iota(1,9), cubeResult);
    });

    // si l'état est TERM, alors la fibre a fini
    // d'exécuter la fonction
    squareFiber.call();
    cubeFiber.call();
    while (squareFiber.state
        != Fiber.State.TERM && cubeFiber.state
        != Fiber.State.TERM)
    {
        write(squareResult, " ", cubeResult);
        squareFiber.call();
        cubeFiber.call();
        write("\n");
    }
}
```

# Stockage par thread

L'attribut `static` permet de déclarer des objets qui ne sont initialisés qu'une seule fois. Si la même ligne est exécutée une deuxième fois, l'initialisation sera ignorée. Chaque thread a son propre objet `static` (*SPT - stockage par thread*) et ne pourra pas modifier les variables statiques d'un autre thread, même si elles portent le même nom. `static` permet donc de déclarer des objets globaux pour le thread *courant*.

C'est différent de Java et C/C++ par exemple, où `static` signifie global pour toute l'application, entrainant des problèmes de synchronisations dans les programmes multi-threadés.

La valeur assignée à une variable `static` doit être évaluable à la compilation. Elle ne doit pas être dépendante de l'exécution! Il est possible d'initialiser les variables `static` à l'exécution en utilisant `static this()`, un constructeur unique pour les structures, les classes et les modules.

```d
static int b = 42;
// b n'est initialisé qu'une fois
// Quand le programme est exécuté sur plusieurs
// threads, chaque thread aura sa propre variable 
// "b" qui n'interférera pas avec celle des autres
```

On peut déclarer une variable globale "classique" que tous les threads peuvent lire et modifier avec l'attribut `__gshared` qui est l'équivalent de `static` en C. C'est un nom laid pour rappeler au développeur de ne pas trop l'utiliser.

```d
__gshared int b = 50;
// Aussi initialisé qu'une fois !
// Un b vraiment global que tous les threads
// peuvent lire et, ce qui le rend 
// dangereux, modifier
```

### Pour aller plus loin

- [Thread-local storage sur Wikipedia](https://fr.wikipedia.org/wiki/Thread_local_storage)

## {SourceCode}

```d
import std.concurrency : spawn, thisTid;

void worker(bool firstTime)
{
    import std.stdio : writeln;
    // laStatique est global dans le thread
    // courant uniquement. Aucun autre thread
    // ne pourra y accéder. Notez qu'elle n'est
    // initialisée que la première fois où cette
    // fonction est exécutée.
    static int laStatique = 0;
    writeln("Thread ", thisTid,
        ": laStatique = ", laStatique++);
    if (firstTime)
        worker(false);
}

void main()
{
    // Crée cinq threads qui appellent chacun 
    // worker(true, i)
    for (size_t i = 0; i < 5; ++i) {
        spawn(&worker, true);
    }
}
```

# Synchronisation et partage

Même si la façon recommandée de faire du parallélisme en D est de se baser sur des données `immutable` et de synchroniser les threads avec le passage de messages, le langage supporte nativement des primitives de *synchronisation* et un attribut de type `shared` pour marquer les objets qui sont accessibles par plusieurs threads:

```d
shared(int)* p = new int;
int * t = p; // ERREUR
```

Par exemple, `std.concurrency.send` ne permet que d'envoyer des données `immutable` ou `shared` par passage de messages.  `shared` est transitif, donc si une classe ou un structure est marquée comme `shared`, tous ses membres le seront aussi. Notez que les variables `static` ne sont pas `shared` par défault parce qu'elles sont implémentées avec le stockage sur thread local (STL) et que chaque thread a une copie de la variable.

Les blocs `synchronized` permettent de demander au compilateur de créer une section critique où ne peut entrer qu'un seul thread à la fois.

```d
synchronized
{
    importTruc();
}
``` 

Dans les méthodes de `class`, on peut associer plusieurs *mutexs* à ces blocs avec la syntaxe `synchronized(membre1, membre2)` pour réduire les risques de conflits. Le D insère automatiquement les *sections critiques*. Une classe complète peut être marqué comme `synchronized` et le compilateur s'assurera qu'un seul thread accède à une instance de la classe à la fois.

Des opérations atomiques peuvent être effectuées sur des variables `shared` en utilisant la fonction `core.atomic.atomicOp`:

```d
shared int test = 5;
test.atomicOp!"+="(4);
```

### Pour aller plus loin

- [Concurrence par partage de données dans _Programming in D_](http://ddili.org/ders/d.en/concurrency_shared.html)
- [L'attribut de type `shared`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=11)
- [La synchronisation par verrous avec `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=13)
- [Interblocages et `synchronized`](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=15)
- [Spécification de `synchronized` en D](https://dlang.org/spec/statement.html#SynchronizedStatement)
- [Conversion implicite de types `shared`](https://dlang.org/spec/const3.html#implicit_conversions)

## {SourceCode}

```d
import std.concurrency : receiveOnly, send,
    spawn, Tid, thisTid;
import core.atomic : atomicOp, atomicLoad;

/*
Une file d'attente qui peut être partagée de 
façon sûre par plusieurs threads. Tous les accès
à une instance sont automatiquement verrouillés
grâce au mot clé `synchronized`
*/
synchronized class SafeQueue(T)
{
    // Note: les éléments doivent être privés
    // dans une classe synchronisée
    private T[] elements;

    void push(T value) {
        elements ~= value;
    }

    /// Retourne T.init si la file est vide
    T pop() {
        import std.array : empty;
        T value;
        if (elements.empty)
            return value;
        value = elements[0];
        elements = elements[1 .. $];
        return value;
    }
}

/*
Affichage un message de façon sûré,
indépendament du nombre de threads. Notez que
les paramètres variadiques sont utilisés pour
args! Cela signifie que args peut être entre 0
et N paramètres.
*/
void safePrint(T...)(T args)
{
    // Exécuté par un seul thread
    synchronized {
        import std.stdio : writeln;
        writeln(args);
    }
}

void threadProducer(shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    import std.range : iota;
    // Met des valeurs de 1 à 11 dans la file
    // d'attente
    foreach (i; iota(1,11)) {
        queue.push(i);
        safePrint("Pushed ", i);
        atomicOp!"+="(*queueCounter, 1);
    }
}

void threadConsumer(Tid owner,
    shared(SafeQueue!int) queue,
    shared(int)* queueCounter)
{
    int popped = 0;
    while (popped != 10) {
        auto i = queue.pop();
        if (i == int.init)
            continue;
        ++popped;
        // récupère de façon sûre la valeur
        // de queueCounter en utilisant
        // atomicLoad
        safePrint("Récupéré ", i,
            " (Le consommateur a poussé ",
            atomicLoad(*queueCounter), ")");
    }

    // J'ai fini !
    owner.send(true);
}

void main()
{
    auto queue = new shared(SafeQueue!int);
    shared int counter = 0;
    spawn(&threadProducer, queue, &counter);
    auto consumer = spawn(&threadConsumer,
                    thisTid, queue, &counter);
    auto stopped = receiveOnly!bool;
    assert(stopped);
}
```
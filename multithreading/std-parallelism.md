# std.parallelism

Le module `std.parallelism` fourni des primitives de haut-niveau pour faciliter la programmation concurrente.

### parallel

[`std.parallelism.parallel`](http://dlang.org/phobos/std_parallelism.html#.parallel) permet de distribuer automatiquement le corps d'un `foreach` sur différents threads:

```d
// mise au carré d'un tableau en parallèle
auto tab = iota(1,100).array;
foreach(ref i; parallel(tab))
{
    i = i*i;
}
```

`parallel` utilise l'opérateur `opApply` en interne. La méthode `parallel` est un raccourci pour `taskPool.parallel` qui est une `TaskPool` qui utilise *le nombre de cpus - 1* threads. Créer sa propre instance de l'objet permet de contrôler le degré de parallélisme.

Il faut faire attention à ce que le corps de l'itération de `parallel` ne modifie pas des variables que d'autres threads pourraient utiliser.

L'option `workingUnitSize` spécifie le nombre d'éléments gérés par chaque thread.

### reduce

La fonction [`std.algorithm.iteration.reduce`](http://dlang.org/phobos/std_algorithm_iteration.html#reduce), connue dans d'autres langages fonctionelles sous le nom de *accumulate* ou de *fold*, appelle la fonction `fun(acc, x)` pour chaque élément `x` où `acc` est le résultat de l'appelle précédent:

```d
// 0 est la valeur de départ
auto sum = reduce!"a + b"(0, elements);
```

[`Taskpool.reduce`](http://dlang.org/phobos/std_parallelism.html#.TaskPool.reduce) est une version parallèle de `reduce`:

```d
// Détermine la somme des éléments d'une range
// en parallèle, en utilisant le premier élément
// de la range comme valeur de départ de acc
auto sum = taskPool.reduce!"a + b"(nums);
```

`TaskPool.reduce` sépare la range en sous-ranges qui sont réduites en parallèle. Une fois que ces résultats ont été calculés, les résultats sont réduits eux-mêmes.

### `task()`

[`task`](http://dlang.org/phobos/std_parallelism.html#.task) est une fonction qui permet d'appeler une autre fonction, qui pourrait prendre longtemps ou qui devrait être exécutée dans son propre thread. Il peut soit être mis dans la file d'attente d'une taskpool:

```d
auto t = task!read("foo.txt");
taskPool.put(t);
```

soit être directement exécuté sur un nouveau thread:

```d
t.executeInNewThread();
```

Pour obtenir le résultat de la tâche, il faut appeler la méthode `yieldForce` de l'objet. Elle bloquera l'exécution jusqu'à ce que le résultat soit disponible.

### Pour aller plus loin

- [Le parallélisme dans _Programming in D_](http://ddili.org/ders/d.en/parallelism.html)
- [std.parallelism](http://dlang.org/phobos/std_parallelism.html)

## {SourceCode}

```d
import std.parallelism : task,
    taskPool, TaskPool;
import std.array : array;
import std.stdio : writeln;
import std.range : iota;

string theTask()
{
    import core.thread : dur, Thread;
    Thread.sleep( dur!("seconds")(1) );
    return "Bonjour !";
}

void main()
{
    // taskpool avec deux threads
    auto myTaskPool = new TaskPool(2);
    // Arrêter la taskpool est très important !
    scope(exit) myTaskPool.stop();

    // On lance une tâche longue et on fait
    // autre chose pendant ce temps
    auto task = task!theTask;
    myTaskPool.put(task);

    auto arr = iota(1, 10).array;
    foreach(ref i; myTaskPool.parallel(arr)) {
        i = i*i;
    }

    writeln(arr);

    import std.algorithm.iteration : map;

    // On utilise reduce pour calculer la somme
    // des carrés en parallèle
    auto result = taskPool.reduce!"a+b"(
        0.0, iota(100).map!"a*a");
    writeln("Sum of squares: ", result);

    // On récupère le résultat de l'opération
    // que l'on a lancé en arrière plan plus
    // tôt
    writeln(task.yieldForce);
}
```

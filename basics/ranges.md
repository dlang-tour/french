# Ranges

Lorsque le compilateur rencontre un `foreach`:

```
foreach(element; range)
{
    // Corps de la boucle
}
```

Il réecrit le code en interne en quelque chose de similaire à ceci:

```
for(auto __copieRange = range;
    !__copieRange.empty;
    __copieRange.popFront())
{
    auto element = __copieRange.front;
    // Corps de la boucle
}
```

Si cet objet range est un type par référence (ex. une classe), alors la range sera consommée et ne sera plus disponible pour une future itération (à moins que le corps de la boucle termine l'itération avant la dernière boucle normale). Si l'objet range est un type par valeur, alors une copie de la range sera faite et en fonction de la façon dont la range est définie, la boucle consommera ou ne consommera pas la range originale. La plupart des ranges de la librairie standard sont des `struct`s et donc une itération `foreach` est généralement non-destructive, même si cela n'est pas garanti. Si cette garantie est importante, une **forward** range est requise.

Un objet qui satisfait cet interface est appellé une **range** et on peut donc itérer dessus:

```
struct Range
{
    T front() const @property;
    bool empty() const @property;
    void popFront();
}
```
Notez que s'il est d'usage pour `empty` et `front` d'être définies comme des fonctions `const` (impliquant que les appeller ne modifiera pas la range), ce n'est pas obligatoire.

Les fonctions de `std.range` et `std.algorithm` fournissent les briques de base pour profiter de cette interface. Les ranges nous permettent de créer des algorithmes complexes à partir d'objets sur lesquels on peut itérer facilement. De plus, les ranges nous permettent de créer des objets **paresseux** qui ne font un calcul que quand c'est réellement nécessaire, par exemple quand le prochain élément de la range est demandée. Des algorithmes spécifiques aux ranges seront présentés dans un prochain chapitre.

### Exercise

Complétez le code source d'exemple pour créer la range `FibonacciRange` qui retourne les nombres de la [suite de Fibonacci](https://fr.wikipedia.org/wiki/Suite_de_Fibonacci).

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

struct FibonacciRange
{
    bool empty() const @property
    {
        // Quand est-ce que se fini 
        // une suite de Fibonacci ?!
    }

    void popFront()
    {
    }

    int front() const @property
    {
    }
}

void main()
{
    import std.range : take;
    import std.array : array;

    FibonacciRange fib;
    
    // `take` créé une autre range qui
    // va retourner N éléments aux maximum.
    // Cette range est _paresseuse_ et n'accède
    // à la range originale que si nécessaire
    auto fib10 = take(fib, 10);
    
    // Mais nous voulons accéder à tous les éléments
    // et les transformer en tableau d'entiers.

    int[] les10Fib = array(fib10);

    writeln("Les dix premiers nombres de la suite de Fibonacci: ",
        les10Fib);
    assert(les10Fib ==
        [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]);
}
```

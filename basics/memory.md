# Gestion mémoire

D est un langage de programmation système et autorise donc la gestion manuelle de la mémoire. Cela-dit, la gestion manuelle de la mémoire est source de beaucoup d'erreurs, et D utilise donc un *ramasse-miettes* comme méthode par défaut pour gérer les allocations de mémoire.

D fourni des types de pointeurs `T*` similaires à ceux de C:

```d
int a;
int* b = &a; // b contient l'adresse de a
auto c = &a; // c est un int* et contient l'adresse de a
```

Un nouveau bloc de mémoire sur le tas est alloué en utilisant l'expression `new`, qui retourne un pointeur vers l'espace mémoire alloué:

    int* a = new int;

Dès que l'espace mémoire référencé par `a` n'est plus référencée par aucun pointeur dans le programme, le ramasse-miettes libérera cet espace mémoire.

D a trois niveaux de sécurité différents pour les fonctions : `@system`, `@trusted` et `@safe`.
Sauf si précisé explicitement, le niveau par défaut est `@system`.
`@safe` est un sous-ensemble de D qui prévient les erreurs de mémoire en interdisants certaines opérations.
Du code `@safe` ne peut appeller que d'autres fonctions `@safe` ou `@trusted`.
L'arithmétique de pointeurs est interdite dans du code `@safe`:

```d
void main() @safe 
{
    int a = 5;
    int *p = &a;
    int *c = p + 5 // ERREUR
}
```

Les fonctions `@trusted` sont des fonctions vérifiées par le programmeur qui permettent de faire une connection entre SafeD (le sous-ensemble de D qui prévient les erreurs mémoires) et le reste des fonctions plus bas-niveau.

### Pour aller plus loin

* [SafeD](https://dlang.org/safed.html)

## {SourceCode}

```d
import std.stdio : writeln;

void safeFun() @safe
{
    writeln("Bonjour");
    // allouer de la mémoire avec le ramasse-miettes
    // est sûr aussi.
    int* p = new int;
}

void unsafeFun()
{
    int* p = new int;
    int* fiddling = p + 5;
}

void main()
{
    safeFun();
    unsafeFun();
}
```
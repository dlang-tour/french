# Programmation fonctionelle

D accorde une grande importance à la *programmation fonctionelle* et intègre des mécaniques pour développer dans un style fonctionel.

En D, une fonction peut être déclarée `pure`, ce qui implique qu'à partir des mêmes paramètres, elle retournera **toujours** le même résultat. Les fonctions `pure`s ne peutvent pas accéder ou changer une variable mutable et ne peuvent appeler que des fonctions qui sont elles-même `pure`s.

```d
int add(int gauche, int droite) pure
{
    // ERREUR: fonctionImpure();
    return gauche + droite;
}
```

Cette variante de `add` est appellée une fonction **strictement pure** parce qu'elle retourne un résultat qui dépend uniquement des paramètres en entrée, sans les modifier. D permet également de définire des fonctions **non strictement pures** qui peuvent accépter des paramètres mutables:

```d
void add(ref int resultat, int gauche, int droite) pure
{
    resultat = gauche + droite;
}
```

Ces fonctions sont quand même considérés pures et ne peuvent pas modifier ou accéder à des variables mutables de la portée globale. Seuls les paramètres mutables peuvent être modifiés.

De part les contraintes posées par `pure`, les fonctions pures sont idéal pour les environnements parallélisés et rendre les interblocages *impossibles*.
De plus, les fonctions pures peuvent être facilement mises en cache et permettent des optimisations du compilateurs.

L'attribut `pure` est automatiquement déduit par le compilateur pour les fonctions schématisées et les fonctions `auto`, quand c'est possible (c'est aussi vrai pour `@safe`, `nothrow` et `@nogc`).

### Pour aller plus loin

- [Un recueil de morceaux de codes fonctionnels en D](https://garden.dlang.io)


## {SourceCode}

```d
import std.bigint : BigInt;

/**
 Calcul la puissance d'un chiffre
 avec un exposant.

 Returns:
    Le chiffre à la puissance de l'exposant
 */
BigInt bigPow(uint base, uint power) pure
{
    BigInt result = 1;

    foreach (_; 0 .. power)
        result *= base;

    return result;
}

void main()
{
    import std.datetime : benchmark, to;
    import std.functional : memoize;
    import std.stdio : writefln, writeln;

    // memoize met en cache le résultat de la fonction
    // en fonction des entrées fournies en entrée.
    // Les fonctions pures fonctionnent très
    // biens avec ça !
    alias fastBigPow = memoize!(bigPow);

    void test()
    {
        writefln(".uintLength() = %s ",
        	   fastBigPow(5, 10000).uintLength);
    }

    foreach (i; 0 .. 10)
        benchmark!test(1)[0]
        	.to!("msecs", double)
        	.writeln("ça a pris: millisecondes");
}
```
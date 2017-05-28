# Tableaux

Il y a deux types de tableaux en D: **statiques** et **dynamiques**.
Quelque soit le type de tableau, les accès au tableau vérifient d'abord si l'index est dans les limites du tableau (sauf si le compilateur peut prouver que ces vérifications ne sont pas nécessaires).
Lorsque c'est vérifications échouent, une `RangeError` termine l'exécution du programme.
Les courageux peuvent complètement désactiver ces vérifications avec la flag `-boundscheks=off` du compilateur pour améliorer la vitesse du programme (au prix de la sûreté)

#### Tableaux statiques

Les tableaux statiques sont stockés sur la pile si ils sont définis dans une fonction, et dans la mémoire statique sinon. Ils ont une taille fixe connue au moment de la compilation. Le type d'un tableau statique inclut sa taille:

    int[8] tab;

`tab` est de type `int[8]`. Notez que la taille du tableau est spécifié à droite du type et non à droite du nom de ce dernier comme en C/C++.

#### Tableaux dynamiques

Les tableaux dynamiques sont stockés sur le tas et peuvent être redimensionnés à l'exécution. Un tableau dynamique est créé grâce à une expression `new` et à sa taille:

```d
int taille = 8; // variable instanciée à l'exécution
int[] tab = new int[taille];
```

Le type de `tab` est `int[]`, qu'on appelle une **tranche**. Nous expliquerons ce qu'est une tranche plus en détails dans la prochaine partie. Des tableaux multi-dimensionnels peuvent être créés simplement en utilisant la syntaxe `auto tab = new int[3][3]`.

#### Opérations sur les tableaux et propriétés

Les tableaux peuvent être concaténés en utilisant l'opérateur `~`, qui créera un nouveau tableau dynamique.

Les opérations mathématiques peuvent être appliquées à des tableaux entiers en utilisant un syntaxe de ce genre `c[] = a[] + b[]`, par exemple.
Ceci ajoute tous les élements de `a` à ceux de `b` de sorte que
`c[0] = a[0] + b[0]`, `c[1] = a[1] + b[1]`, etc. Il est également possible de réaliser des opérations sur un tableau avec une seule valeur:

```d
a[] *= 2; // multiplie tous les éléments par 2
a[] %= 26; // calcule le module par 26 de tous les éléments
```

Ces opérations sont susceptibles d'être optimisées par le compilateur pour utiliser des instructions spéciales du processuer qui réaliserons les opérations en une fois.

Les tableaux statiques et dynamiques ont une propriété `.length`, en lecture seule pour les tableaux dynamiques, mais qui peut être utilisé dans le cas d'un tableau dynamique pour changer la taille de ce dernier. 
La propriété `.dup` créé une copie du tableau.

Quand on utilise la syntaxe `tab[index]` pour accéder à un élément d'un tableau, le symbole spécial `$` fait référence à la taille du tableau. Par exemple, `tab[$ - 1]` représente le dernier élément du tableau. Cette expression est du sucre syntaxique pour `tab[tab.length - 1]`.

### Exercice

Completez le fonction `encrypt` pour déchiffrer le message secret. Le texte devrait être chiffré en utilisant le *chifre de César*, qui remplace les caractères de l'alphabet en utilisant un certain décalage. Le texte à chiffrer contient des caractères entre `a` et `z`, ce qui devrait rendre les choses plus simples.

### Aller plus loin

- [Les tableaux dans _Programming in D_](http://ddili.org/ders/d.en/arrays.html)
- [La spécification des tableaux](https://dlang.org/spec/arrays.html)

## {SourceCode:incomplete}

```d
import std.stdio : writeln;

/**
Remplace tous les caractères dans le
tableau `input` par un décalage de `shift`.
Les caractères sont compris entre `a` et `z`
et le caractère qui suit `z` est `a`.

Params:f
    input = tableau à décaler
    shift = longueur du décalage pour chaque lettre

Returns:
    tableau décalé
*/

char[] encrypt(char[] input, char shift)
{
    auto result = input.dup;
    // ...
    return result;
}

void main()
{
    // Nous allons maintenant chiffrer le 
    // message avec un un chiffrement de 
    // César de décalage 16!
    char[] toBeEncrypted = [ 'w','e','l','c',
      'o','m','e','t','o','d',
      // La dernière virgule sera juste
      // ignorée
    ];
    writeln("Avant: ", toBeEncrypted);
    auto encrypted = encrypt(toBeEncrypted, 16);
    writeln("Après: ", encrypted);

    // Assurons nous que votre fonction
    // fonctionne correctement
    assert(encrypted == [ 'm','u','b','s','e',
            'c','u','j','e','t' ]);
}
```

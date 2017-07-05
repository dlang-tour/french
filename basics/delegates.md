# Delegates

### Fonctions en paramètres

Une fonction peut aussi être le paramètre d'une autre fonction:

```d
void faireQqch(int function(int,int) faiseur)
{
    // appelle la fonction passée en paramètre
    faiseur(5,5);
}

faireQqch(additioner); // utilise la fonction globale `additioner`
                       // additioner doit prendre deux paramètres
                       // entiers
```

`faireQqch` peut être appellé comme n'importe quelle autre fonction

### Fonctions locales avec un contexte

L'exemple ci-avant utilise le type `function` qui représente un pointeur vers une fonction globale. Dès qu'on veut utiliser une fonction locale ou une méthode d'un objet, il faut utiliser des `delegate`s. C'est un pointeur vers une fonction qui contient également des informations sur son contexte. Par exemple, un `delegate` qui pointe vers une méthode d'un objet, inclue un pointeur vers cet objet. Un `delegate` créé par une fonction imbriquée inclue un lien vers la fonction englobante. Le compilateur D peut faire une copie du contexte sur le tas si c'est nécessaire pour garantir la sûreté de la mémoire, et le `delegate` pointera alors vers cette zone mémoire.

```d
void foo()
{
    void local()
    {
        writeln("local");
    }
    auto f = &local; // f est de type delegate()
}
```
La même fonction `faireQqch` qui prend un `delegate` en paramètre ressemblerait à ça:

```d
void faireQqch(int delegate(int,int) faiseur);
```

Les objets `function` et `delegate` ne sont pas interchangeables. Mais la fonction de la librairie standard [`std.functional.toDelegate`](https://dlang.org/phobos/std_functional.html#.toDelegate) convertit une `function` en `delegate`.

### Fonction anonymes et lambdas

Les fonctions pouvant être stockées dans des variables et passées en paramètres à d'autres fonctions, il est parfois laborieux de leur donner un nom et de les définir précisément. C'est pour celà que D permet de définir des fonctions anonymes et des _lambdas_:

```d
auto f = (int gauche, int droite){
    return gauche + droite;
};

auto f = (int gauche, int droite) // Lambda — convertie en interne vers la fonction ci-avant
```

Il est également possible de passer des chaînes de caractères contenant des fonctions en paramètres de schémas à certaines parties fonctionelles de la librairie standard. Par exemple, elles permettent de définir des algorithmes de _réduction_ de façon concise:

```d
[1,2,3].reduce!'a + b'; // 6
```

Les fonctions sous forme de chaînes de caractères sont possibles uniquement pour un ou deux paramètres et utilisent `a` pour le premier et `b` pour le deuxième.

### Pour aller plus loin

- [Spécification des `delegate`](https://dlang.org/spec/function.html#closures)

## {SourceCode}

```d
import std.stdio : writeln;

enum IntOps {
    add = 0,
    sub = 1,
    mul = 2,
    div = 3
}

/**
Fournie un calcul mathématique
Params:
    op = opération mathématique à effectuer
Returns: un delegate qui réalise l'opération
*/
auto getMathOperation(IntOps op)
{
    // Défini 4 lambda fonctions pour
    // 4 opérations mathématiques différentes
    auto add = (int lhs, int rhs) => lhs + rhs;
    auto sub = (int lhs, int rhs) => lhs - rhs;
    auto mul = (int lhs, int rhs) => lhs * rhs;
    auto div = (int lhs, int rhs) => lhs / rhs;

    // On s'assure que le switch couvre
    // tous les cas
    final switch (op) {
        case IntOps.add:
            return add;
        case IntOps.sub:
            return sub;
        case IntOps.mul:
            return mul;
        case IntOps.div:
            return div;
    }
}

void main()
{
    int a = 10;
    int b = 5;

    auto func = getMathOperation(IntOps.add);
    writeln("Le type de la fonction est ",
        typeof(func).stringof, "!");

    // exécute le delegate qui réalise 
    // l'opération pour nous
    writeln("résultat: ", func(a, b));
}
```

# Flux de contrôle

Le flux d'exécution d'une application peut être contrôlé conditionellement grâce aux instructions `if` et `else`:

```d
if (a == 5){
    writeln("La condition est remplie !");
} else if (a > 10) {
    writeln("Une autre condition est remplie !");
} else {
    writeln("Aucune condition n'est remplie !");
}
```

Quand un bloc `if` ou `else` ne contient qu'une instruction,
les crochets sont optionnels.

D a les mêmes opérateurs que C/C++ et Java pour comparer des variables:

* `==` et `!=` pour tester l'égalité et l'inégalité
* `<`, `<=`, `>` et `>=` pour tester l'infériorité (ou l'égalite) et la supériorité (ou l'égalité)

On peut combiner plusieurs conditions, l'opérateur `||` représente la *OU* logique
et `&&` représente le *ET* logique.

D a également une structure `switch`..`case` qui exécute un cas en fonction
de la valeur d'*une* variable. `switch` fonctionne avec les types fondamentaux 
comme avec les chaînes de caractères !
Il est même possible de définir des intervalles pour les types entiers en utilisant
la syntaxe `case START: .. case END:`. N'oubliez pas de jeter un oeil au code source d'exemple.

### Pour aller plus loin

#### Références de base

- [Les expressions logiques dans _Programming in D_](http://ddili.org/ders/d.en/logical_expressions.html)
- [Les instructions if dans _Programming in D_](http://ddili.org/ders/d.en/if.html)
- [Les expressions ternaires dans _Programming in D_](http://ddili.org/ders/d.en/ternary.html)
- [Les `switch` et les `case` dans _Programming in D_](http://ddili.org/ders/d.en/switch_case.html)

#### Références avancées

- [Les expressions en détail](https://dlang.org/spec/expression.html)
- [Spécification de l'instruction if](https://dlang.org/spec/statement.html#if-statement)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    if (1 == 1)
        writeln("D a eu son diplôme de mathématiques");

    int c = 5;
    switch(c) {
        case 0: .. case 9:
            writeln(c, " est entre 0 et 9");
            break; // necessary!
        case 10:
            writeln("Un dix !");
            break;
        default: // Si rien ne correspond !
            writeln("Rien");
            break;
    }
}
```

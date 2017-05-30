# Schémas (templates)

**D** permet la définition de schémas de fonctions similaires à C++ ou Java, ce qui veut signifie la définition de fonctions ou d'objets **génériques** qui ne fonctionnent avec n'importe quel type pour lequel les instructions contenues dans le corps de la fonction compilent sans erreurs:

```d
auto add(T)(T lhs, T rhs)
{
    return lhs + rhs;
}
```

Le paramètre de schéma `T` est défini entre parenthèses avant la liste des paramètres de la fonction. `T` est un emplacement de type, qui sera remplacé par le compilateur quand la fonction sera *instanciée* en utilisant l'opérateur `!`:

```d
add!int(5,10);
add!float(5.0f, 10.0f);
add!Animal(Chien,chat); // ne compilera pas, Animal n'implémente pas l'opérateur +
```

### Déductions de paramètres de schémas

Les schémas de fonctions ont deux listes de paramètres, la première pour les paramètres à la compilation et la deuxième pour les paramètres à l'exécution. (Les fonctions schématisées ne peuvent accepter que des paramètres à l'exécution).
Si un ou plusieurs paramètres de compilation ne sont pas fournis lorsque la fonction est appellée, le compilateur essaye de les déduire à partir de la liste des paramètres d'exécution et de leurs types:

```d
int a = 5; int b = 10;
add(a, b); // On déduit que `T` est un `int`
float c = 5.0;
add(a, c); // On déduit que `T` est un `float`
```

### Propriétés des schémas

Une fonction peut avoir un nombre arbitraire de paramètres de schéma qui sont spécifiés à l'instanciation en utilisant la syntaxe `fonction!(T1, T2 ...)`. Les paramètres de schéma peuvent être n'importe quel type, y compris des `string`s et des nombres à virgule flottante.

Contrairement aux génériques en Java, les schémas en D ne fonctionnent qu'à la compilation, et génèrent du code optimisé et taillé sur mesure pour l'ensemble de types sur lesquels la fonction est appellée.

Bien sur, on peut définir des schémas de `struct`, de `class` et d'`interface` également:

```d
struct S(T)
{
    //...
}
```

### Pour aller plus loin

- [Tutoriel sur les schémas en D](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Schémas dans _Programming in D_](http://ddili.org/ders/d.en/templates.html)

#### Ressources avancées

- [Spécifichation des schémas en D](https://dlang.org/spec/template.html)
- [Les schémas en D](http://dlang.org/templates-revisited.html):  Walter Bright écrit à propos de comment D améliore les templates C++.
- [Schémas variadiques](http://dlang.org/variadic-function-templates.html): Articles à propos d'un idiome D qui permet d'implémenter des fonctions variadiques à l'aide de schémas variadiques

## {SourceCode}

```d
import std.stdio : writeln;

/*
Schéma de classe qui permet
un implémentation générique
des animaux
*/
class Animal(string bruit) {
    void faireBruit() {
        writeln(bruit ~ "!");
    }
}

class Chien: Animal!("Ouaf") {
}

class Chat: Animal!("Miaou") {
}
// On aurait pu remplacer la définition de cette
//  classe par :
// alias Chat = Animal!("Miaou");

/**
Schéma de fonction qui prend un type
T et implémente la fonction faireBruit
Params:
    animal = objet qui peut faire du bruit
    n = nombre d'appels à faireBruit
*/
void multipleBruit(T)(T animal, int n) {
    for (int i = 0; i < n; ++i) {
        animal.faireBruit();
    }
}

void main() {
    auto chien = new Chien;
    auto chat = new Chat;
    multipleBruit(chien, 5);
    multipleBruit(chat, 5);
}
```

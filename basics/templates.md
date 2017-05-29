# Modèles ou patrons (templates)

**D** permet la définition de modèles de fonctions similaires à C++ ou Java, ce qui est un moyen de définir des fonctions ou des objets **génériques** qui fonctionnent avec n'importe quel type pour lequel les instructions contenues dans le corps de la fonction compilent sans erreurs:

```d
auto add(T)(T lhs, T rhs)
{
    return lhs + rhs;
}
```

Le paramètre de modèle `T` est défini entre parenthèses avant la liste des paramètres de la fonction. `T` est un paramètre de substitution, qui sera remplacé par le compilateur quand la fonction sera *instanciée* en utilisant l'opérateur `!`:

```d
add!int(5,10);
add!float(5.0f, 10.0f);
add!Animal(Chien,chat); // ne compilera pas, Animal n'implémente pas l'opérateur +
```

### Déductions de paramètres de modèles

Les modèles de fonctions ont deux listes de paramètres, la première pour les paramètres à la compilation et la deuxième pour les paramètres à l'exécution. (Les fonctions sans patron ne peuvent accepter que des paramètres à l'exécution).
Si un ou plusieurs paramètres de substitution ne sont pas fournis lorsque la fonction est appelée, le compilateur essaye de les déduire à partir des types des paramètres d'exécution:

```d
int a = 5; int b = 10;
add(a, b); // On déduit que `T` est un `int`
float c = 5.0;
add(a, c); // On déduit que `T` est un `float`
```

### Propriétés des modèles

Une fonction peut avoir un nombre arbitraire de paramètres de substitution qui sont spécifiés à l'instanciation en utilisant la syntaxe `fonction!(T1, T2 ...)`. Les paramètres de substitution peuvent être de n'importe quel type de base, y compris des `string`s et des nombres à virgule flottante.

Contrairement aux génériques en Java, les modèles en D ne fonctionnent qu'à la compilation, et génèrent du code optimisé et taillé sur mesure pour l'ensemble de types sur lesquels la fonction est appellée.

Bien sûr, on peut également définir des patrons à partir de `struct`, de `class` et d'`interface`:

```d
struct S(T)
{
    //...
}
```

### Pour aller plus loin

- [Tutoriel sur les modèles en D](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Modèles dans _Programming in D_](http://ddili.org/ders/d.en/templates.html)

#### Ressources avancées

- [Spécifichation des schémas en D](https://dlang.org/spec/template.html)
- [Les modèles en D](http://dlang.org/templates-revisited.html):  Walter Bright écrit à propos de comment D améliore les templates C++.
- [Modèles variadiques](http://dlang.org/variadic-function-templates.html): Articles à propos d'un idiome D qui permet d'implémenter des fonctions variadiques à l'aide de modèles variadiques

## {SourceCode}

```d
import std.stdio : writeln;

/*
Modèle de classe qui permet
une implémentation générique
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
// On aurait pu remplacer la définition de cette classe par :
// alias Chat = Animal!("Miaou");

/**
Modèle de fonction qui prend un type
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

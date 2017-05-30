# Méta-programmation par modèles

Si vous avez déjà eu à faire de la *méta-programmation par modèles* en C++, vous serez soulagés d'apprendre que le D offre des outils pour vous rendre la vie plus facile. La méta-programmation par modèles est une technique qui permet de prendre des décisions en fonction de certaines propriétés des types, et donc de rendre des types génériques encore plus flexibles.

### `static if` et `is`

Comme le `if` normal, `static if` compile un bloc de code en fonction d'une condition qui peut être évaluée à la compilation:

```d
static if(is(T == int))
{
    writeln("T est un entier");
}
static if(is(typeof(x) : int))
{
    writeln("T se convertit implicitement en entier");
}
```

L'expression [`is`](http://wiki.dlang.org/Is_expression) est un outil générique qui évalue des conditions à la compilation.

```d
static if(is(T == int)) // T est un paramètre de modèle
{
    int x = 10;
}
```

Les accolades sont optionelles si la condition est valide et aucune nouvelle portée n'est créée. Pour créer un nouveau bloc à la compilation, utiliser `{ {` et `} }`.

`static if` peut être utilisé n'importe où dans le code, dans des fonctions, dans la portée globale ou dans des définitions de types.

### Modèle de `mixin`

À chaque fois que vous voyez de la *duplication de code*, les modèles de `mixin` sont vos amis:

```d
mixin template Foo(T)
{
    T foo;
}
...
mixin Foo!int; // la variable foo de type entier est accessible à partir d'ici
```

Un modèle de `mixin` peut contenir n'importe quel nombre d'expressions complexes, qui sont insérées dans le code à l'endroit de l'instanciation du modèle. Dites au revoir au pré-processeur si vous venez de C!

### Contraintes de modèles

Un modèle peut être défini avec n'importe quel nombre de contraintes qui garantissent certaines propriétés que les types doivent avoir:

```d
void foo(T)(T value)
if (is(T : int))
{
    // foo!T ne peut être valide
    // que si T peut se convertir
    // en entier
}
```

Les contraintes peuvent être combinées pour former une expression booléenne, et peuvent même contenir des appels à des fonctions qui peuvent être appelées à la compilation (CTFE). Par exemple, `std.range.primitives.isRandomAccessRange` vérifie qu'un type est bien une range et supporte l'opérateur `[]`

### Pour aller plus loin

#### Références de base

- [Tutoriel sur les modèles en D](https://github.com/PhilippeSigaud/D-templates-tutorial)
- [Compilation conditionelle](http://ddili.org/ders/d.en/cond_comp.html)
- [std.traits](https://dlang.org/phobos/std_traits.html)
- [Plus sur les modèles dans  _Programming in D_](http://ddili.org/ders/d.en/templates_more.html)
- [Mixins dans  _Programming in D_](http://ddili.org/ders/d.en/mixin.html)

#### Ressources avancées

- [Spécification de `version`](https://dlang.org/spec/version.html)
- [Traits](https://dlang.org/spec/traits.html)
- [Modèles de mixins](https://dlang.org/spec/template-mixin.html)
- [Spécification des templates en D](https://dlang.org/spec/template.html)

## {SourceCode}

```d
import std.traits : isFloatingPoint;
import std.uni : toUpper;
import std.string : format;
import std.stdio : writeln;

/*
Un vecteur qui fonctionne uniquement pour les
nombres, entiers ou à virgule flottante
*/
struct Vector3(T)
  if (is(T: real))
{
private:
    T x,y,z;

    /*
    Générateur de getter et de setter, parce
    qu'on déteste la duplication !

    var -> T getVar() et void setVar(T)
    */
    mixin template GetterSetter(string var) {
        // On utilise mixin pour construire
        // les noms des fonctions
        mixin("T get%s() const { return %s; }"
          .format(var.toUpper, var));

        mixin("void set%s(T v) { %s = v; }"
          .format(var.toUpper, var));
    }

    /*
    Génère facilement les méthodes 
    getX, setX, etc ... avec un modèle
    de mixin
    */
    mixin GetterSetter!"x";
    mixin GetterSetter!"y";
    mixin GetterSetter!"z";

public:
    /*
    La fonction point n'est disponible que 
    pour les types de nombres à virgule
    flottante
    */
    static if (isFloatingPoint!T) {
        T dot(Vector3!T rhs) {
            return x * rhs.x + y * rhs.y +
                z * rhs.z;
        }
    }
}

void main()
{
    auto vec = Vector3!double(3,3,3);
    // Ça ne fonctionne pas à cause de la
    // contrainte de modèle
    // Vector3!string illegal;

    auto vec2 = Vector3!double(4,4,4);
    writeln("vec dot vec2 = ", vec.dot(vec2));

    auto vecInt = Vector3!int(1,2,3);
    // n'a pas la méthode dot parce qu'on ne 
    // l'a activée que pour les nombres à virgule
    // flottante
    // vecInt.dot(Vector3!int(0,0,0));

    // getter et setters générés automatiquement
    vecInt.setX(3);
    vecInt.setZ(1);
    writeln(vecInt.getX, ",",
      vecInt.getY, ",", vecInt.getZ);
}
```

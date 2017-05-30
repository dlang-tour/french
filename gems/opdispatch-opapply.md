# opDispatch et opApply

D permet de surcharger des opérateurs comme `+`, `-` ou l'opérateur d'appel `()` pour les [classes et les structures](https://dlang.org/spec/operatoroverloading.html). Nous allons nous intéresser à la surcharges des opérateurs `opDispatch` et `opApply`.

### opDispatch

`opDispatch` peut être défini comme une méthode d'une `struct` ou d'une `class`. Chaque appel à une méthode indéfinie dans cet objet sera passé à `opDispatch` en passant le nom de la méthode appellé en paramètre de modèle. `opDispatch` est une méthode *attrape-tout* qui permet un autre niveau de programmation générique — et le tout **à la compilation**!

```d
struct C
{
    void callA(int i, int j) { //... }
    void callB(string s) { //... }
}
struct CallLogger(C)
{
    C content;
    void opDispatch(string name, T...)(T vals)
    {
        writeln("called ", name);
        mixin("content." ~ name)(vals);
    }
}

CallLogger!C l;
l.callA(1, 2); // "called content.callA"
l.callB("ABC"); // "called content.callB"
```

### opApply

Au lieu de définir une *range*, on peut implémenter la méthode `opApply` pour appeller `foreach` sur un objet personnalisé. Itérer sur ce genre de type appellera `opApply` avec un delegate spécial en paramètre:

```d
class Tree
{
    Tree gauche;
    Tree droite;

    int opApply(int delegate(Tree) dg)
    {
        if(gauche && gauche.opApply(dg)) return 1;
        if(dg(this)) return 1;
        if(droite && droite.opApply(dg)) return 1;
        return 0;
    }
    Tree tree = new Tree;
    foreach(node; tree)
    {
        //...
    }
}
```

Le compilateur transforme le corps du `foreach` en un delegate() qui est passé à l'objet. Son unique paramètre contiendra le contenu courant de la valeur de l'itération. La valeur de retour `int` doit être vérifiée et si ce n'est pas `0`, l'itération doit être arrêtée.

### Pour aller plus loin

- [Surcharge d'opérateurs dans _Programming in D_](http://ddili.org/ders/d.en/operator_overloading.html)
- [`opApply` dans _Programming in D_](http://ddili.org/ders/d.en/foreach_opapply.html)
- [Surcharge d'opérateur en D](https://dlang.org/spec/operatoroverloading.html)

## {SourceCode}

```d
/*
Un Variant est un type qui peut contenir
un autre type:
https://dlang.org/phobos/std_variant.html
*/

import std.variant : Variant;

/*
Ce type peut contenir un nombre quelconque
de membres avec opDispatch, comme `var` en 
Javascript.
*/
struct var {
    private Variant[string] values;

    @property
    Variant opDispatch(string name)() const {
        return values[name];
    }

    @property
    void opDispatch(string name, T)(T val) {
        values[name] = val;
    }
}

void main() {
    import std.stdio : writeln;

    var test;
    test.foo = "test";
    test.bar = 50;
    writeln("test.foo = ", test.foo);
    writeln("test.bar = ", test.bar);
    test.foobar = 3.1415;
    writeln("test.foobar = ", test.foobar);
    // ERREUR parce qu'elle n'existe
    // pas encore
    // writeln("test.notthere = ",
    //   test.notthere);
}
```
# Classes

D supporte les classes et les interfaces comme en Java ou en C++.

Toutes les classes héritent de [`Object`](https://dlang.org/phobos/object.html) implicitement:

    class Foo { } // hérite de Object
    class Bar : Foo {} // Bar est également un Foo

Les classes en D sont générallement instanciées sur le tas en utilisant `new`:

    auto bar = new Bar;

Les objets de type classe sont toujours des types par référence et contrairement aux `struct` ne sont pas copiés par valeur:

    Bar bar = foo; // bar pointe vers foo

Le ramasse-miette s'assurera que la mémoire est libérée quand il n'y a plus aucune référence vers un objet.

### Héritage

Si une méthode d'une classe de base est surchargée, le mot clé `override` doit être utilisé pour l'indiquer. Cela protège des surcharges accidentelles de fonctions:

    class Bar : Foo {
        override fonctionDeFoo() {}
    }

En D, les classes ne peuvent hériter que d'une seule classe.

### Méthodes `final` et `abstract`

- Une fonction peut être déclarée `final` dans une classes parente pour empêcher la surchage dans les classes filles
- Une fonction peut être déclarée `abstract` dans une classe parente pour forcer la surcharge dans les classes filles
- Une classe complète peut être déclarée `abstract` pour empêcher qu'elle ne soit instanciée
- `super(...)` peut être utilisé pour appeller explicitement le constructeur de base.

### Comparer des classes

Pour les objets de type classe, les opérateurs `==` et `!=` comparent le contenu des objets. De ce fait, comparer une classe avec `null` est une erreur, puisque `null` n'a pas de contenu. L'opérateur `is` comparent deux classes pour voir si elles sont identiques. Pour tester la différence, utilisez `e1 !is e2`:

```d
MaClasse c;
if(c == null) // ERREUR
    //...
if(c is null) // ok
    //...
```

Les `struct`s sont comparées bit à bit lorsqu'utilisées avec `is`. Pour les autres types, l'identité est équivalent à l'égalité.

### Pour aller plus loin

- [Classes dans _Programming in D_](http://ddili.org/ders/d.en/class.html)
- [Héritage dans _Programming in D_](http://ddili.org/ders/d.en/inheritance.html)
- [La classe Object dans _Programming in D_](http://ddili.org/ders/d.en/object.html)
- [Spécification des classes en D](https://dlang.org/spec/class.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Un type qui peut être utilisé pour
n'importe quoi ...
*/
class Any {
    // protected est juste accessible par
    // les classes filles
    protected string type;

    this(string type) {
        this.type = type;
    }

    // public est implicite au fait
    final string getType() {
        return type;
    }

    // Il faut implémenter ça !
    abstract string convertirEnString();
}

class Entier: Any {
    // accesible uniquement par Entier
    private {
        int nombre;
    }

    // constructeur
    this(int nombre) {
        // call base class constructor
        super("Entier");
        this.nombre = nombre;
    }

    // Ceci est implicite, and une autre
    // façon de définir le niveau de 
    // protection
    public:

    override string convertirEnString() {
        import std.conv : to;
        // Le couteau suisse de la conversion
        return to!string(nombre);
    }
}

class Float: Any {
    private float nombre;

    this(float nombre) {
        super("float");
        this.nombre = nombre;
    }

    override string convertirEnString() {
        import std.string : format;
        // We want to control precision
        return format("%.1f", nombre);
    }
}

void main()
{
    Any[] anys = [
        new Entier(10),
        new Float(3.1415f)
        ];

    foreach (any; anys) {
        writeln("type de any= ", any.getType());
        writeln("Contenu = ",
            any.convertirEnString());
    }
}
```

# Structures

Une des façons de définir nos propres types en D
est de définir une `struct`:

```d
struct Person
{
    int age;
    int height;
    float ageXHeight;
}
```

Une `struct` est toujours construite sur la pile (à moins
qu'elle soit appellée avec `new`) et est copiée par **valeur**
lorsqu'elle est passée en paramètres à une fonction.

```d
auto p = Person(30, 180, 3,1415);
auto t = p; //copie
```

Quand un nouvel objet `struct` est créé, ses membres peuvent
être initialisés dans l'ordre dans lequel ils sont définis dans
la `struct`. On peut définir un constructeur personnalisé en créant
une méthode `this(...)`. En cas de conflits de nom, on peut accéder
à la structure courante grâce à `this`:

```d
struct Person 
{
    this(int age, int height)
    {
        this.age = age;
        this.height = height;
        this.ageXHeight = cast(float)age * height;
    }
}

//...

Person p(30, 100); //initialisation
p = Person(30, 180); //assignation à une nouvelle instance
```

Une `struct` peut contenir n'importe quel nombre de méthodes. Ces méthodes
sont `public` par défaut, et donc accessibles depuis l'extérieure. Elles
peuvent être `private` et donc n'être accessibles que depuis une autre 
méthode de la même `struct`, ou d'un autre endroit dans le même module.

```d
struct Person
{
    void faireQqch()
    {
        //...
    }

    private void faireChosePrivee()
    {
        //...
    }
} 

//...

p.faireQqch(); // Appel de la méthode faireQqch
p.faireChosePrivee(); // Interdit
```

### Méthodes `const`

Si une méthode est déclarée avec `const`, elle ne sera pas autorisée à modifier
un membre de la structure. C'est une garantie offerte par le compilateur.
Déclarer une méthode `const` permet de l'appeller sur un objet `const` ou `immutable`, mais garantie également que la méthode ne changera pas l'état de l'objet.

### Méthodes statiques

Si une méthode est déclarée `static`, on pourra l'appeller sans objet instancié (par exemple `Person.methodeStatique()`) mais elle ne sera pas autorisée à accéder à des membres non-statiques. Cela peut être utilisé si une méthode n'a pas besoin d'accéder à un membre non-static de la structure, mais est lié logiquement à cette structure. Cela peut aussi être utilisé pour offrir une fonctionnalité sans créer d'instance explicite, par exemple, certains implémentations du patron de conception Singleton utilisent `static`.

### Héritage

Une `struct` ne peut pas hériter d'une autre `struct`.
Des hiérarchies de types ne peuvent être construites qu'avec des classes,
que nous verrons dans une prochaine aprtie.
Cependant, avec `alias this` ou les `mixins`, on peut facilement obtenir du molyporphisme.

### Pour aller plus loin

- [Structs dans _Programming in D_](http://ddili.org/ders/d.en/struct.html)
- [La spécification des structs](https://dlang.org/spec/struct.html)

### Exercice

À partir de `struct Vector3`, implémentez les fonctions suivantes et faites fonctionner l'exemple correctement:

* `length()` - retourne la longueur du vecteur
* `dot(Vector3)` - retourne le produit scalaire avec ce vecteur
* `toString()` - retourne la représentation de ce vecteur sous forme de string

La fonction [`std.string.format`](https://dlang.org/phobos/std_format.html) retourne une chaîne de caractères en utilisant une syntaxe similaire à celle de `printf`: `format("MonEntier = %d", monEntier)`. Les chaînes de caractères seront expliquées plus en détails dans une prochaine partie.

## {SourceCode:incomplete}

```d
struct Vector3 {
    double x;
    double y;
    double z;

    double length() const {
        import std.math : sqrt;
        return 0.0;
    }

    // rhs sera copié
    double dot(Vector3 rhs) const {
        return 0.0;
    }

    /**
    Returns: représentation sous forme de string
        La sortie doit être limitée à une 
        précision
        de 1 chiffre après la virgule
    "x: 0.0 y: 0.0 z: 0.0"
    */
    string toString() const {
        import std.string : format;
        // Astuce: réferez vous à la 
        // documentation de std.format pour 
        // comprendre comment influencer la 
        // représentation des flottants.
        return format("");
    }
}

void main() {
    auto vec1 = Vector3(10, 0, 0);
    Vector3 vec2;
    vec2.x = 0;
    vec2.y = 20;
    vec2.z = 0;

    // Si une fonction membre n'a pas de paramètre
    // les parenthèses sont optionelles 
    // lors de l'appel
    assert(vec1.length == 10);
    assert(vec2.length == 20);

    // Test du produit scalaire
    assert(vec1.dot(vec2) == 0);

    // 1 * 1 + 2 * 1 + 3 * 1
    auto vec3 = Vector3(1, 2, 3);
    assert(vec3.dot(Vector3(1, 1, 1)) == 6);

    // 1 * 3 + 2 * 2 + 3 * 1
    assert(vec3.dot(Vector3(3, 2, 1)) == 10);

    // Grâce à toString(), nous pouvons afficher
    // nos vecteurs grâce à writeln
    import std.stdio : writeln, writefln;
    writeln("vec1 = ", vec1);
    writefln("vec2 = %s", vec2);

    // Vérifie la représentation sous forme de 
    // chaîne de caractères
    assert(vec1.toString() ==
        "x: 10.0 y: 0.0 z: 0.0");
    assert(vec2.toString() ==
        "x: 0.0 y: 20.0 z: 0.0");
}
```
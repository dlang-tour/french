# Sous-types

Une `struct` ne peut pas hériter d'autres `struct`s. Mais pour ces pauvres `struct`s, D a une autre moyen d'étendre une fonctionnalité : le **sous-typage**.

Une structure peut définir un de ses membres en tant qu'`alias this`:

```d
struct SafeInt
{
    private int theInt;
    alias theInt this;
}
```

N'importe quelle fonction ou opération qui ne peut pas être réalisée par `SafeInt` sera gérée par l'attribut `alias this`. De l'intérieur, `SafeInt` se comporte comme un entier normal.

Cela permet d'étendre des types avec des nouvelles fonctionnalités sans aucun coûts en terme de mémoire ou d'exécution. Le compilateur s'assure de faire la bonne opération quand il accède à l'attribut `alias this`.

`alias this` fonctionne également avec les classes.

## {SourceCode}

```d
import std.stdio : writeln;

struct Vector3 {
    private double[3] vec;
    alias vec this;

    double dot(Vector3 rhs) {
        return vec[0]*rhs.vec[0] +
          vec[1]*rhs.vec[1] + vec[2]*rhs.vec[2];
    }
}

void main()
{
    Vector3 vec;
    // on intéragit en fait avec un
    // double[] ici.
    vec = [ 0.0, 1.0, 0.0 ];
    assert(vec.length == 3);
    assert(vec[$ - 1] == 0.0);

    auto vec2 = Vector3([1.0,0.0,0.0]);
    // mais cette fonctionnalité a été
    // étendue!
    writeln("vec dot vec2 = ", vec.dot(vec2));
}
```
# Tests unitaires

Les tests sont une excellente façon d'assurer la stabilité et le bon fonctionnement de ses applications. Ils servent de documentation sous forme d'exemples, et permettent de bien maintenir toutes les fonctionnalités lorsqu'on fait une modification au code. En D, on peut utiliser la syntaxe très pratique des blocs `unittest`, qui font partie intégrante du langage. N'importe où dans un module D, des blocs `unittest` peuvent être insérés pour tester une fonctionnalité.

```d
// Bloc pour ma fonction monAbs
unittest
{
    assert(monAbs(-1) == 1);
    assert(monAbs(1)  == 1); 
}
```

Cela permet d'appliquer la méthode du [Test-driven development](https://en.wikipedia.org/wiki/Test-driven_development) de façon très simple.

### Exécution des tests unitaires

Les blocs `unittest` peuvent contenir n'importe quelles instructions, qui sont compilées et exécuté quand le programme est compilé avec l'option `-unittest`. DUB permet aussi de compiler et d'exécuter les tests grâce à la commande `dub test`.

### Vérifier les exemples avec `assert`

Habituellement, un bloc `unittest` contient des assertions (avec `assert`) qui testent les fonctionnalités d'une fonction donnée. Les blocs `unittest` sont habituellement placés près de la définition d'une fonction que cette dernière soit au niveau module ou dans une classe ou une struct.

### Améliorer la couverture du code

Les tests unitaires sont un bouclier puissant pour blinder ses applications. On appelle la mesure du nombre de lignes de code qui sont testées la _couverture du code_. C'est le ratio entre le nombre de lignes exécutées par les tests et le nombre de lignes existantes. Le compilateur DMD permet de générer facilement un rapport de la couverture de code en activant l'option `-cov`. Pour chaque module, un fichier `.lst` est généré, qui contient des statistiques détaillées sur la couverture.

Comme le compilateur est capable de déduire les attributs pour du code schématisé automatiquement, il est courant d'ajouter des annotations aux tests unitaires, pour être sûr que ces annotations seront appliquées au code testé:

```d
unittest @safe @nogc nothrow pure
{
    assert(monAbs() == 1)
}
```

### Pour aller plus loin

- [Les tests unitaires dans _Programming in D_](http://ddili.org/ders/d.en/unit_testing.html)
- [Spécification des tests unitaires en D](https://dlang.org/spec/unittest.html)

## {SourceCode}

```d
import std.stdio : writeln;

struct Vector3 {
    double x;
    double y;
    double z;

    double dot(Vector3 rhs) const {
        return x*rhs.x + y*rhs.y + z*rhs.z;
    }

    // Ca fonctionne !
    unittest {
        assert(Vector3(1,0,0).dot(
          Vector3(0,1,0) == 0);
    }

    string toString() const {
        import std.string : format;
        return format("x:%.1f y:%.1f z:%.1f",
          x, y, z);
    }

    // .. et ça aussi !
    unittest {
        assert(Vector3(1,0,0).toString() ==
          "x:1.0 y:0.0 z:0.0");
    }
}

void main()
{
    Vector3 vec = Vector3(0,1,0);
    writeln(`Ce vecteur a été testé: `,
      vec);
}

/*
Ou ailleurs.
Rien n'est compilé et est juste ignoré
quand compilé en mode release. Exécutez
dub test en local ou compilez avec
dmd -unittest pour tester vos modules
*/
unittest {
    Vector3 vec;
    // .init est une propriété qui retourne la
    // valeur initiale d'un type
    assert(vec.x == double.init);
}
```

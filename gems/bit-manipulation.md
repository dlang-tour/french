# Manipulations de bit

Un excellent exemple des capacités de D à générer du code à la compilation avec des mixins est celui de la manipulation de bits.

### Manipulations simples

D implémente les opérateurs suivants pour manipuler des bits:

- `&` et bit à bit
- `|` ou bit à bit
- `~` inversion bit à bit
- `<<` *décallage à gauche* signé bit à bit
- `>>` *décallage à droite* signé bit à bit
- `>>>` *décallage à droite* non-signé bit à bit

### Exemple

Une manipulation bit à bit courante est de lire la valeur d'un bit. `core.bitopt.bt` est suffisant pour les tâches les plus communes, mais pour s'habituer à la manipulation bit à bit, implémentons la lecture d'un bit de manière un peu verbeuse:

```d
enum posA = 1;
enum maskA = (1 << posA);

bool getFieldA()
{
    return _data & maskA;
}
```

Une généralisation de cette fonction consiste à tester des blocs plus grand que 1, et donc de créer un masque de lecture avec la longueur nécessaire, puis de décaller les données et d'appliquer le masque:

```d
enum posA = 1;
enum lenA = 3;
enum maskA = (1 << lenA) - 1; // ...0111

uint getFieldA()
{
    return (_data >> posA) & maskA;
}
```

On peut modifier la valeur d'un bit en inversant le masque et en autorisant donc l'écriture que dans le bloc spécifié:

```d
void setFieldA(bool b)
{
    return (_data & ~maskAWrite) | ((b << aPos) & maskAWrite),
}
```

### `std.bitmanip` à la rescousse !

C'est très amusant d'écrire son propre code pour faire de la manipulation bit à bit et D fournit tous les outils nécessaires pour le faire. Mais dans la plupart des cas, on ne veut pas copier-coller ce code de manipulation, car c'est sujet à erreurs et difficile à maintenir.
D vous permet donc d'écrire des manipulations bit à bit lisibles et maintenables avec `std.bitmanip` et la puissance des mixins, sans sacrifier les performances.

Jetez un coup d'œil à votre droite. Un `BitVector` est défini, mais il n'utilise que X bits et il est totalement indistinguable d'une structure normale

`std.bitmanip` et `core.bitop` contiennent plein d'autres outils qui sont d'une grande aide pour écrire des applications qui ont besoin d'économiser leur mémoire.

### *padding* et alignement

Comme le compilateur va ajouter du *padding* (remplissage) aux variables de taille inférieure à la taille de la mémoire dans le système d'exploitation (`size_t.sizeof`), par exemple `bool`, `byte`, `char`, il est recommandé de commencer avec des champs de haut alignement.

## Pour aller plus loin

- [std.bitmanip](http://dlang.org/phobos/std_bitmanip.html) - manipulations bit-à-bit
- [_Bit Packing like a Madman_ — vidéo de la conférence D](http://dconf.org/2016/talks/sechet.html)

## {SourceCode}

```d
struct BitVector
{
    import std.bitmanip : bitfields;
    // crée des champs privés avec les
    // noms suivants
    mixin(bitfields!(
        uint, "x",    2,
        int,  "y",    3,
        uint, "z",    2,
        bool, "flag", 1));
}

void main()
{
    import std.stdio : writefln, writeln;

    BitVector vec;
    vec.x = 2;
    vec.z = vec.x - 1;
    writefln("x: %d, y: %d, z: %d",
              vec.x, vec.y, vec.z);

    // seuls 8 bits, 1 octet sont utilisés
    writeln(BitVector.sizeof);

    struct Vector { int x, y, z; }
    // 4 octets (int) par variable
    writeln(Vector.sizeof);

	struct BadVector
	{
		bool a;
		int x, y, z;
		bool b;
	}
    // à cause du padding
    // 4 octets sont utilisés pour chaque champ
	writeln(BadVector.sizeof);
}
```

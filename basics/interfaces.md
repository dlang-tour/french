# Interfaces

D permet de définir des `interface`s qui sont techniqument comme des types de classes, mais dont les méthodes doivent être implémentés par toute classe qui hérité de l'`interface`:

```d
interface Animal
{
    void faireBruit();
}
```

La méthode `faireBruit` doit être implémentée par `Chien` car cette classe hérite de l'interface `Animal`. Basiquement, `faireBruit` se comporte comme une méthode `abstract` dans une classe parente:

```d
class Chien: Animal
{
    override faireBruit()
    {
        //...
    }
}

auto chien = new Chien;
Animal animal = chien; // conversion implicite en type d'interface
chien.faireBruit(); 
```

### Le pattern NVI (non virtual interface)

Le pattern [NVI](https://en.wikipedia.org/wiki/Non-virtual_interface_pattern) (Patron de conception d'interface non-virtuelle dans la langue de Molière) permet la définition d'exécutions types en autorisant la définition de méthode _non virtuelles_ pour une interface.
On peut facilement mettre en place ce pattern en D, puisque le langage autorise la définition de fonctions `final` dans une `interface` que l'on ne peut pas surcharger. Cela force un certain comportement qui est customisé par la surcharger des autres méthodes de l'`interface`:

```d
interface Animal
{
    void faireBruit();
    final doubleBruit()
    {
        faireBruit();
        faireBruit();
    }
}
```

### Pour aller plus loin

- [Les interfaces dans _Programming in D_](http://ddili.org/ders/d.en/interface.html)
- [SpécifiChation des interfaces en D](https://dlang.org/spec/interface.html)

## {SourceCode}

```d
import std.stdio : writeln;

interface Animal {
    /*
    Fonction virtuelle
    qui doit être surchargée !
    */
    void faireBruit();

    /*
    NVI pattern. Utilise faireBruit en interne
    pour customiser le comportement des
    classes filles
    */
    
    Params: 
        n =  nombre de répétitions
    */
    final void multipleBruit(int n) {
        for(int i = 0; i < n; ++i) {
            faireBruit();
        }
    }
}

class Chien: Animal {
    override void faireBruit() {
        writeln("Ouaf!");
    }
}

class Chat: Animal {
    override void faireBruit() {
        writeln("Miaou!");
    }
}

void main() {
    Animal chien = new Chien;
    Animal chat = new Chat;
    Animal[] animals = [chien, chat];
    foreach(animal; animals) {
        animal.multipleBruit(5);
    }
}
```
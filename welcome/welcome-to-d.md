# Bienvenue

Bienvenue dans cette visite interactive du *langage de programmation D*

{{#dmanmobile}}

Cette visite donne un aperçu de ce langage __puissant__ et __expressif__ qui se compile directement vers du code machine __natif__ et __performant__.

{{/dmanmobile}}

### Qu'est-ce que le langage D ?

Le D est le résultat de _décennies d'expérience dans la conception de compilateurs_ pour de nombreux langages, et c'est un langage aux [fonctionnalités uniques](http://dlang.org/overview.html):

{{#dmandesktop}}

- des constructions _haut niveau_ pour des modélisations puissantes
- un langage _compilé_ et _performant_
- typage statique
- interfacage direct avec l'API du système d'exploitation et le matériel
- temps de compilation extrèmement rapide
- permet une gestion sûre de la mémoire (SafeD)
- du code _maintenable_ et _lisible_
- une courbe d'apprentissage courte (syntaxe similiare au C, à Java et à d'autres)
- compatible avec l'interface binaire-programme C
- compatibilité limitée avec l'interface binaire-programme C++
- multi-paradigme (impératif, structuré, orienté objet, générique, fonctionnel pur et même assembleur)
- prévention des erreurs intégrée (contrats, tests unitaires)

... et beaucoup d'autres [fonctionnalités](http://dlang.org/overview.html)

{{/dmandesktop}}

### À propos de cette visite

Chaque partie est accompagnée d'exemples. Ces exemples peuvent être modifiés et utilisés pour expérimenter avec les fonctionnalités du langage D. Cliquez sur le bouton "Executer" (ou appuyez sur `Ctrl+Entrée`) pour exécuter le code.

### Contribuer

Cette visite est [open-source](https://github.com/dlang-tour) et nous accueillons toutes les contributions pour rendre cette visite encore plus complète et agréable.

## {SourceCode}

```d
import std.stdio;
import std.algorithm;
import std.range;

void main()
{
    // Commençons !
    writeln("Bonjour tout le monde !");

    // Un exemple pour programmeurs
    // expérimentés : prend trois listes,
    // et sans allouer de nouvelle mémoire,
    // effectue un tri traversant les listes
    // existantes
    int[] arr1 = [4, 9, 7];
    int[] arr2 = [5, 2, 1, 10];
    int[] arr3 = [6, 8, 3];
    sort(chain(arr1, arr2, arr3));
    writefln("%s\n%s\n%s\n", arr1, arr2, arr3);
    // Pour en apprendre plus sur cet exemple,
    // voir la page "Algorithmes de range" dans
    // le menu "Quelques bouchées de D"
}
```

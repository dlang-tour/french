# Bienvenue

Bienvenue dans cette visite interactive du *langage de programmation D*

{{#dmanmobile}}

Cette visite va vous donner un aperçu de ce langage __puissant__ et __expressif__ qui se compile vers du code __natif__ et __performant__.

{{/dmanmobile}}

### Qu'est-ce que le langage D ?

Le D est le résultat de _décennies d'expérience dans la conception de compilateurs_ pour de nombreux langages, et c'est un langage aux [fonctionnalités uniques](http://dlang.org/overview.html): 

{{#dmandesktop}}

- des constructions _haut niveau_ pour des modélisations puissantes
- un langage _compilé_ et _performant_
- typage statique
- amélioration du C++ (sans les erreurs)
- interfacage direct avec l'API du système d'exploitation et le matériel
- compilation très rapide
- permet une gestion sûre de la mémoire (SafeD)
- du code _maintenable_ et _lisible_
- une courbe d'apprentissage courte (syntaxe similiare au C, à Java et à d'autres)
- interfacable avec des librairies C
- multi-paradigme (impératif, structuré, orienté objet, généric, fonctionnel et même assembleur)
- prévention des erreurs intégrées (contrats, tests unitaires)

... et beaucoup d'autres [fonctionnalités](http://dlang.org/overview.html)

{{/dmandesktop}}

### À propos de cette visite

Chaque partie est accompagnée du code d'exemple qui peut être modifié pour vous faire votre propre expérience du D.
Cliquez sur le bouton "run" (ou appuyez sur `Ctrl+Entrée`) pour exécuter le code.

### Contribuer

Cette visite est [open-source](https://github.com/dlang-tour) et nous accueillons toutes les contributions pour rendre cette visite encore plus complète et agréable.

## {SourceCode}

```d
import std.stdio;

// Allons-y !
void main()
{
    writeln("Bonjour tout le monde !");
}
```

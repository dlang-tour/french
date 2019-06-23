# Imports et modules

{{#img-right}}turtle.svg{{/img-right}}

L'une des décisions de conception fondamentale dans la langue D consiste à être cohérent et à éviter les cas critiques. Ceci s'appelle l'[*argument de la régression*](https://fr.wikipedia.org/wiki/Argument_de_la_r%C3%A9gression), un concept illustré par l'expression hindouïté [*"turtles all the way down"*](https://en.wikipedia.org/wiki/Turtles_all_the_way_down).

## Imports

Pour une simple application "Hello World" en D, il faut q'une déclaration `import` soit placée. La déclaration `import` permet de rendre publiques les fonctions et les types définies dans un **module**.

### Régression à l'infini

**Il n'est pas nécessaire** qu'une déclaration `import` soit placée tout en haut du fichier source. Celle-ci peut également être utilisée localement à l'intérieur des fonctions ou sous tout autres portées. Vous allez voir dans les prochains chapitres que ceci s'applique à presque tous les concepts présents dans D. Le language n'expose aucune contrainte arbitraire sur vous. 

### Imports selectifs

La librairie standard [Phobos](https://dlang.org/phobos/), est située dans le **package** `std` et ses modules peuvent être utilisés grâce à l'instruction `import std.MODULE`

L'instruction `import` peut être utilisée pour importer uniquement certains symboles d'un module:

    import std.stdio : writeln, writefln;

Ces `import`s séléctifs peuvent permettre de rendre un programme plus clair en explicitant la provenance de chaque symbole, et c'est également un moyen d'éviter les conflits entre différents symboles qui ont le même nom dans différents modules.

### Les imports correspondent aux répertoires et aux fichiers

Le système de module présent sur D - en contraste aux autres systèmes - est entièrement basé sur les fichiers.
Par exemple, `mon.chat` fait toujours référence à un fichier `chat.d` dans le dossier `mon/`.
Le dossier `mon/` doit être dans le répertoire de travail courant ou dans un des répertoires définis explicitement par `-I`.
Finalement, afin de faciliter la séparation de grands modules en plusieurs petits fichiers, plutôt que d'utiliser `chat.d`, un dossier `chat/` pourrait être utilisé également.
Le compilateur D essaiera alors de charger `mon/chat/package.d` plutôt que `mon/chat.d`.

La convention (non stricte) pour les fichiers `package.d` est d'importer publiquement tous les autres modules du même dossier.

## {SourceCode}

```d
void main()
{
    import std.stdio;
    // ou import std.stdio : writeln;
    writeln("Bonjour le monde!");
}
```

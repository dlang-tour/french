# Imports et modules

Pour une simple application "Hello World" en D, il faut faire des `import`s. L'instruction `import` permet de rendre publiques les fonctions et les types définies dans un **module**.

La librairie standard [Phobos](https://dlang.org/phobos/), est située dans le **package** `std` et ses modules peuvent être utilisés grâce à l'instruction `import std.MODULE`

L'instruction `import` peut être utilisée pour importer uniquement certains symboles d'un module:

    import std.stdio : writeln, writefln;

Ces `import`s séléctifs peuvent permettre de rendre un programme plus clair en explicitant la provenance de chaque symbole, et c'est également un moyen d'éviter les conflits entre différents symboles qui ont le même nom dans différents modules.

Une instruction `import` n'a pas besoin d'être placée tout en haut du fichier source. Elle peut être utilisée dans une fonction où à n'importe quel autre *scope*.

## {SourceCode}

```d
void main()
{
    import std.stdio;
    // ou import std.stdio : writeln;
    writeln("Bonjour le monde !");
}
```

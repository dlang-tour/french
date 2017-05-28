# Gardes de portée

Les gardes de portée permettent d'exécuter des instructiions sous certaines conditions lorsqu'on sort de la portée courante:

* `scope(exit)` exécutera toujours les instructions
* `scope(success)` les instructions seront exécutées lorsqu'aucune exception n'aura été lancée
* `scope(failure)` les instructions seront exécutées lorsqu'une exception aura été lancée avant la sortie de la portée

Utiliser les gardes de portée rend le code bien plus propre et permet de placer l'allocation des ressources et leur libération l'un à côté de l'autre. Ils améliorent également la sûreté du programme, pusqu'ils s'assurent que le code de libération des ressources est **toujours** appelé, indépendament de l'exécution du code à l'exécution.

La fonctionnalité de `scope` du D remplacement l'idiome RAII, souvent utilisé en C++ et qui ammène souvent à des objets complexes pour la gestion des ressources mémoires.

### Pour aller plus loin

- [`scope` dans _Programming in D_](http://ddili.org/ders/d.en/scope.html)

## {SourceCode}

```d
import std.stdio : writefln, writeln;

void main()
{
    writeln("<html>");
    scope(exit) writeln("</html>");

    {
        writeln("\t<head>");
        scope(exit) writeln("\t</head>");
        "\t<title>%s</title>".writefln("Salut");
    } // Le scope(exit) deux lignes plus haut
      // est exécuté ici

    writeln("\t<body>");
    scope(exit) writeln("\t</body>");

    writeln("\t\t<h1>Bonjour à tous!</h1>");

    // les gardes de portée permettent de placer
    // les allocations et le code de nettoyage
    // l'un à côté de l'autre
    import core.stdc.stdlib : free, malloc;
    int* p = cast(int*) malloc(int.sizeof);
    scope(exit) free(p);
}
```

# Utiliser D

D est fourni avec un compilateur : `dmd`, 
un outil pour exécuter du code D en tant que script : `rdmd` 
et un gestionnaire de dépendances : `dub`

### Le compilateur DMD

*DMD* compile des fichiers D pour créer un binaire exécutable.
On peut utiliser *DMD* en ligne de commande en lui fournissant
un fichier:

  dmd hello.d
  
Il existe beaucoup d'options qui vous permettent de modifier le
comportement de *DMD*. Explorez la [documentation](https://dlang.org/dmd.html#switches)
ou exécutez `dmd --help` pour un aperçu des options disponibles.

### Compilation "on-the-fly" avec `rdmd`

L'utilitaire `rdmd`, fourni avec *DMD*, compile un programme et 
ses dépendances puis exécute automatiquement le résultat:

  rdmd hello.d
 
Sur les systèmes UNIX, la ligne spéciale `#!/usr/bin/env rdmd` pour être
mis en première ligne d'un fichier D pour pouvoir utiliser ce dernier
comme un script.

Explorez la [documentation](https://dlang.org/rdmd.html) ou exécutez
`rdmd --help` pour plus d'informations sur les options.

### Le gestionnaire de dépendances `dub`

Le gestionnaire de dépendances standard de D est [`dub`](http://code.dlang.org).
Quand `dub` est installé, on peut créer un nouveau projet en exécutant la commande:

  dub init hello
  
Exécuter `dub` dans un dossier va récupérer toutes les dépendances, les compiler
avec votre application, et exécuter cette dernière.
`dub build` se contente de compiler le projet.

La [documentation](https://code.dlang.org/docs/commandline) et la commande `dub help`
vous donneront un aperçu des fonctionnalités de `dub`.

Tous les paquets disponibles par `dub` sont repertoriés sur [code.dlang.org](https://code.dlang.org)

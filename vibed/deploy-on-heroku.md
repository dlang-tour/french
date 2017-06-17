# Déployer sur Heroku

### Prérequis

- Un [compte](https://signup.heroku.com/login) Heroku
- [git](https://git-scm.com/) installé
- Votre application doit compiler sans erreurs en utilisant dmd (`dub build`)

#### 1 : préparez le code

Heroku a besoin de savoir comment communiquer avec une application déployée. Il y a donc une variable d'environnement `PORT` qui doit être injectée dans l'application. Cette dernière doit ensuite écouter sur ce port. Pour le développement, un port par défaut (ici __8080__) est mis en place:

```d
shared static this()
{
    //...
    auto settings = new HTTPServerSettings;
    // Fourni un port par défaut dans le cas où la variable $PORT n'existe pas.
    settings.port = environment.get("PORT", "8080").to!ushort;
    listenHTTP(settings, router);
}
```

On peut également créer un fichier `Profile`, c'est à dire un fichier texte à la racine du projet, qui déclare explicitement quelle commande doit être exécutée pour démarrer l'application.

Le fichier `Procfile` de notre application d'exemple ressemble à ça:

```
web: ./hello-world
```

### 2 : Préparez l'application

Avant d'aller plus loin, connectez vous à Heroku en ligne de commande en utilisant [Heroku Toolbelt](https://toolbelt.heroku.com/standalone).

Ça permet d'accéder à l'interface en ligne de commande de Heroku, qui peut être utilisée pour gérer vos applications et vos extensions.

Après avoir installé le toolbelt (**NdT**: ceinture d'outils) Heroku, lancez la commande suivante:

```
$ heroku login
```

### 3: Créez l'application

Rendez vous sur le [dashboard heroku](https://dashboard.heroku.com) et créez une nouvelle application. Après avoir fait ceci, mémorisez le nom de l'application, vous en aurez besoin plus tard.

Ou utilisez la ligne de commande comme ceci:

```
$ heroku create
Creating app... done, ⬢ rocky-hamlet-67506
https://rocky-hamlet-67506.herokuapp.com/ | https://git.heroku.com/rocky-hamlet-67506.git
```

### Déploiement par git

Pour déployer directement l'application, utilisez les commandes `git`. Un *git remote* séparé doit être ajouté pour pouvoir y envoyer les nouvelles versions.
Le nom de l'application que l'on a récupéré juste avant doit être passé en argument, ici c'est *rocky-hamlet-67506*:

```
$ heroku git:remote -a rocky-hamlet-67506
```

Notez que le nouveau *remote* est ajouté à la configuration de git:

```
$ git remote -v
heroku https://git.heroku.com/rocky-hamlet-67506.git (fetch)
heroku	https://git.heroku.com/rocky-hamlet-67506.git (push)
```

### Ajouter le buildpack

Les buildpacks sont responsable de la génération des ressources et de la compilation du code.

Pour plus d'informations, consultez la [documentation d'heroku](https://devcenter.heroku.com/articles/buildpacks)

Pour le déploiement, le [buildpack vibe.d](https://github.com/MartinNowak/heroku-buildpack-d) peut être utilisé.

```
$ heroku buildpacks:set https://github.com/MartinNowak/heroku-buildpack-d
```

Par défaut, le buildpack utilise la dernière version de `dmd`. Il est possible d'utiliser LDC ou GDC et de choisir une version specifique du compilateur en ajoutant un fichier `.d-compiler` au projet.

Utilisez `dmd`, `ldc` ou `gdc` pour sélectionner la dernière version d'un compilateur ou `dmd-2.0xxx`, `ldc-1.0xxx` ou `gdc-4.9xxx` pour sélectionner une version spécifique du compilateur.

### Déployer le code

Procédez comme d'habitude avec git et écrivez votre application géniale.

Pour publier une nouvelle version, pushez simplement la dernière version vers le remote heroku.

```
$ git add .
$ git commit -am "My first vibe.d release"
$ git push heroku master
Counting objects: 9, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 997 bytes, done.
Total 9 (delta 0), reused 0 (delta 0)

-----> Fetching custom git buildpack... done
-----> D (dub package manager) app detected
-----> Building libevent
-----> Building libev
-----> Downloading DMD
-----> Downloading dub package manager
-----> Setting PATH:
-----> Initializing toolchain
-----> Building app
       Running dub build ...
Building configuration "application", build type release
Running dmd (compile)...
Compiling diet template 'index.dt' (compat)...
Linking...
       Build was successful
-----> Discovering process types
       Procfile declares types -> web
-----> Compiled slug size: 3.5MB
-----> Launching... done, v4
       https://rocky-hamlet-67506.herokuapp.com/ deployed to Heroku
To git@heroku.com:rocky-hamlet-67506.git
 * [new branch]      master -> master
```

Ouvrez l'application dans votre navigateur avec la commande suivante:

    $ heroku open

# Conteneurs dynos

Après le déploiement, l'application s'exécute dans un conteneur web dyno. Dyno est un conteneur léger qui exécute la commande spécifiée dans le `Procfile`.

Utiliser la commande `ps` permet de vérifier combien de conteneurs dynos sont actuellement en train de s'exécuter

```
$ heroku ps
Free dyno hours quota remaining this month: 550h 0m (100%)
For more information on dyno sleeping and how to upgrade, see:
https://devcenter.heroku.com/articles/dyno-sleeping

No dynos on ⬢ rocky-hamlet-67506
```

Par défaut, l'application est déployée dans un dyno gratuit qui n'accepte pas les requêtes. Les dynos gratuis se metteront en veille après une demi heure d'inactivité. Cela cause un délai de quelques secondes pour la première requête lorsque le conteneur est en veille.

Pour démarrer le dyno, exécutez la commande suivante:

    $ heroku ps:scale web=1

### Visionner les logs

Heroku traite les logs comme des flux d'évenements triés par ordre chronologiques qui sont rassemblés à partir de toutes les les flux de sortie de vos applications, ce qui permet d'avoir un log global:

    $ heroku logs --tail

## Plus d'informations

Après le déploiement de votre application sur Heroku, vous pouvez rendre votre application encore plus géniale en utilisant des add-ons. Par exemple:

- [Postgresql](https://elements.heroku.com/addons/heroku-postgresql)
- [MongoDb](https://elements.heroku.com/addons/mongohq)
- [Logging](https://elements.heroku.com/addons#logging)
- [Caching](https://elements.heroku.com/addons#caching)
- [Error and exceptions](https://elements.heroku.com/addons#errors-exceptions)

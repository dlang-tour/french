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

Le fichier `Profile` de notre application d'exemple ressemble à ça:

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

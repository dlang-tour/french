# Serveur web

vibe.d permet d'écrire des serveurs web HTTP(S) très rapidement:

```d
auto settings = new HTTPServerSettings;
settings.port = 8080;
listenHTTP(settings, &foo);
```

On démarre ainsi un serveur web sur le porte 8080, et toutes les requêtes seront gérées par la fonction `foo`:

```d
void foo(HTTPServerRequest req,
    HTTPServerResponse res) { ... }
```

Pour faciliter les cas d'utilisation typiques et la configurations des différents chemins d'accès, une classe `URLRouter` est fournie, qui permet de connecter des fonctions pour gérer des requêtes `GET`, `POST`, etc ... On utilise pour celà les méthodes `.get("chemin", fonction)` ou `.post("chemin", fonction), ou on connecte une classe d'*interface web* qui implémente les chemins du serveur web en tant que méthodes:

```
auto router = new URLRouter;
router.regiserWebInterface(new WebService);
listenHTTP(settings, router);
```

Les chemins de l'interface web personnalisée `WebService` seront automatiquement déduits à partir des méthodes de la classe, selon un mécanisme simple:

* `index()` gérera `/`
* `getName()` gérera la requête `GET` sur `/name`
* `postUsername()` gérera la requête `POST` sur `/username`

Des chemins customisés peuvent être créés en associant un attribut `@path("/hello/world")` à une méthode. Les paramètres des requêtes `POST` peuvent être rendus accessibles dans la fonction en utilisant des paramètres avec le préfixe `_`. Il est aussi possible de spécifier les paramètres dans le chemin en lui-même:

```d
@path("/my/api/:id")
void foo(int _id)
```

Il n'est pas nécessaire de passer un `HTTPServerResponse` et un `HTTPServerRequest` en paramètre à chaque fonction. vibe.d vérifie statiquement la liste des paramètres des fonctions et les passe si nécessaire.

## {SourceCode:disabled}

```d
import vibe.d;

class WebService
{
    /*
    On utilise des variables de sessions pour
    avoir des informations persistantes pendant
    toutes les requêtes d'un client
    */
    private SessionVar!(string, "username")
        username_;

    /*
    Par défault, les requêtes du chemin racine
    ("/") sont dirigés vers la méthode index
    */
    void index(HTTPServerResponse res)
    {
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <form action="/username" method="POST">
        Your name:
        <input type="text" name="username">
        <input type="submit" value="Submit">
        </form>
        </body>
        </html>};

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    /*
    L'attribut @path peut être utilisé pour
    personnaliser les redirections d'URL. Ici
    les requêtes vers "/name" seront liés à la
    méthode getName
    */
    @path("/name")
    void getName(HTTPServerRequest req,
            HTTPServerResponse res)
    {
        import std.string : format;

        // Génère un en-tête d'information
        // composé de balises <li> en inspectant
        // les propriétés du header
        string[] headers;
        foreach(key, value; req.headers) {
            headers ~=
                "<li>%s: %s</li>"
                .format(key, value);
        }
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <h1>Your name: %s</h1>
        <h2>Headers</h2>
        <ul>
        %s
        </ul>
        </body>
        </html>}.format(username_,
                headers.join("\n"));

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }

    void postUsername(string username,
            HTTPServerResponse res)
    {
        username_ = username;
        auto contents = q{<html><head>
            <title>Tell me!</title>
        </head><body>
        <h1>Your name: %s</h1>
        </body>
        </html>}.format(username_);

        res.writeBody(contents,
                "text/html; charset=UTF-8");
    }
}

void helloWorld(HTTPServerRequest req,
        HTTPServerResponse res)
{
    res.writeBody("Hello");
}

shared static this()
{
    auto router = new URLRouter;
    router.registerWebInterface(new WebService);
    router.get("/hello", &helloWorld);

    auto settings = new HTTPServerSettings;
    // Nécessaire pour utiliser SessionVar
    settings.sessionStore =
        new MemorySessionStore;
    settings.port = 8080;
    listenHTTP(settings, router);
}
```

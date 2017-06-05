# Interface REST JSON

Vibe.d permet de créer rapidement un service web JSON. Si on veut implémenter la sortie JSON suivante pour répondre à la requête HTTP sur `/api/v1/chapitres`:

```json
[
    {
        "title": "Coucou",
        "id": 1,
        "sections": [
            {
                "title": "Le monde",
                "id" 1
            }
        ]
    },
    {
        "title": "Avancé",
        "id": 2,
        "sections": []
    }
]
```

On définit d'abord une interface qui implémente les fonctions correspondantes et les `struct`s D qui vont être sérialisés:

```d
interface IRest
{
    struct Section
    {
        string title;
        int id;
    }

    struct Chapter
    {
        string title;
        int id;
        Section[] sections;
    }

    @path("/api/v1/chapters")
    Chapter[] getChapters();
}
```

Pour remplir effectivement les structures de données, on doit hériter de cette interface et implémenter la logique métier:

```d
class Rest: IRest
{
    Chapter[] getChapters()
    {
        // remplir
    }
}
```

À partir d'un `URLRouter`, on enregistre une instance de `Rest` et tout fonctionne!

```d
auto router = new URLRouter;
router.registerRestInterface(new Rest);
```

Le *générateur d'interface REST* de vibe.d permet aussi de faire la même chose dans l'autre sens avec des requêtes POST qui transforment un objet JSON reçu en structure D.

L'interface REST peut être utilisée pour générer un client REST qui envoie des requêtes JSON à un serveru de façon transparente. C'est très utile lorsque le code est partagé entre le client et le serveur.

```d
auto api = new RestInterfaceClient!IRest("http://127.0.01:8000");
// envoie un GET /api/v1/chapters et désérialise
// la réponse dans IRest.Chapter[]
auto chapters = api.getChapters();
```

## {SourceCode:disabled}

```d
import vibe.d;

interface IRest
{
    struct Section
    {
        string title;
        int id;
    }

    struct Chapter
    {
        string title;
        int id;
        Section[] sections;
    }

    @path("/api/v1/chapters")
    Chapter[] getChapters();

    /*
    Envoie des données de la forme:
        { "title": "Langage D" }
    */
    @path("/api/v1/add-chapter")
    @method(HTTPMethod.POST)
    int addChapter(string title);
}

class Rest: IRest
{
    private Chapter[] chapters_;

    this()
    {
        chapters_ = [ Chapter("Hello", 1,
                [ Section("World", 1) ] ),
                 Chapter("Advanced", 2) ];
    }

    Chapter[] getChapters()
    {
        return chapters_;
    }

    int addChapter(string title)
    {
        import std.algorithm : map, max, reduce;
        // Génère le prochaine ID
        auto newId = chapters_.map!(x => x.id)
                            .reduce!max + 1;
        chapters_ ~= Chapter(title, newId);
        return newId;
    }
}

shared static this()
{
    auto router = new URLRouter;
    router.registerRestInterface(new Rest);

    auto settings = new HTTPServerSettings;
    settings.port = 8080;
    listenHTTP(settings, router);
}
```

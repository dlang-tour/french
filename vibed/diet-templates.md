# templates DIET

Pour faciliter l'écriture de pages web, vibe.d permet d'utiliser les [templates DIET](https://vibed.org/templates/diet), qui simplifient l'écriture de pages HTML.
DIET est basé sur les [templates Jade](http://jade-lang.com).

    doctype html
    html(lang="fr")
        head
            // Le code D est évalué
            title #{titrePage}

            // attributs
            script(type='text/javascript')

              if (foo) bar(1 + 5)
            // ID = body-id
            // style = le-style
            body#body-id.le-style
                h1 template DIET

La syntaxe est basée sur l'indentation et les balises n'ont pas besoin d'être fermées.

Tous les templates DIET sont compilés et sont stockés en mémoire pour être ultra-efficaces. Les templates DIET permettent d'utiliser du code D qui est évalué lors de la phase de rendu des pages. Les expressions sont contenues dans `#{ 1 + 1 }` et peuvent être utilisées partout dans le template. Toutes les lignes de code D sont préfixées par `-`:

```
- foreach(title; titles)
h1 #{title}
```

On peut utiliser des expressions complexes de cette façon, et des fonctions peuvent être définies qui produisent du html.

Les templates DIET sont compilés en utilisant la **CTFE** et doivent se situer dans le dossier `views` dans un projet vibe.d standard. Pour compiler un template DIET, il faut utiliser la fonction `render` dans le gestionnaire URL:

```d
void foo(HTTPServerResponse res)
{
    string titre = "Salut";
    int test = 10;
    res.render!("mon-template.dt", titre, test);
}
```

Toutes les variables D accessibles dans un template DIET sont passés en paramètres de modèle de `render`.

## {SourceCode:disabled}

```d
doctype html
html
  head
    title D statement test
  body
    - import std.algorithm : min;
    p Four items ahead:
    - foreach( i; 0 .. 4 )
      - auto num = i+1;
      p Item
        b= num
    p Prints 8:
    p= min(10, 2*6, 8)
```

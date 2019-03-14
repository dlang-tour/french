# Tableaux associatifs

D comprend nativement des *tableaux associatifs*, aussi connus sous le nom de tableaux de hachage. Un tableau associatif avec des clés de type `string` et des valeurs de type `int` est déclaré comme ceci:

    int[string] tab;

On peut alors accéder à une valeur par sa clé, et donc la modifier:

    tab["clé1"] = 10;

Pour tester si une clé existe dans un tableau associatif, l'expression `in` peut être utilisée:

    if("clé1" in tab)
        writeln("Oui");

Les expressions `in`retournent un pointeur vers la valeur si elle peut être trouvée, et un pointeur `null` sinon. Le test d'existance et la modification de la valeur peuvent donc être combinés:

    if (auto val = "clé1" in tab)
        *val = 20

Accéder à une clé qui n'existe pas provoque une `RangeError` qui termine immédiatement l'exécution de l'application. Pour un accès sur avec une valeur par défaut, `get(cle, valeurDefaut)` peut être utilisé.

Les tableaux associatifs ont une propriété `.length` comme les tableaux et fournissent une méthode `.remove(cle)` pour supprimer des éléments en précisant leur clé.
Il est laissé comme exercice au lecteur d'explorer les deux ranges particulières `.byKey` et `byValue`.

### Pour aller plus loin

- [Tableaux associatifs dans _Programming in D_](http://ddili.org/ders/d.en/aa.html)
- [Spécification des tableaux associatifs](https://dlang.org/spec/hash-map.html)
- [std.array.byPair](http://dlang.org/phobos/std_array.html#.byPair)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Sépare le texte en mots et retourne un tableau
associatif qui fait correspondre à chaque mot
le nombre de fois où ce dernier apparait dans 
le texte.

Params:
    text = texte à séparer
*/
int[string] wordCount(string text)
{
    // La fonction splitter sépare de façon
    // paresseuse le texte en une range
    import std.algorithm.iteration : splitter;
    import std.string : toLower;

    // Indexé par les mots et contenant le 
    // compte des mots
    int[string] words;

    foreach(word; splitter(text.toLower(), " "))
    {
        // Incrémente le compte de mots
        // si le mot a été trouvé.
        // Les entiers sont à 0 par défaut.
        words[word]++;
    }

    return words;
}

void main()
{
    string text = "programmer en D est fun";

    auto wc = wordCount(text);
    writeln("Nombre de mots: ", wc);

    // itérations possibles:
    // byKey, byValue, byKeyValue
    foreach (word; wc.byValue)
        writeln(word);
}
```

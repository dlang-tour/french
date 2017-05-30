# Unicode en D

Unicode est un standard international pour encoder et représenter du texte informatiquement. D support complètement l'unicode dans le langage et dans sa librairie standard.

## Qu'est ce que c'est ? Pourquoi ?

Les ordinateurs, au niveau éléctronique, n'ont aucune idée de ce qu'est du texte, puisqu'ils ne travaillent qu'avec des nombres. Il faut donc un moyen pour le code d'interagir avec des données textuelles et des les représenter sous forme binaire. La méthode de transformation est appelée un *encodage*, et Unicode est un encodage.

Pour voir la représentation numériques des chaînes de l'exemple, lancez simplement le programme.

Unicode est unique au monde, car il permet de représenter tous les langages du monde avec un seul encodage. Avant Unicode, les ordinateurs de différents fabriquants et vendus dans différentes régions du monde avaient du mal à communiquer, et dans certains cas l'encodage n'était pas supporté du tout, ce qui rendait impossible l'affichage du texte. 

Pour plus d'informations sur Unicode et les détails techniques, lisez l'article wikipédia sur Unicode dont vous pouvez trouver le lien dans la section "Pour aller plus loin"

## Comment ?

Unicode a résolu la plupart de ces problèmes et est très bien supporté sur toutes les machines modernes. D a appris des erreurs des langages plus anciens, et toutes les chaînes de caractères en D sont des chaînes Unicode, alors que les chaînes de caractères en C ou en C++ sont juste des tableaux d'octets.

En D, `string`, `wstring` et `dstring` sont respectivement des chaînes de caractères encodés en UTF-8, UTF-16 et UTF-32. Les types de leur caractères sont `char`, `wchar` et `dchar`.

D'après la spécification, c'est une erreur de stocker une chaîne des données non Unicode dans un type de string; attendez-vous à ce que vos programmes plantent si votre chaîne est mal encodée.

Pour stocker d'autres encodages, ou pour obtenir un comportement similaire à celui de C/C++, vous pouvez utiliser `ubyte[]` ou `char*`.

## Les strings dans les algorithmes de range

*Il est conseillé de lire le chapitre sur les [algorithmes de ranges](gems/range-algorithms) avant de lire cette partie*

Il est important d'avoir plusieurs choses à l'esprit quand on utilise Unicode en D.

Premièrement, pour le confort de programmation, quand on itère sur une `string` en utilisant une fonction de range, Phobos encodera tous les éléments des `string`s et des `dstring`s en points de code UTF-32. Cette pratique, appelée **auto-décodage** signifie que:

```d
static assert(is(typeof(utf8.front) == dchar));
```

Ce comportement impliques plusieurs choses, parmi lesquelles le fait que `std.traits.hasLength!string == false`. Pourquoi ? Parce que, en terme d'API des ranges, la méthode `length` des `string`s retournera le **nombre d'éléments de la chaîne** et pas le nombre d'éléments sur lesquels la chaîne va itérer.

Dans l'exemple, vous pouvez voir pourquoi ces deux choses ne sont pas toujours égales. De ce fait, les algorithmes de range dans Phobos se comportent comme si les `string`s n'avaient pas d'informations sur leur longueur.

Pour plus d'informations et de détails techniques sur l'auto-décodage, jetez un coup d'6œil aux liens de la section "Pour aller plus loin".

### Pour aller plus loin

- [Unicode sur Wikipedia](https://fr.wikipedia.org/wiki/Unicode)
- [Les fonctions Unicode dans Phobos](https://dlang.org/phobos/std_uni.html)
- [Outils pour décoder et encoder de l'UTF dans Phobos](https://dlang.org/phobos/std_utf.html)
- [Un article sur l'auto-décodage](https://jackstouffer.com/blog/d_auto_decoding_and_you.html)
- [Un article détaillé sur les avantages d'utiliser UTF-8](http://utf8everywhere.org/)

## {SourceCode}

```d
import std.range.primitives : empty,
    front, popFront;
import std.stdio : write, writeln;

void main()
{
    string utf8 = "å ø ∑ 😦";
    wstring utf16 = "å ø ∑ 😦";
    dstring utf32 = "å ø ∑ 😦";

    writeln("utf8 length: ", utf8.length);
    writeln("utf16 length: ", utf16.length);
    writeln("utf32 length: ", utf32.length);

    foreach (item; utf8)
    {
        auto c = cast(ubyte) item;
        write(c, " ");
    }
    writeln();
    
    foreach (dchar item; utf16)
    {
        auto c = cast(ushort) item;
        write(c, " ");
    }
    writeln();

    // Le résultat de l'auto-décodage
    static assert(
        is(typeof(utf8[0]) == immutable(char))
    );
    static assert(
        is(typeof(utf8.front) == dchar)
    );
}
```

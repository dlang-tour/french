# Exceptions

Ce guide ne concerne que les `Exception`s utilisateur. Les `Error`s du système sont généralement fatales et ne devraient **jamais** être interceptées.

### Attraper les exceptions

Un cas typique d'utilisation d'exceptions est la validation de données entrées par l'utilisateur. Quand une exception est lancée, la pile sera déroulée jusqu'à ce qu'un bloc qui attrape l'exception soit trouvé.

```d
try
{
    lireTexte("fichierBidon");
}
catch(FileException e)
{
    // ...
}
```

Il est possible d'y avoir plusieurs blocs `catch`s et un bloc `finally` qui est exécuté que l'erreur soit survenue ou non. Les exceptions sont lancées avec l'instruction `thrown`.

```d
try
{
    throw new StringException("Vous ne passerez pas !");
}
catch(FileException e)
{
    // ...
}
catch(StringException e)
{
    // ...
}
finally
{
    // ...
}
```

Nous verrons dans un prochain chapitre que les gardes de portée sont souvent une meilleure solution que le motif `try-finally`.

### Exceptions personnalisées

On peut facimement hériter d'`Exception` et créer des exceptions personnalisées:

```d
class UtilisateurNonTrouve : Exception
{
    this(string message, string fichier = __FILE__, size_t ligne = __LINE__)
    {
        super(message, fichier, ligne);
    }
}

throw new UtilisateurNonTrouve("D-Man est en congés");
```

### Un monde sûr avec `nothrow`

Le compilateur D peut garantir qu'une fonction ne peut pas lancer d'exceptions. Ces fonctions sont annotées avec le mot-clé `nothrow`.

```d
bool lessThan(int a, int b) nothrow
{
    writeln("monde non sûr"); // cette fonction peut lancer une exception, donc cette
                              // ligne ne compilera pas
    return a < b;
}
```

### std.exception

`std.exception` fournit une fonction `enforce` qui peut être utilisée comme `assert` mais qui lance une `Exception` à la place d'une `AssertError`:

```d
import std.exception : enforce;

float magique = 1_000_000_000;

enforce(magique + 42 - magique == 42, "Les mathématiques avec les nombres à virgule flottante sont marrantes");

// Lance une exception personnalisée
enforce!StringException('a' != 'A', "algorithme sensible à le casse");
```

Cependant, il y a d'autres choses dans `std.exception`. Par exemple, quand l'erreur n'est pas fatale, il est possible d'utiliser `collectException` dessus:

```d
import std.exception: collectException;
auto e = collectException(uneOperationDangereuse());
if(e)
    writlen("L'opération dangereuse a raté avec ", e);
```

### Pour aller plus loin

- [Les exceptions en D](https://dlang.org/exception-safe.html)
- [std.exception](https://dlang.org/phobos/std_exception.html)
- [core.exception](https://dlang.org/phobos/core_exception.html) de bas niveau
- [object.Exception](https://dlang.org/library/object/exception.html) - classe parente de toutes les exceptions que l'on peut attraper

## {SourceCode}

```d
import std.file : FileException, readText;
import std.stdio : writeln;

void main()
{
    try
    {
        readText("fichierBidon");
    }
    catch (FileException e)
    {
		writeln("Message:\n", e.msg);
		writeln("Fichier: ", e.file);
		writeln("Ligne: ", e.line);
		writeln("Copie de pile:\n", e.info);

        // Le format par défaut pour aussi être
        // utilisé
        // writeln(e);
    }
}
```

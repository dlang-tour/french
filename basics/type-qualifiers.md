# Mutabilité

D est un langage typé statiquement: une fois qu'une variable a été déclarée, son type ne peut être modifié. Cela permet au compilateur d'empêcher certains bugs et de mettre en place certains restrictions à la compilation.
Un typage sûr permet de créer des programmes importants sans erreurs et facilite leur maintenance.

Il y a plusieurs qualificateurs de types en D, mais les plus utilisés sont `const` et `immutable`.

### `immutable`

En plus de son système de typage statique, D fourni des qualificateurs de types (parfois appelés "constructeurs de types") qui assurent un certain nombre de contraintes sur certains objets.
Par exemple, un objet `immutable` ne peut être initialisé qu'une fois et il n'est plus autorisé à changer après ça.

```d
immutable int err = 5;
// ou : immutable err = 5 et le type est déduit
err = 5; // ne compilera pas : ERREUR
```

Les objets `immutable` peuvent donc être partagées entre différents threads non-synchronisés sans risques puisqu'ils ne changent pas par définition. Cela impliquement également que les objets `immutable` peuvent être mis en cache parfaitement.

### `const`

Les objets `const` ne peuvent pas être modifiés non plus. Cette restriction n'est valide que dans la portée courante. Un pointeur `const` peut être créé à partir d'un objet *mutable* ou `immutable`. Cela signifie qu'un objet peut être `const` dans la portée courante, mais que quelqu'un peut peut-être le modifier dans un autre contexte. Il est courant pour les API d'accepter des arguments `const` pour assurer qu'elles ne modifieront pas les données qu'on leur fournit en entrée, et pour permettre à la fonction de traiter des données *mutables* ou `immutable`.

```d

void foo ( const char[] s )
{
    // si elle n'est pas commentée, la prochaine ligne
    // produira une erreur (on ne peut pas modifier un const):
    // s[0] = 'x';

    import std.stdio : writeln;
    writeln(s);
}

// grâce à const, ces deux exemples vont compiler:
foo("abcd"); // une string est un tableau immutable
foo("abcd".dup); // .dup retourne une copie mutable
```

`immutable` et `const` sont des qualificateurs de types _transitifs_, ce qui assure qu'une fois que `const` est appliqué à un type, cela s'applique récursivement à tous les composants de ce type.

### Pour aller plus loin

#### Références de base

- [Immutable dans _Programming in D_ (en anglais)](http://ddili.org/ders/d.en/const_and_immutable.html)
- [Scopes dans _Programming in D_ (en anglais)](http://ddili.org/ders/d.en/name_space.html)

#### Références avancées

- [const(FAQ)](https://dlang.org/const-faq.html)
- [Les qualificateurs de types en D](https://dlang.org/spec/const3.html)

## {SourceCode}

```d
import std.stdio : writeln;

void main()
{
    /**
    * Les variables sont mutables par défaut
    * et on peut les modifier:
    */
    int m = 100; // mutable
    writeln("m: ", typeof(m).stringof);
    m = 10; // correct

    /**
    * Pointeur vers un segment de mémoire mutable:
    */
    // Un pointeur const vers un segment de mémoire mutable est autorisé
    const int* cm = &m;
    writeln("cm: ", typeof(cm).stringof);
    // Par définition, `const` ne peut pas être modifié:
    // *cm = 100; // erreur !

    // Un `immutable` est garanti de rester
    // inchangé, il ne peut pas pointer vers
    // un segment de mémoire mutable
    // immutable int* im = &m; // erreur!

    /**
    * Pointeur vers un segment en lecture seule :
    */
    immutable v = 100;
    writeln("v: ", typeof(v).stringof);
    // v = 5; // erreur !

    // `const` peut pointer vers de la mémoire en lecture seul,
    // mais est également lecture seule.
    const int* cv = &v;
    writeln("*cv: ", typeof(cv).stringof);
    // *cv = 10; // erreur!
}
```

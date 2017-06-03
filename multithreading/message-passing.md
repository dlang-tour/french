# Passage de messages

À la place de gérer le parallélisme et la synchronisation à la main, D permet d'utiliser le *passage de messages* pour exploiter le puissance des CPU à plusieurs cœurs. Les threads communiquent avec des *messages*, qui contiennent des valeurs arbitraires, pour se partager le travail et se synchroniser. Ils s'interdisent de partager des données pour éviter beaucoup de problèmes liés au parallélisme.

Toutes les fonctions qui implémentent le passage de messages en D peuvent être trouvées dans le module [`std.concurrency`](https://dlang.org/phobos/std_concurrency.html). 

`spawn` créé un nouveau thread à partir d'une fonction fournie par l'utilisateur:

```d
auto threadId = spawn(&foo, thisTid);
```

`thisTid` est une variable définie dans `std.concurrency` qui fait référence au thread actuel, une information nécessaire pour le passage de messages. `spawn` prend une fonction en premier argument et un nombre arbitraire d'autres paramètres qui fonctionnent comme arguments de la fonction.

```
void foo(Tid parentTid)
{
    receive(
        (int i) { writeln("Un ", i, " a été envoyé!"); }
    );

    send(parentTid, "Fait");
}
```

La fonction `receive` fonctionne comme un `switch`-`case` qui réparti les valeurs envoyées par les autres threads à des delegates en fonction du type de la valeur.

Pour envoyer un message à un thread spécifique, on utilise la fonction `send` avec son id:

```d
send(threadId, 42);
```

`receiveOnly` peut être utilisé pour ne recevoir qu'un certain type de valeurs:

```d
string text = receiveOnly!string();
assert(text == "Done");
```

La famille des fonctions `receive` bloque l'exécution du thread jusqu'à réception d'un message dans sa "boite aux lettres".

### Pour aller plus loin

- [Échanger des messages entre thread](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=5)
- [La concurrence par passage de messages](http://ddili.org/ders/d.en/concurrency.html)
- [Pattern Matching avec receive](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=6)
- [Copie de fichiers multi-threadés](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=7)
- [Terminer l'exécution de threads](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=8)
- [Out-of-Band Communication](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=9)
- [Mailbox crowding](http://www.informit.com/articles/article.aspx?p=1609144&seqNum=10)

## {SourceCode}

```d
import std.stdio : writeln;
import std.concurrency : receive, receiveOnly,
    send, spawn, thisTid, Tid;

/*
Une structure personnalisée qui est utilisée
comme type de message pour coordonner une armée
de threads
*/
struct NumberMessage {
    int number;
    this(int i) {
        this.number = i;
    }
}

/*
CancelMessage est utilisé pour arrêter
l'exécution d'un thread
*/
struct CancelMessage {
}

/// Acquitter un CancelMessage
struct CancelAckMessage {
}

/*
La fonction principale des threads qui prend
l'id du thread parent en paramètre
*/
void worker(Tid parentId)
{
    bool canceled = false;
    writeln("Starting ", thisTid, "...");

    while (!canceled) {
      receive(
        (NumberMessage m) {
          writeln("Reçu un entier: ", m.number);
        },
        (string text) {
          writeln("Reçu une string: ", text);
        },
        (CancelMessage m) {
          writeln("Stop : ", thisTid, "...");
          send(parentId, CancelAckMessage());
          canceled = true;
        }
      );
    }
}

void main()
{
    Tid threads[];
    // Crée 10 threads fils.
    for (size_t i = 0; i < 10; ++i) {
        threads ~= spawn(&worker, thisTid);
    }

    // Les threads d'id paire reçoivent un 
    // nombre, les autres une chaîne de 
    // caractères
    foreach(int idx, ref tid; threads) {
        import std.string : format;
        if (idx  % 2)
            send(tid, NumberMessage(idx));
        else
            send(tid, format("T=%d", idx));
    }

    // Tous les threads reçoivent un message
    // cancel!
    foreach(ref tid; threads) {
        send(tid, CancelMessage());
    }

    // Et on attend que tous les threads aient
    // confirmé leur arrêt
    foreach(ref tid; threads) {
        receiveOnly!CancelAckMessage;
        writeln("Reçu un CancelAckMessage!");
    }
}
```

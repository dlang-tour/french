# Bases et I/O asynchrone

Par défaut, la fonction `main()` d'une application vibe.d est spécifiée dans un constructeur de module spécial `shared static this()`:

```d
import vibe.d;

shared static this()
{
    // code vibe.d ici
}
```

Un constructeur de module est exécuté une fois avant `main()`. Vibe.d implémente sa propre fonction `main()` qui cache les mécaniques internes à l'utilisateur.

Vibe.d utilise les *fibres* pour implémenter l'E/S asynchrone: chaque fois qu'un appel à un socket est bloquant, parce qu'il n'y a pas de données à lire par exemple, la fibre courante appelle `yield` et rend l'exécution à une autre opération. Quand les données sont disponibles, on reprend l'exécution:

```d
// Pourrait bloquer, mais vibe.d s'assure que
// l'on reprenne ici quand le socket est près
line = connection.readLine();
// Peut bloquer aussi
connection.write(line);
```

Le code a l'air *synchrone* et bloquerait normalement le thread courant, mais il ne le fait pas ! Le code est propre et concis, mais il utilise quand même la puissance des E/S asynchrones, qui nous permettent de gérer des milliers de connexions sur un seul processeur.

Toutes les fonctionnalités de vibe.d utilisent les opérations asynchrones par fibres sur les sockets, ainsi aucune inquiètude que cette connexion lente à MongoDB bloque toute l'application.

Regardez l'exemple qui montre comment implémenter un simple serveur echo basé sur TCP.

## {SourceCode:disabled}

```d
import vibe.d;

shared static this()
{
    // Écoute le porte 8080 (TCP).
    // Chaque fois qu'une nouvelle connexion
    // arrive, elle est gérée par le delegate
    // spécifié dans le deuxième paramètre. conn
    // est l'objet qui représente la connexion
    // TCP avec le client.
    listenTCP(8080, (TCPConnection conn) {
        string line;
        conn.write("ECHO server: salut !\r\n");
        conn.write("Tapez 'q' pour fermer\r\n");
        while (line != "quit") {
            line = cast(string) conn.readLine();
            conn.write("ECHO: " ~ line
              ~ "\r\n");
        }

        // La fin de l'exécution du delegate
        // fermera la connexion au client
    });
}
```

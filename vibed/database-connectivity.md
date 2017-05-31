# Connexion à des bases de données

Vibe.d permet d'accéder facimement à vos bases de données. **MongoDB** et **Redis** sont directement supportés par vibe.d, et on peut trouver des adaptateurs pour d'autres bases de données sur [code.dlang.org](https://code.dlang.org)

### MongoDB

L'accès à MongoDB se fait par la classe [`MongoClient`](http://vibed.org/api/vibe.db.mongo.client/MongoClient). L'implémentation n'a pas de dépendances externes et utilise les sockets asynchrones de vibe.d, la connexion n'est pas bloquante si il y a de la latence.

```d
auto client = connectMongoDB("127.0.0.1");
auto users = client.getCollection("users");
users.inser(Bson("peter"));
```

### Redis

Le support de Redis est également implémenté avec les sockets vibe.d et n'a pas de dépendances externes non pllus. On utilise la classe [`RedisDatabase`](http://vibed.org/api/vibe.db.redis.redis/RedisDatabase) qui permet d'exécuter des commandes sur un serveur Redis. Des structures et des fonctions sont également disponibles pour vous faciliter la vie, comme [`RedisList`](http://vibed.org/api/vibe.db.redis.types/RedisList) qui permet d'accéder à une liste stockée dans Redis de façon transparente.

### MySQL

Le support de MySQL sans dépendance à la bibliothèque MySQL officielle peut-être ajouté à vibe.d en utilisant [mysql-native](http://code.dlang.org/packages/mysql-native). Cette bibliothèque supporte les sockets non-bloquants de vibe.d aussi.

### Postgresql

Un client Postgresql complet est implémenté dans la bibliothèque externe [dpq2](http://code.dlang.org/packages/dpq2), qui est basée sur la bibliothèque officielle *libpq*. Elle utilise le système d'évènements de vibe.d pour implémenter un comportement asynchrone.

Une autre alternative à Postgresql est [ddb](http://code.dlang.org/packages/ddb) qui implémente un client Postgresql avec les sockets vibe.d et aucune dépendances externes.
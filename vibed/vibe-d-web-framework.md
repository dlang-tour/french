# Le framework web Vibe.d

[Vibe.d](http://vibed.org) est un puissant framework web. Cette visite va présenter quelques exemples de programme qui l'utilisent. Vibe.d a plusieurs avantages:

* Basé sur les fibres et une approche *asynchrone*, vibe.d permet d'écrire des serveurs web HTTP(S) très performants et des services web. Il permet d'écrire du code qui semble synchrone mais qui en réalité fait tout le travail asynchrone en arrière-plan.
* Un générateur d'interface web et de JSON facile à utiliser
* Support natif de Redis et de MongoDB qui rend facile la programmation de systèmes *backend* performants.
* Possibilité d'écrire des clients et des serveurs UPD et TCP en utilisant ce framework

Notez que les exemples de ce chapitre ne peuvent pas être exécutés dans le navigateur, car elles requièrent l'accès au réseau, qui est désactivé pour d'évidentes raisons de sécurité.

La façon la plus simple de créer un projet et d'installer `dub` et de créer un nouveau projet vibe.d avec le squelette prédéfini:

    dub init <nom-de-votre-projet> -t vibe.d

`dub` s'assurera que vibe.d est téléchargé et disponible pour compiler votre projet.

La livre [D Web development](https://www.packtpub.com/web-development/d-web-development) est une bonne introduction à ce framework.
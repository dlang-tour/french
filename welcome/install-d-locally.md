# Installer D en local

Vous pouvez [télécharger](http://dlang.org/download.html) la dernière version
de **DMD** (Digital Mars D), le compilateur de référence sur le site [dlang.org](https://dlang.org).
Vous pourrez ensuite procéder à l'installation en suivant ces instructions :

### Windows

* [Installateur](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd-{{latest-release}}.exe)
* ou: [Archive](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.windows.7z)
* En utilisant [chocolatey](https://chocolatey.org/packages/dmd): `choco install dmd`

### macOS

* `.dmg` [package](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.dmg)
* ou [Archive](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.osx.tar.xz)
* En utilisant [homebrew](http://brew.sh): `brew install dmd`

### Linux / FreeBSD / macOS

Pour installer rapidement `dmd` dans votre répertoire utilisateur, lancez la commande: `curl -fsS https://dlang.org/install.sh | bash -s dmd`

Des paquets sont fournis pour plusieurs distributions:

* [ArchLinux](https://wiki.archlinux.org/index.php/D_(programming_language))
* [Debian/Ubuntu](http://d-apt.sourceforge.net).
* [Fedora/CentOS](http://dlang.org/download.html#dmd)
* [Gentoo](https://wiki.gentoo.org/wiki/Dlang)
* [OpenSuse](http://dlang.org/download.html#dmd)

### Autres compilateurs

En plus du compilateur de référence, qui utilise son propre backend, il y a deux autres
compilateurs qui peuvent être récupéré sur la page de téléchargement de [dlang.org](https://dlang.org):

* [**GDC**](http://gdcproject.org/downloads) qui utilise le backend GCC
* [**LDC**](https://github.com/ldc-developers/ldc#installation) qui utilise le backend LLVM

GDC  et LDC n'utilisent pas toujours la version la plus récente du frontend de DMD,
mais ils permettent un meilleur niveau d'optimisation et la support d'autres 
plateformes commme par exemple ARM.

Consultez le wiki pour [plus d'informations](https://wiki.dlang.org/Compilers)

# Évaluation de fonction à la compilation (CTFE)

La CTFE est un mécanisme qui permet au compilateur d'évaluer une fonction au moment de la **compilation**. Il n'est pas nécessaire de programmer les fonctions différement pour utiliser cette fonctionnalité. Il suffit qu'une fonction prenne en arguments des valeurs connues à la compilations, et le compilateur D peut décider d'interpréter cette fonction à ce moment.

```d
// Le résultat peut être calculé à
// la compilation. Vérifiez l'assembleur
// généré, il ne contiendra pas d'appel
// à la fonction!
static val = sqrt(50);
```

Les mots clés comme `static`, `immutable` ou `enum` indiquent au compilateur d'utiliser la CTFE dès que c'est possible. Le grand avantage de cette technique est que les fonctions n'ont pas besoin d'être réecrites pour l'utiliser, et le même code peut parfaitement être partagé entre l'interprétation à la compilation et à l'exécution:

```d
int n = faireQqchALexecution();
// Même fonction qu'avant mais
// cette fois, juste appelée à 
// l'exécution
auto val = sqrt(n);
```

Un exemple parfait en D est la librairie [`std.regex`](https://dlang.org/phobos/std_regex.html). Elle fourni un type `ctRegex` qui utilises les *string mixins* et la CTFE pour générer des automates d'expressions régulières ultra optimisés à la compilation. Le même code peut être réutilisé pour l'autre version `regex` qui permet de compiler des expressions régulières pendant l'exécution.

```d
auto compileRegex = ctRegex!(`^.*/([^/]+)/?$`);
auto expReg = regex(`^.*/([^/]+)/?$`);

// compileRegex et expReg seront interchangeables
// mais compileRegex sera plus rapide !
```

Toutes les fonctionnalités du langage ne sont pas disponibles pendant l'évaluation de fonction à la compilation, mais l'ensemble des fonctionnalités supportées grandit à chaque nouvelle version du compilateur.

### Pour aller plus loin

- [Introduction aux expressions régulières en D](https://dlang.org/regular-expression.html)
- [std.regex](https://dlang.org/phobos/std_regex.html)
- [Compilation conditionelle](https://dlang.org/spec/version.html)

## {SourceCode}

```d
import std.stdio : writeln;

/*
Calcule la racine carrée d'un nombre
en utilisant la méthode de Newton.

Params:
    x = le nombre dont on veut la racine carrée

Returns: racine carrée de x
*/
auto sqrt(T)(T x) {
    // seuil à partir duquel affiner
    // l'approximation ne vaut pas 
    // une autre itération
    enum GoodEnough = 0.01;

    import std.math : abs;
    
    // Choisir des bonnes valeurs de départ.
    T z = x*x, old = 0;
    int iter;
    while (abs(z - old) > GoodEnough) {
        old = z;
        z -= ((z*z)-x) / (2*z);
    }

    return z;
}

void main() {
    double n = 4.0;
    writeln("La racine carrée de 4 à l'exécution = ",
        sqrt(n));
    static cn = sqrt(4.0);
    writeln("La racine carrée de 4 à la compilation = ",
        cn);
}
```
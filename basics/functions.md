# Fonctions

Une fonction a déjà été introduite: `main()` - le point de départ de chaque programme en D. Une fonction peut retourner une valeur (où être declarée avec `void` si elle ne retourne rien) et accepter un nombre arbitraire d'arguments:

```d
int additioner(int gauche, int droite){
    return gauche + droite;
}
```

### Type de retour `auto`

Si le type de retour défini est `auto`, le compilateur D déduit le type de retour automatiquement. Si il y a plusieurs instructions `return`, elles doivent renvoyer des types compatibles.

```d
auto additioner(int gauche, int droite) // retourne un entier
{
    return gauche + droite;
}

auto inferieurOuEgal() // retourne un double
{
    if(gauche <= droite)
        return 0;
    else
        return 1.0;
}
```

### Arguments par défauts

Les fonctions peuvent éventuellement définir des valeurs par
défaut pour leur paramètres. Cela évite d'avoir à écrire des
surcharges de fonctions redondantes.

```d
void plot(string msg, string couleur = "rouge")
{
    //...
}

plot("D est génial !");
plot("D est génial !", "bleu");
```

Une fois qu'un paramètre par défaut a été spécifié, tous les 
paramètres qui le suivent doivent être des paramètres par 
défaut également.

### Fonctions locales

Une fonction peut être déclarée à l'intérieur d'une autre fonction.
Elle peut alors être utilisée dans la portée où elle est définie
mais n'est pas visible pour le reste du monde.
Ces fonctions peuvent même avoir accès à des objets qui sont dans
la portée parente de la portée locale:

```d
void fun()
{
    int local = 10;
    int fun_secret()
    {
        local++;
    }
}
//...
```

Ces fonctions imbriquées sont appelées des *delegates* mais nous y 
reviendrons plus en détail [plus tard](basics/delegates).

### Pour aller plus loin

- [Fonctions dans _Programming in D_](http://ddili.org/ders/d.en/functions.html)
- [Paramètres de fonctions dans _Programming in D_](http://ddili.org/ders/d.en/function_parameters.html)
- [Spécification des fonctions](https://dlang.org/spec/function.html)

## {SourceCode}

```d
import std.stdio : writeln;
import std.random : uniform;

void randomCalculator()
{
    // On définit 4 fonctions loales
    // pour 4 opérations mathématiques
    auto additioner(int gauche, int droite) {
        return gauche + droite;
    }
    auto soustraire(int gauche, int droite) {
        return gauche - droite;
    }
    auto multiplier(int gauche, int droite) {
        return gauche * droite;
    }
    auto diviser(int gauche, int droite) {
        return gauche / droite;
    }

    int a = 10;
    int b = 5;

    // uniform(START, END) génère un nombre 
    // aléatoire entre START et END (non inclus)
    // En fonction du résultat, on réalise une
    // des opérations mathématiques
    switch (uniform(0, 4)) {
        case 0:
            writeln(additioner(a, b));
            break;
        case 1:
            writeln(soustraire(a, b));
            break;
        case 2:
            writeln(multiplier(a, b));
            break;
        case 3:
            writeln(diviser(a, b));
            break;
        default:
            // Instruction spéciale qui marque 
            // une ligne qu'on ne doit jamais 
            // atteindre
            assert(0);
    }
}

void main()
{
    randomCalculator();
    // additioner(), soustraire(), multiplier()
    // and diviser() ne sont pas accessibles en
    // dehors de leur portée
    static assert(!__traits(compiles,
                            additioner(1, 2)));
}

```
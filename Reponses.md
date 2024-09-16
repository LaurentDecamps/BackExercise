# Partie 1 : POO

## Interfaces

L'interface est correcte si B, C et D sont également des interfaces.

## Héritage et interface

Parmi les deux propositions, je préfère la première qui correspond plus à une modélisation réelle.

La capacité de voler est dans une interface dont hérite la classe Avion et la classe Oiseau qui vole tous les deux. Chaque classe héritant d'Oiseau ou d'Avion devrait implémenter la méthode vole() comme ci-dessous :

```csharp
public class Condor : Oiseau
{
    public override void vole()
    {
        // Code du vol du condor
    }
}
```

Je connais la notion abordée ici, c'est un singleton et plus particlièrement "lazy singleton" avec le pattern "double check locking".  Le framework a proposé depuis le type Lazy<T>.

## Heritage porté

## Injection dep

L'utilisation d'injection de dépendance :
* "Réduit le couplage entre un objet et ses dépendances" :
Vrai. L'injection de dépendances permet en effet de réduire le couplage fort entre les objets en favorisant une dépendance vers des abstractions plutôt que des implémentations concrètes.
* "Favorise l'utilisation d'interfaces" :
Vrai. L'injection de dépendances encourage l'utilisation d'interfaces ou de classes abstraites pour définir les contrats entre les objets, ce qui améliore la flexibilité et facilite les tests.
* "C'est confier à un objet tiers la création des objets dont dépend un autre objet" :
Vrai. C'est une bonne définition de l'injection de dépendances. Un conteneur d'injection de dépendances (ou "injecteur") est généralement responsable de la création et de l'injection des dépendances.
* "Centralise dans le constructeur des classes la création des instances dont elles dépendent" :
Faux. Cette affirmation va à l'encontre du principe de l'injection de dépendances. L'injection de dépendances vise justement à éviter que les classes créent elles-mêmes leurs dépendances.

# Partie 2 : Sql Server

## Requête 1

Les immatriculations des voitures ayant fait une révision d'au moins 300€ avec le changement d’au moins 2
pneus.

```sql
SELECT DISTINCT v.immatriculation
FROM vehicule v
JOIN revision r ON v.id = r.vehicule_id
JOIN revision_piece rp ON r.id = rp.revision_id
JOIN piece p ON rp.piece_id = p.id
WHERE r.prix >= 300
AND p.nom = 'pneu'
AND rp.nb_piece >= 2
```

## Requête 2

Lister voitures ayant fait au moins 2 révisions sur 1 année.
Résultat attendu: immatriculation, année

```sql
SELECT v.immatriculation, EXTRACT(YEAR FROM r.date) AS annee
FROM vehicule v
JOIN revision r ON v.id = r.vehicule_id
GROUP BY v.immatriculation, EXTRACT(YEAR FROM r.date)
HAVING COUNT(*) >= 2
ORDER BY annee, v.immatriculation
```

## Requête 3

Lister les immatriculations des voitures n'ayant pas fait de revision en 2008;

```sql
SELECT v.immatriculation
FROM vehicule v
WHERE v.id NOT IN (
    SELECT DISTINCT r.vehicule_id
    FROM revision r
    WHERE EXTRACT(YEAR FROM r.date) = 2008
)
ORDER BY v.immatriculation
```

# Left join

Analysons les deux requêtes SQL pour comprendre leur différence :

```sql
SELECT … FROM A LEFT JOIN B ON A.a=B.b AND B.c=2;
SELECT … FROM A LEFT JOIN B ON A.a=B.b WHERE B.c=2;
```

La différence clé réside dans la position de la condition "B.c=2". Dans la première requête, elle fait partie de la clause ON du LEFT JOIN, tandis que dans la seconde, elle est dans la clause WHERE.

La première retourne potentiellement plus de lignes que la seconde.

# Inner join

Analysons les deux requêtes SQL pour comprendre leur différence :

```sql
SELECT … FROM A INNER JOIN B ON A.a=B.b AND B.c=1;
SELECT … FROM A INNER JOIN B ON A.a=B.b WHERE B.c=1;
```

L'INNER JOIN ne retourne que les lignes qui correspondent dans les deux tables.
Que la condition B.c=1 soit dans la clause ON ou dans un WHERE après la jointure, elle filtre les mêmes lignes.

Il n'y a donc aucune différence de résultat

# Trie

Pour trier par nom dans l'ordre croissant (alphabétiquement je suppose) on utilise la commande ORDER BY Nom ASC

# Transaction

En SQL Server, la survenue d'une erreur SQL au sein d'une transaction n'aboutit pas forcément à l'annulation complète de cette transaction. Le comportement dépend de plusieurs facteurs :
1. Niveau de gravité de l'erreur :
Certaines erreurs de faible gravité ne provoquent pas automatiquement l'annulation de la transaction.
2. Configuration de XACT_ABORT :
Si XACT_ABORT est défini sur OFF (ce qui est le paramètre par défaut), certaines erreurs ne provoqueront pas l'annulation automatique de la transaction.
3. Gestion explicite des erreurs :
Le développeur peut utiliser des blocs TRY...CATCH pour gérer les erreurs et décider explicitement s'il faut annuler (ROLLBACK) ou valider (COMMIT) la transaction.
4. Type de transaction :
Les transactions implicites et explicites peuvent avoir des comportements légèrement différents en cas d'erreur.
5. Paramètres de connexion :
Certains paramètres de connexion peuvent influencer le comportement en cas d'erreur.

Dans de nombreux cas, une erreur laissera la transaction dans un état "non validé", mais n'effectuera pas automatiquement un ROLLBACK. C'est alors au développeur de décider comment gérer cette situation, soit en effectuant un ROLLBACK explicite, soit en corrigeant l'erreur et en poursuivant la transaction.

# PARTIE 3 : C#

## Gestion d’erreurs

Pour traiter un comportement inattendu dans une bibliothèque C#, la meilleure option parmi celles proposées est : 

```csharp
throw new ComportementInattenduException()
```

Voici pourquoi cette approche est préférable :

Lancer une exception :

* C'est la méthode standard en C# pour signaler des conditions d'erreur ou des comportements inattendus.
* Permet au code appelant de gérer l'erreur de manière appropriée.
* Fournit des informations détaillées sur l'erreur (stack trace, message, etc.).
* Respecte les principes de la programmation orientée objet et les conventions C#.

## Comparaison object

```csharp
object o1 = new object();
object o2 = new object();
console.writeline($'{o1==o2},{o1.Equals(o2)});
```

Cela affiche "false,false" car l'opérateur == et la méthode Equals comparent les références qui sont différentes.    

## Heritage

C'est le mot clé "base" qui permet de référencer la classe parent.

## Polymorphisme

C'est le mot clef "new" qui permet de redéfinir une méthode hérité.

## Internal

# Code

## Interfaces

```csharp
public interface AdapterInterface
{
    string IsPositive();
}

public class ApiAdapter : AdapterInterface
{
    private readonly decimal balance;

    public ApiAdapter(decimal initialBalance)
    {
        this.balance = initialBalance;
    }

    public string IsPositive()
    {
        return this.balance > 0 ? "true" : "false";
    }
}
```

## Count digit

```csharp
using System;
using System.Linq;

public class StringHelper
{
    public int CountDigits(string input)
    {
        if (input == null)
            return 0;
        return input.Count(char.IsDigit);
    }
}

public class StringTest
{
    static public void Main()
    {
        StringHelper stringHelper = new StringHelper();
        Console.WriteLine(stringHelper.CountDigits("34RDfdv5"));
    }
}
```

## Max divisor commun

```csharp
public class MathHelper
{
    public int MaxDivisor(int firstNumber, int secondNumber)
    {
        firstNumber = Math.Abs(firstNumber);
        secondNumber = Math.Abs(secondNumber);
        while (secondNumber != 0)
        {
            int remainder = firstNumber % secondNumber;
            firstNumber = secondNumber;
            secondNumber = remainder;
        }
        return firstNumber;
    }
}
```
### Exercice 1

Modifiez la collection salle afin que soient dorénavant validés les documents destinés à y être insérés ; cette validation aura lieu en mode « strict » et portera sur les champs suivants :

`nom` sera obligatoire et devra être de type chaîne de caractères.

`capacite` sera obligatoire et devra être de type entier (int).

Dans le champ `adresse`, les champs `codePostal` et `ville`, tous deux de type chaîne de caractères, seront obligatoires.

Que constatez-vous lors de la tentative d’insertion suivante, et quelle en est la cause ?

```
db.salles.insertOne( 
    {"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville"}} 
)
```

Que proposez-vous pour régulariser la situation ?

> ```
> db.salles.insertOne( 
>    {"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville", "codePostal": "75000"}} 
> )
> ```
>
> Lors de la tentative d'insertion, une erreur s'affiche indiquant que le champ codePostal est requis dans le document. Cette erreur est causée par la validation que nous avons ajoutée pour ce champ.
> 
> Pour régulariser la situation, il faut ajouter un champ codePostal.

### Exercice 2

Rajoutez à vos critères de validation existants un critère supplémentaire : le champ `_id` devra dorénavant être de type entier (int) ou ObjectId.

> ```
> db.runCommand({ collMod: "salles", validator: {$jsonSchema: {properties: {_id: {bsonType: ["int", "objectId"]} }}}}) 
> ```

Que se passe-t-il si vous tentez de mettre à jour l’ensemble des documents existants dans la collection à l’aide de la requête suivante :

```
db.salles.updateMany({}, {$set: {"verifie": true}}) 
```

Supprimez les critères rajoutés à l’aide de la méthode delete en JavaScript

> Une erreur est renvoyée, car la mise à jour ajoute un champ "verifie" qui n'est pas présent dans le schéma de validation.
> 
> Pour supprimer les critères de validation ajoutés, il faut exécuter la commande suivante :
> 
> ```
> db.runCommand({ collMod: "salles", validationLevel: "off" }) 
> ```

### Exercice 3

Rajoutez aux critères de validation existants le critère suivant :

Le champ `smac` doit être présent OU les `styles` musicaux doivent figurer parmi les suivants : "jazz", "soul", "funk" et "blues".

> ```
> db.runCommand({
>    collMod: "salles",
>    validator: {
>       $or: [
>          {smac: {$exists: true}},
>          {styles: {$in: ["jazz", "soul", "funk", "blues"]}} 
>       ]
>    },
>    validationLevel: "strict"
> })
> ```


Que se passe-t-il lorsque nous exécutons la mise à jour suivante ?

```
db.salles.update({"_id": 3}, {$set: {"verifie": false}}) 
```

> Si les critères de validation ont bien été ajoutés et que le document avec l'identifiant 3 ne contient ni le champ `smac` ni une valeur pour `styles` parmi "jazz", "soul", "funk" et "blues", la mise à jour avec la commande échouera et retournera une erreur de validation de schéma.
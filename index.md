### Exercice 1

Un bref examen de vos fichiers journaux a révélé que la plupart des requêtes effectuées sur la collection salles cible des capacités ainsi que des départements, comme ceci :

```
db.salles.find({"capacite": {$gt: 500}, "adresse.codePostal": /^30/}) 
```
```
db.salles.find({"adresse.codePostal": /^30/, "capacite": {$lte: 400}}) 
```

Que proposez-vous comme index qui puisse couvrir ces requêtes ?

Détruisez ensuite l’index créé.

> `db.salles.createIndex({"adresse.codePostal": 1, "capacite": -1})`

> `db.salles.dropIndex("adresse.codePostal_1_capacite_-1")`

.

> La requête `db.salles.find({"capacite": {$gt: 500}, "adresse.codePostal": /^30/})` effectue une recherche dans la collection "salles" et retourne tous les documents qui répondent aux conditions suivantes:
> 
> - La valeur de "capacité" est supérieure à 500
> - La valeur du champ "codePostal" dans le sous-champ "adresse" commence par "30"
> 
> La requête `db.salles.find({"adresse.codePostal": /^30/, "capacité": {$lte: 400}})` effectue une recherche dans la collection "salles", et retourne tous les documents qui répondent aux conditions suivantes:
> 
> - La valeur du champ "codePostal" dans le sous-champ "adresse" commence par "30"
> - La valeur de "capacité" est inférieure ou égale à 400
> 
> Index :
> 
> - La valeur 1 pour adresse.codePostal signifie que l'index sera créé en ordre croissant sur ce champ
> - La valeur -1 pour capacité signifie que l'index sera créé en ordre décroissant sur ce champ
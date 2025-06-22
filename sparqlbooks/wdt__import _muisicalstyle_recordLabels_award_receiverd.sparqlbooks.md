# In this notebook we import the musical style, record labels, award received 



## musical style
```sparql
# Cette requête récupère les genres musicaux (P136) pour un échantillon de personnes (wd:Q5)
# Elle est utilisée pour tester l’extraction avant l’insertion dans AllegroGraph

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

CONSTRUCT {
  ?item wdt:P136 ?genre .
  ?genre rdfs:label ?genreLabel .
}
WHERE {
  # Sélection d'un sous-ensemble de personnes (par exemple 100 premières)
  {
    SELECT ?item
    WHERE {
      ?item a wd:Q5 .
    }
    ORDER BY ?item
    OFFSET 0      # Changer pour 10000, 20000… pour traiter en lots
    LIMIT 100
  }
  SERVICE <https://query.wikidata.org/sparql> {
    ?item wdt:P136 ?genre .
    SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  }
}
```
```sparql
### Insère les triplets genre musical (P136) dans ton graphe AllegroGraph

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

WITH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md>
INSERT {
  ?item wdt:P136 ?genre.
  ?genre rdfs:label ?genreLabel.
}
WHERE {
  # Sélectionne les personnes du graphe
  {SELECT ?item
   WHERE {
     ?item a wd:Q5.
   }
   ORDER BY ?item
   OFFSET 0     # Modifier si tu as besoin de paginer (ex. OFFSET 10000)
   LIMIT 10000  # Wikidata impose une limite
  }

  # Appel à Wikidata pour les genres
  SERVICE <https://query.wikidata.org/sparql> {
    ?item wdt:P136 ?genre.
    SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  }
}

```
### Ajoute un label à la propriété wdt:P136 (genre musical) 
```sparql
### Ajoute un label à la propriété wdt:P136 (genre musical)

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

INSERT DATA {
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    wdt:P136 rdfs:label "genre musical".
  }
}

```
### ajout de genreLabel manquants 
```sparql
### Insère les rdfs:label manquants pour les genres musicaux (P136)

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

WITH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md>
INSERT {
  ?genre rdfs:label ?genreLabel.
}
WHERE {
  # Trouver les genres musicaux présents dans le graphe
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?person wdt:P136 ?genre.
  }

  # Récupérer les labels depuis Wikidata
  SERVICE <https://query.wikidata.org/sparql> {
    ?genre rdfs:label ?genreLabel.
    FILTER (lang(?genreLabel) = "en")  # ou "fr" pour français
  }
}

```
```sparql
### Vérifie la présence des genres musicaux dans le graphe

PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?genre ?genreLabel (COUNT(*) AS ?n)
WHERE {
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?person wdt:P136 ?genre.
    OPTIONAL { ?genre rdfs:label ?genreLabel }
  }
}
GROUP BY ?genre ?genreLabel
ORDER BY DESC(?n)
LIMIT 50
```
## record labels  
```sparql
### Récupère les labels musicaux (P264) pour chaque personne (Q5) + leurs labels

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

CONSTRUCT {
  ?item wdt:P264 ?labelEntity.
  ?labelEntity rdfs:label ?labelName.
}
WHERE {
  # Personnes déjà présentes dans le graphe local
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?item a wd:Q5.
  }

  # Récupération depuis Wikidata
  SERVICE <https://query.wikidata.org/sparql> {
    ?item wdt:P264 ?labelEntity.
    SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  }
}

```
```sparql
### Insère les labels musicaux (P264) dans le graphe AllegroGraph

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

WITH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md>
INSERT {
  ?item wdt:P264 ?labelEntity.
  ?labelEntity rdfs:label ?labelName.
}
WHERE {
  # Sélectionner les personnes locales
  {SELECT ?item
   WHERE {
     ?item a wd:Q5.
   }
   ORDER BY ?item
   OFFSET 0     # Adapte l’OFFSET si besoin
   LIMIT 10000
  }

  # Service Wikidata
  SERVICE <https://query.wikidata.org/sparql> {
    ?item wdt:P264 ?labelEntity.
    SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  }
}

```
### Ajoute le label explicite à la propriété P264
```sparql
### Ajoute le label explicite à la propriété P264 (label musical)

PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

INSERT DATA {
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    wdt:P264 rdfs:label "label musical".
  }
}
```
### ajout des LabelName manquants 
```sparql
### Récupère et insère les labels textuels (rdfs:label) des entités wdt:P264

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

WITH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md>
INSERT {
  ?labelEntity rdfs:label ?labelName.
}
WHERE {
  # Récupérer les entités de label musical
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?person wdt:P264 ?labelEntity.
  }

  # Aller chercher le label sur Wikidata
  SERVICE <https://query.wikidata.org/sparql> {
    ?labelEntity rdfs:label ?labelName.
    FILTER (lang(?labelName) = "en")  # ou "fr" si tu préfères en français
  }
}
 
```
```sparql
### Vérifie si les labels musicaux ont bien été importés
### Affiche les labels avec le nombre d’artistes associés

PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?labelEntity ?labelName (COUNT(*) AS ?n)
WHERE {
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?person wdt:P264 ?labelEntity.
    OPTIONAL { ?labelEntity rdfs:label ?labelName }
  }
}
GROUP BY ?labelEntity ?labelName
ORDER BY DESC(?n)
LIMIT 20
```
## award received  
```sparql
### Récupère les récompenses (P166) pour chaque artiste et leurs labels

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

CONSTRUCT {
  ?person wdt:P166 ?award.
  ?award rdfs:label ?awardLabel.
}
WHERE {
  # Artistes déjà présents dans ton graphe local
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?person a wd:Q5.
  }

  # Appel à Wikidata pour récupérer les récompenses
  SERVICE <https://query.wikidata.org/sparql> {
    ?person wdt:P166 ?award.
    SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  }
}

```
```sparql
### Insère les triplets de récompenses (P166) + labels dans le graphe

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

WITH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md>
INSERT {
  ?person wdt:P166 ?award.
  ?award rdfs:label ?awardLabel.
}
WHERE {
  {SELECT ?person
   WHERE {
     ?person a wd:Q5.
   }
   ORDER BY ?person
   OFFSET 0     # Paginer si >10 000
   LIMIT 10000
  }

  SERVICE <https://query.wikidata.org/sparql> {
    ?person wdt:P166 ?award.
    SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
  }
}

``` 

### Ajoute un label explicite à la propriété P166
```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

INSERT DATA {
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    wdt:P166 rdfs:label "award received".
  }
}
```
### ajout des awardlabel manquants
```sparql
### Insère les rdfs:label manquants pour les récompenses musicales (P166)

PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

WITH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md>
INSERT {
  ?award rdfs:label ?awardLabel.
}
WHERE {
  # Trouver les entités de récompense dans le graphe
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?person wdt:P166 ?award.
  }

  # Récupérer les labels depuis Wikidata
  SERVICE <https://query.wikidata.org/sparql> {
    ?award rdfs:label ?awardLabel.
    FILTER (lang(?awardLabel) = "en")  # ou "fr" si tu préfères
  }
}
```
```sparql
### Vérifie si les récompenses ont bien été importées
### Affiche les récompenses les plus fréquentes

PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?award ?awardLabel (COUNT(*) AS ?n)
WHERE {
  GRAPH <https://github.com/Ziedellouzi/rappers/blob/main/graphs/wikidata-imported-data.md> {
    ?person wdt:P166 ?award.
    OPTIONAL { ?award rdfs:label ?awardLabel }
  }
}
GROUP BY ?award ?awardLabel
ORDER BY DESC(?n)
LIMIT 20
```

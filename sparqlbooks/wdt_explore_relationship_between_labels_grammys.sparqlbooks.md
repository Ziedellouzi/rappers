# In this notebook we import the musical style, labels, award received 



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

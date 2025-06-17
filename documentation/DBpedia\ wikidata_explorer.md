*  grammy award (album of the year) (dbpedia) 
  
  ```sparql
  
  PREFIX dbr: <http://dbpedia.org/resource/>
  PREFIX dbo: <http://dbpedia.org/ontology/>

  SELECT ?album ?albumLabel
  WHERE {
  ?album dbo:wikiPageWikiLink dbr:Grammy_Award_for_Album_of_the_Year .
  ?album rdfs:label ?albumLabel .
  FILTER (lang(?albumLabel) = "en")
  }
  LIMIT 200

  ```
*  nombre des rappeurs ayant un grammy award (album of the year) (wikidata) (total 119) 
  
  ```sparql
  SELECT (COUNT(*) AS ?effectif)
WHERE {
  ?rapper wdt:P31 wd:Q5;                # Instance de humain
          wdt:P106 wd:Q2252262;         # Profession: rappeur
          wdt:P7050 ?aotyID.            # Album of the Year artist ID
}

  ```

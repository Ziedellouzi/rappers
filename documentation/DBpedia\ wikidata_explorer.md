*  grammy award (album of the year) (dbpedia  
  
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

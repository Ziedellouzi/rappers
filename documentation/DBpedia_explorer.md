* le nombre des rappeurs ayants un grammy award (album od the year) ( en total 109)
  
  ```sparql
  PREFIX dbr: <http://dbpedia.org/resource/>
  PREFIX dbo: <http://dbpedia.org/ontology/>
  PREFIX dbp: <http://dbpedia.org/property/>
  
  SELECT (COUNT(*) as ?effectif)
  WHERE { 
      ?laureate dbo:award dbr:Album of the Year artist ID 
  }

  ```

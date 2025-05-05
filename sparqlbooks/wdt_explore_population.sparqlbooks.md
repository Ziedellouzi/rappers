# wikidata exploration
````sparql
SELECT (COUNT(*) as ?eff)
WHERE {
    ?item wdt:P31 wd:Q5;  # Any instance of a human.
          wdt:P106 wd:Q2252262;  # Rapper 
}
````
extraire la liste des rappeurs et leurs date de naissance 
````sparql
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wikibase: <http://wikiba.se/ontology#>

SELECT ?rapper ?rapperLabel ?birthDate
WHERE {
  ?rapper wdt:P106 wd:Q2252262;  # Occupation: Rapper
          wdt:P569 ?birthDate.   # Date of birth
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
ORDER BY ?birthDate
````
compter les rappeurs né apres 1751
````sparql
### Modern rappers : born from 1751 onward
SELECT (count(*) as ?number)
WHERE {
    ?item wdt:P106 wd:Q2252262.    # rappers
    
    ?item wdt:P31 wd:Q5; # Any instance of a human.
            wdt:P569 ?birthDate.

    BIND(REPLACE(str(?birthDate), "(.)([0-9]{4})(.)", "$2") AS ?year)
    FILTER(xsd:integer(?year) > 1750 )
}
````
compter combien de proprietés sont valable pour la population des rappeurs
````sparql


PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wikibase: <http://wikiba.se/ontology#>
PREFIX bd: <http://www.bigdata.com/rdf#>

SELECT ?p ?propLabel ?eff
WHERE {
{
SELECT ?p  (count(*) as ?eff)
WHERE {
    ?item wdt:P106 wd:Q2252262. # rappers
    ?item wdt:P31 wd:Q5; # Any instance of a human.
            wdt:P569 ?birthDate.
    ?item  ?p ?o.

    BIND(REPLACE(str(?birthDate), "(.*)([0-9]{4})(.*)", "$2") AS ?year)
    ### Experiment with different time filters if too many values

}
GROUP BY ?p 

    }

# get the original property (in the the statement construct)     
?prop wikibase:directClaim ?p .

SERVICE wikibase:label { bd:serviceParam wikibase:language "en". } 


}  
ORDER BY DESC(?eff)
# LIMIT 20
````
compter combien de proprietés sont valable pour la population des rappeurs (mieux) 
````sparql
SELECT ?p ?propLabel (COUNT(*) as ?eff)
WHERE {
  {  ?item wdt:P31 wd:Q5;  # Any instance of a human.
          wdt:P106 wd:Q2252262. }
     ?item ?p ?o .
  # get the original property (in the the statement construct)     
?prop wikibase:directClaim ?p .

SERVICE wikibase:label { bd:serviceParam wikibase:language "en". } 
         # Rapper 
} 
Group BY ?p ?propLabel
ORDER BY DESC(?eff) 
# LIMIT 20
````

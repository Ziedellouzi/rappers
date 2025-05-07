# Import citizenships

In this notebook we add to the imported Wikidata population properties regarding citizenship in countries

```sparql
### Number of persons in our population
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT (COUNT(*) as ?n)
WHERE {
    GRAPH <https://github.com/Ziedellouzi/rappers/main/graphs/wikidata-imported-data.md>
        {?s a wd:Q5.}
}

```

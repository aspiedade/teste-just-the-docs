
[Documentação](../../documentacao.md) > [How-to](../how-to.md)

# Places Insights

## Referências

- Demo: <https://mapsplatform.google.com/demos/places-insights/>
- Write Query: <https://developers.google.com/maps/documentation/placesinsights/queries>
- Visualize Results: <https://developers.google.com/maps/documentation/placesinsights/visualize-data>
- Schema:
  - Country: <https://developers.google.com/maps/documentation/placesinsights/reference/country-schema>
  - Core: <https://developers.google.com/maps/documentation/placesinsights/reference/core-schema>

## **Amostra**

uolcs-datalake-prd.places\_insights\_\_\_br\_\_\_sample.places\_sample

### Exemplo de query

```sql
SELECT WITH AGGREGATION_THRESHOLD
COUNT(*) AS count
FROM `uolcs-datalake-prd.places_insights___br___sample.places_sample`
WHERE
-- ST_DWITHIN(ST_GEOGPOINT(-73.9857, 40.7484), point, 1000)
'restaurant' IN UNNEST(types)
AND business_status = "OPERATIONAL"
AND rating >= 4.0
AND allows_dogs = true
```

uolcs-datalake-prd.places\_insights\_\_\_br\_\_\_sample.places\_sample

uolcs-datalake-prd.p

laces\_insights\_\_\_br\_\_\_sample.places\_sample

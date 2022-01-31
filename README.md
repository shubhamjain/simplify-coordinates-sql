# Simplify Coordinates
[Simplify.js](https://mourner.github.io/simplify-js/) is a small library to reduce the number of coordinates required to represent the same shape. It can be used to  compress route data by removing redundant coordinates. The library has been ported to various languages and this repo does it for SQL (specifically Postgres, using PL/Python). It will be useful when you want to compress the coordinates in the database itself. 

## Requirements
This extension depends upon PL/Python extension, which allows Postgres functions to be written in Python. The reason being that loops, if..then statements, recursion are far more easier in Procedural langauges than SQL. 

The extension can be installed by downloading a specific package ([read here](https://stackoverflow.com/questions/26091390/postgresql-how-to-install-plpythonu-extension)).

To enable the extension, run:

`CREATE EXTENSION plpythonu;`

## Usage

The function accepts text and returns text. Postgres doesn't have a good support for array of arrays, so text proved to a better medium. Suppose laglng is the column that contains coordinates in the text format. If they are in something else, you can use `::text` to transform it. 

```sql
SELECT
  latlng as orig_coords,
  simplifypoints(latlng, .00005) as new_coords,
  (
    (jsonb_array_length(latlng::jsonb) - jsonb_array_length(simplifypoints(latlng, .00005)::jsonb)) * 100 /
    jsonb_array_length(latlng::jsonb)
  ) as reduction_perc
FROM routes;
```

**Output**

![image](https://user-images.githubusercontent.com/309760/151829852-4a626388-0bec-40b2-8789-1f4f143a1b23.png)

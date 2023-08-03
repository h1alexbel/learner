# Searching

## URI Searches

Simplified search,
written in Apache Lucene syntax:
```text
GET /products/_search?q=name:test AND tags:wine
```

## Query DSL

**Query DSL is a preferred way to write a search queries**:

```text
GET /products/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "test"  
          }
        },
        {
          "match": {
            "tags": "wine"
        }
      ]
    }
  }
}
```

## Term-level queries

Term-level queries are used to search structured data for exact values,
a.k.a filtering.

**Term-level queries are not analyzed**,
the search value is used exactly as is for inverted index lookups.


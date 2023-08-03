# Mapping & Analysis

## Text Analysis

Applicable to text fields/values.
Text values are analyzed when indexing documents.
**The `_source` object is not used when searching for documents**.

**Text is processed before stored**.

Analyzer component consists of:
1. Character filter
2. Tokenizer
3. Token filters

### Character filters

Adds, removes, or changes characters.
Analyzers contain zero or more character filters.

Example of `html_strip` filter:
input:
```html
"I&apos;m in a <em>good</em> mood."
```
output:
```text
"I'm in a good mood."
```

**By default, Elasticsearch uses no Character filters**.

#### Tokenizers

An analyzer can have only one tokenizer.
Tokenizer splits a string into a tokens.
During this process, some characters can be removed.

Example:
standard:
```text
"I REALLY like RUP!" -> ["I", "REALLY", "like", "RUP"]
```

**By default, Elasticsearch uses `standard` tokenizer**.

#### Token filters

Receive the output of the tokenizer as input

A token filter can add, remove, or modify tokens.
An analyzer can have zero or more token filters. 

Example:
Lowercase filter:
```text
["I", "REALLY", "like", "RUP"] -> ["i", "really", "like", "rup"]
```

**By default, Elasticsearch uses `lowercase` token filters**.

The default configurations for text analysis are built `Standard Analyzer`.
**Which is used to perform matches for all the text fields**.

Elasticsearch is a full rich of built-in analyzers, character filters,
tokenizers, and token filters.
**But we can add custom ones**.

## Analyze API

For testing your own analyzers and exploring existing ones,
you can use Analyze API:
```text
POST /_analyze
{
  "text": "2 guys walk into    a bar, but th third... DUCKS! :-)",
  "analyzer": "standard"
}
```

also, you can build your own analyzer combining `filter`, `tokenizer`, and `char_filter`:
```text
POST /_analyze
{
  "text": "",
  "char_filter": [],
  "tokenizer": "standard",
  "filter": ["lowercase"]
}
```

## Inverted Indices

Efficient data access is handled by Apache Lucene, not by Elasticsearch.

So, different access patterns, handled differently.
<br>
**Inverted index** is a mapping between terms and which documents contain them.

Example:
```text
"2 guys walk into    a bar, but th third... DUCKS! :-)"
```
Will be transformed into:
```text
["2", "guys", "walk", "into", "a", "bar", "but", "the", "third", "ducks"]
```

After that, inverted index will be built:
```text
term     | document #1|
---------|------------|
2        | x          |
a        | x          |
bar      | x          |
but      | x          |
ducks    | x          |
guys     | x          |
into     | x          |
the      | x          |
third    | x          |
walk     | x          |
```

Let's insert some more documents:

```text
"2 guys went into a bar" -> ["2", "went", "into", "a", "bar"]
```

```text
"2 ducks walk around the lake" -> ["2", "ducks", "walk", "around", "the", "lake"]
```

Now, index will be looks like:

```text
term          | document #1| document #2 | document #3|
--------------|------------|-------------|------------|
2             | x          | x           | x          |
a             | x          | x           |            | 
around        |            |             | x          | 
bar           | x          | x           |            |
but           | x          |             |            |
ducks         | x          |             | x          |
guys          | x          | x           |            |
into          | x          | x           |            |
lake          |            |             | x          |
the           | x          |             | x          |
third         | x          |             |            |
walk          | x          |             | x          |
went          |            | x           |            |
```

**Inverted index is created for each `text` field**.
Other value types, such as numeric, dates, and geospatial data are stored in BKD trees.

## Mapping

Mapping defines the structure of documents and how they are indexed and stored.

Mapping can be either explicit or dynamic:
in explicit we define field mapping, while in
dynamic Elasticsearch generates field mapping for us

## Data types

object,
boolean,
long,
integer,
float,
short,
date

### Object

Used for any JSON object.
Objects may be nested.
Mapped using the `properties` parameter.
**Objects are not stored as objects in Apache Lucene**.
**Objects are flattened**: each layer is denoted with a `.`.

```json
{
  "name": "Maker",
  "price": 64,
  "stock": 10,
  "manufacturer": {
    "name": "Nespresso",
    "country": "Switzerland"
  }
}
```

This JSON document will be transformed into:

```json
{
  "name": "Maker",
  "price": 64,
  "stock": 10,
  "manufacturer.name": "Nespreso",
  "manufacturer.country": "Switzerland"
}
```

with nested objects, it's better to use [`nested`](#nested) data type,
since `object` type doesn't know how to handle relations between flattened fields.

### Nested

Similar to the object data type, with support of object relationships.
**Inside the Apache Lucene, nested objects are stored as hidden documents**.
Those documents won't show in a search result unless we query them directly.

### Keyword

Used for exact matching of values.
Typically used for filtering, aggregations, and sorting.

Example: searching for articles with a status of `PUBLISHED`.
**For full-text searches, use the `text` data type instead**.

For `keyword` data type, a perspective analyzer is used:
```text
"analyzer": "keyword"
```

Keyword analyzer is a `no-op` analyzer.
**It doesn't do anything, since it tended for exact matching**.

### Arrays

After [analyzing](#analyze-api), array elements will be merged together:

```text
["first message", "and the second are merged"] -> ["first", "message", "and", "the", "second", "are", "merged"]
```
Elements must be of the same type.

## Type coercion

Let's say we insert the data into document #1:
```json
{
  "price": 7.4
}
```

After it, we insert another document #2:

```json
{
  "price": "7.4"
}
```

Elasticsearch inspects values and its datatype with actual mapping.
If it's not matched,
Elasticsearch tries to coerce the type into an expected data type.
If coercion is not possible, `parsing exception` will be raised.
Coercion is not used for dynamic mapping: supplying `"7.4"` for a new field
will create a `text` mapping.
<br>
**Warning**: search queries use indexed values, not the `_source` one,
however, the result will contain the values that were supplied
at index time `"7.4"`, not the values are indexed: `7.4`.
Supplying a floating point for an integer field will truncate it to an integer.
<br>
Coercion can be disabled.

## Managing Mappings

TBD..

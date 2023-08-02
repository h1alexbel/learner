# Elasticsearch

Elasticsearch operates with **indexes** and **documents** inside.

**Documents are immutable**:
we are actually replacing documents.

## Ops

### Create

Create a document inside the documents:
```text
POST /products/_doc
{
  "name": "Coffee Maker",
  "price": 64,
  "stock": 10
}
```

All documents have `_id`, if we are not specifying it,
Elasticsearch will generate it for us.

To create a document with id:
```text
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 49,
  "stock": 4
}
```

### Get

To get a document:
```text
GET /products/_doc/100
```

document's data is located in `_source` key.
If a document is not found, `found` key will be false.

Example:

```json
{
  "_index": "products",
  "_type": "_doc",
  "_id": "100",
  "_version": 1,
  "_seq_no": 1,
  "_primary_term": 1,
  "found": true,
  "_source": {
    // data
  }
}
```
### Update

Document can be also updated:
```text
POST /products/_update/100
{
  "doc": {
    "stock": 3
  }
}
```
If the field does not exist, it will be added to the document.

### Script

Also, on updates you can apply the script:

```text
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.stock-= params.quantity",
    "params": {
       "quantity": 4
    }
  }
}
```

### Delete

```text
DELETE /products/_doc/100
```

## Routing

Routing is the process of resolving a shard for a document.

Default routing strategy:
```text
shard = hash(id) % num_shards
```

It's possible to customize routing.

According to the default routing strategy, the following case can happen:
1. document was indexed at shard #2
2. the number of shards was changed
3. get a document by id cannot be performed 
   since Elasticsearch doesn't know where the index is stored

That's why additional shards can not be added to index.
**Modifying the number of shards requires 
creating a new index and re-indexing documents into it**.

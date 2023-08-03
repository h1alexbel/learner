# Managing Documents

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

### Internal Document Reading

Algorithm of document reading in elasticsearch is the following one:
1. Figure out [where the document is stored](#routing)
2. Adaptive replica selection based on performance
3. Read request

### Internal Writing

Request goes through the same [routing process](#internal-document-reading):
1. Request to the primary shard for validation
2. Primary shard writes data locally
3. Primary shard parallel writes to Replicas

**If primary shard fails, some replica will be promoted to the primary shard**.
Each replication group must have primary shard(leader election).
<br>
**Primary terms** is a way to distinguish between old and new primary shards.
It's just a counter for how many times the primary shard has changed.
Primary terms can tell whether primary shard was changed or not.
<br>
**Sequence numbers** appended to write operations together with the primary term.
Sequence number is just a counter that is incremented on each write operation.
**The only primary shard can increase the sequence number**.

Using sequence numbers, Elasticsearch can tell the order of write
operations.
Also, sequence numbers helps to formulate the **checkpoints**:
1. Local: for each replica
2. Global: for each replication group

### Versioning

Elasticsearch does not support the revision history for a versions.

Elasticsearch stores a `_version` metadata field with every document:
integer value, incremented by 1, when a document is modified.

* The value is retained for 60 seconds after a document is deleted,
  can be configured with `index.gc_deletes`
* The `_version` field is returned when retrieving documents.

#### Internal vs. External versioning

**The default versioning type is internal**.
There is also an external: **maintained versioning outside Elasticsearch**.

To use external versioning:
```text
PUT /products/_doc/123?version=521&version_type=external
{
  ...
}
```

### Concurrency control

for controlling concurrency, we can use:
1. `_version`
2. Primary terms & Sequence numbers

In the case of primary terms and sequence numbers, this
query will be executed if, and only if the primary term and sequence number
are matched with the provided values:

```text
POST /products/_update/100?if_primary_term=1&if_seq_no=9
{
  ...
}
```

### Updating multiple documents

```text
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.stock--"
  }
  "query": {
    "match_all": {}
  }
}
```

**Elasticsearch snapshots the document to prevent overwriting changes
after the snapshot was taken**.
Each document's primary term and sequence number is used.
That's why the query may take a while for updating many documents.
If conflicts appear, they will be returned within the `version_conflicts` key.
To skip this validation, in order to not abort the query, we can use:
```text
"conflicts": "proceed"
```

### Deleting multiple documents

```text
POST /products/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```

under the hood, Elasticsearch does the same things as in [update_by_query API](#updating-multiple-documents).

### Batching

Batching in Elasticsearch is done with Bulk API.
**Bulk API is more efficient than just sending single write requests**.
Bulk API also supports [concurrency control](#concurrency-control).
Bulk API expects data formatted using the NDJSON specification:

```text
POST /_bulk
{ "index": {} }
```
Each line **must end with a newline character**(\n or \r\n).

**A failed action will not affect other actions**.
Neither will the bulk request as a whole be aborted.

To inspect information about each action we can use `items`.

To perform batching with custom HTTP clients, you need to set the following configuration:
```text
Content-Type: application/x-ndjson
```

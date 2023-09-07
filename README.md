# client-vector-search

A client side vector search library that can embed, search, and cache. Works on the browser and server side.

It outperforms OpenAI's text-embedding-ada-002 and is way faster than Pinecone and other VectorDBs

I'm the founder of [searchbase.app](https://searchbase.app) and we needed this for our product and customers. We'll be using this library in production. You can be sure it'll be maintained and improved.

- Embed documents using transformers by default: gte-small (~30mb).
- Calculate cosine similarity between embeddings.
- Create an index and search on the client side
- Cache vectors with browser caching support.

Lots of improvements are coming!

## Roadmap

Our goal is to build a super simple, fast vector search that works with couple hundred to thousands vectors. ~1k vectors covers 99% of the use cases.

We'll initially keep things super simple and sub 100ms

### TODOs
- [x] embed with gte-small model
- [x] cosine similarity
- [x] create an index and implement search
  - [x] brute force
  - sub 10ms for to search 1k vectors
  - sub 15ms for to create an index with 1k vectors
  - takes around 20s to generate 100 embeddings with Xenova/gte-small
  - creating 1k embeddings takes too much time and space tho!
- [x] proper crud!
  - [x] add
  - [x] update
  - [x] remove
  - [x] get
  - [x] batch remove
- [x] reduce precision of embeddings, 7 decimal places is enough
  - increased search speed by 3x
  - reduced index size by 10x
- [x] caching embeddings
  - LRU cache embedding
- [x] saving the index to [IndexedDB]([IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API))
- [ ] localStorage
- [ ] list out the models we recommend
  - [ ] check their dimensions
- [ ] test the performance of embedding times, indexing and search
- [ ] use browser APIs such as localStorage for caching
- [ ] generalize getEmbedding
  - [ ] include embedding API provider options
- [ ] simple tests
  - [ ] mock the @xenova/transformers for jest, it's not happy with it
- [ ] automatically switch to a local vs API endpoint to get embeddings!
- [ ] better index creation, search with k-d-tree, ball-tree etc. (wip)
  - not a priority for now, 1k index takes sub 15ms to create, main problem is to get embeddings faster!

## Installation

```bash
npm install client-vector-search
```

## Usage Guide

### Step 1: Generate Embeddings for String
Generate embeddings for a given string using the `getEmbedding` method. 

```ts
const embedding = await getEmbedding("Apple"); // Returns embedding as number[]
```
> **Note**: `getEmbedding` is asynchronous; make sure to use `await`.

---

### Step 2: Calculate Cosine Similarity
Calculate the cosine similarity between two embeddings.

```ts
const similarity = cosineSimilarity(embedding1, embedding2, 6);
```
> **Note**: Both embeddings should be of the same length.

---

### Step 3: Create an Index
Create an index with an initial array of objects. Each object must have an 'embedding' property.

```ts
const initialObjects = [...];
const index = new EmbeddingIndex(initialObjects); 
```

---

### Step 4: Add to Index
Add an object to the index.

```ts
const objectToAdd = { id: 6, name: 'Cat', embedding: await getEmbedding('Cat') };
index.add(objectToAdd); 
```

---

### Step 5: Update Index
Update an existing object in the index.

```ts
const vectorToUpdate = { id: 6, name: 'Dog', embedding: await getEmbedding('Dog') };
index.update({ id: 6 }, vectorToUpdate);
```

---

### Step 6: Remove from Index
Remove an object from the index.

```ts
index.remove({ id: 6 });
```

---

### Step 7: Retrieve from Index
Retrieve an object from the index.

```ts
const vector = index.get({ id: 1 });
```

---

### Step 8: Search the Index
Search the index with a query embedding.

```ts
const queryEmbedding = await getEmbedding('Fruit');
const results = index.search(queryEmbedding, { topK: 5 });
```

---

### Step 9: Print the Index
Print the entire index to the console.

```ts
index.printIndex();
```

---

### Step 10: Save to Database
Save the index to a persistent IndexedDB database.

```ts
await index.saveIndexToDB("dbName", "ObjectStoreName");
```

---

### Important: Search in Database
Perform a search operation in the IndexedDB.

```ts
const results = await index.search(queryEmbedding, { topK: 5 }, true, 'dbName', 'ObjectStoreName');
```

---

### Delete Database
To delete an entire database.

```ts
await IndexedDbManager.deleteDB("dbName");
```

---

### Delete Object Store
To delete an object store from a database.

```ts
await IndexedDbManager.deleteObjectStore("dbName", "ObjectStoreName");
```

---

### Retrieve All Objects
To retrieve all objects from a specific object store.

```ts
const allObjects = await IndexedDbManager.getAllObjectsFromDB("dbName", "ObjectStoreName");
```

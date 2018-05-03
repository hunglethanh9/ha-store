<h1 align="center">
  <a title="The resource Manager" href="http://kalm.js.org">
    <img alt="Kalm" width="320px" src="http://res.cloudinary.com/kalm/image/upload/v1487202241/kalm_header.png" />
    <br/><br/>
  </a>
  REST-store
</h1>
<h3 align="center">
  The resource Manager
  <br/><br/><br/>
</h3>
<br/>

[![rest-store](https://img.shields.io/npm/v/rest-store.svg)](https://www.npmjs.com/package/rest-store)
[![Node](https://img.shields.io/badge/node->%3D8.0-blue.svg)](https://nodejs.org)
[![Build Status](https://travis-ci.org/fed135/rest-store.svg?branch=master)](https://travis-ci.org/fed135/rest-store.js)
[![Dependencies Status](https://david-dm.org/fed135/rest-store.js.svg)](https://david-dm.org/fed135/rest-store.js)

---

## How it works

Want to make your app faster and don't want to spend on extra infrastructure ? 

**REST-store** is: 

An in-memory, self-adjustting microcache: 

- Helps reduce the number of requests for 'hot' information
- No noticeable footprint 
- No need for extra caching architecture (redis/memcache)

Adds request dedupping, batching, retrying and circuit-breaking:

- Process-wide request profiling and mapping
- Greatly reduces the number of requests
- Fully configurable

## Use cases

// Sample data query distribution graph

// Call reduction graph

// Median response time graph

## Installing

`npm install rest-store`

## Usage

**Data-Access layer**

```node
function getItems(ids, params) {
    // Some compute-heavy async function or external request to a DB / service
}
```

**Store**
```node
const store = require('rest-store');
const itemStore = store({ getter: { method: getItems }});
```

**Model**
```node
function getItemById(id, params) {
    itemStore.one(id, params)
        .then(item => /* The item you requested */);
}
```

## Options

Name | Required | Default | Description
--- | --- | --- |
getter | true | - | The method to wrap, and how to interpret the returned data. Uses the format `<object>{ method: <function(ids, params)>, responseParser: <function(response, requestedIds)>`
uniqueOptions | false | `[]` | The list of parameters that, when passed, alter the results of the items requested. Ex: 'language', 'view', 'fields', 'country'. These will generate different combinaisons of cache keys.
cache | false | ```{
  enabled: true,
  step: 1000,
  ttl: 10000,
}``` | Caching options for the data
batch | false | ```{
  enabled: false,
  tick: 40,
  limit: 100,
}``` | Batching options for the requests
retry | false | ```{
  enabled: true,
  max: 3,
  scale: {
    mult: 2.5,
    base: 5,
  }
}``` | Retry options for the requests


## Monitoring and events

REST-store emits events to track cache hits, miss and outbound requests.

Event | Description
--- | ---
cacheHit | When the requested item is present in the microcache, or is already being fetched. Prevents another request from being created.
cacheMiss | When the requested item is not present in the microcache and is not currently being fetched. A new request will be made.
batch | When a batch of requests is about to be sent.
batchFailed | Indicates that the batch has failed. Retry policy will dictate if it should be re-attempted.
batchCancelled | Indicates that the batch has reached the allowed number of retries and is now abandonning.
batchSuccess | Indicates that the batch request was successful.
bumpCache | When a call for an item fully loaded in the microcache succeeds, it's ttl gets extended.
clearCache | When an item in the microcache has reached it's ttl and is now being evicted.

## Testing

`npm test`

## References

[White paper - Microcaching]()

## Contribute

Please do! This is an open source project - if you see something that you want, [open an issue](//github.com/kalm/kalm.js/issues/new) or file a pull request.

If you have a major change, it would be better to open an issue first so that we can talk about it. 

I am always looking for more maintainers, as well. Get involved. 

## License 

[Apache 2.0](LICENSE) (c) 2017 Frederic Charette

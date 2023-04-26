# Design an Autocomplete Feature

### Functional Requirements:

* As a user I can see 10 (or k) suggestions for the phrase that I type
  int he search box based on popularity
* Spell check
* ML based recommendation
* Maybe give weight to some suggestions
* It can be personalized and also use my previous history

### Non-functional Requirements:

* High Availability -> Eventual consistency
  - If the search data is slightly off, it is ok
* Latency should be very low, 100ms or less
* Scalable: 1B DAU
* It should be responsive on mobile devices as well
  - Low bandwidth devices
  - The client might be slow and doesn't have too much memory

### Capacity of the System

* 1B DAU
* 50 Searches a day
* 2 autocomplete queries per search


#### How many servers do we need?
Jeff Dean's numbers


#### What is our QPS?

* `1B * 50 * 2 / 100K = 1M QPS`

#### How long does each query take?



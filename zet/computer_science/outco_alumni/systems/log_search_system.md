# Log search System

* Requirements
* What type of events?
* How big are the logs? `1kb`
* Who is using it?
* What kind of searches? `Google search`, `start date/end date`. `SQL`
* Throughput?
* Ingestion `10 logs per second`, `25k customers` -> `250k logs per second`
* Searches: `1k per second`
* `1kb * 250k = 250mb/s * 86400` -> `25tb/day` -> `1pb/month`
* How many users/customers? `25k`
* Batch vs streaming?
* APIs?
* Each customer has their own index? yes
* Log visualization

## Inverted Index

["a", "the", "of"]

["yannick" (3), "varsha" (10), "abhishek" ...]
   |             |
   V             V
 1,500, 432     254 (5), 6943 (5)

 `Elasticsearch`, `Lucene`, `Solr`

 SELECT * FROM LOG WHERE date > yesterday date <= today and search contains "Varsha"


1.) Server Agent
2.) Ingestion Service
3.) Ingestion Queue
4.) Storage Service
5.) Log Database (Inverted Index)

1.) Query Service
2.) Search UI
3.) Log Database (Inverted Index)

Server Agent
Daemon monitors a specific folder, batches

Server Agent -> Ingestion Service -> Queue -> Storage Service -> Log Database
   |                                                  |
  S3                                                 S3

  Server Agent -> Ingestion Service -> Queue -> Storage Service -> Log Database
     |                                                  |
    S3                                                 S3

  Server Agent -> Ingestion Service -> Queue -> Storage Service -> Log Database
     |                                                  |
    S3                                                 S3

Server Agent -> Ingestion Service -> Queue -> Storage Service -> Log Database
   |                                                  |
  S3                                                 S3

For small customers you could get all of these services onto one server. If it's small enough you could probably forgo the queue because it will be able to write immediately.

You mostly want to keep each pipeline in it's own. So there is no possibility to cross contaminate the other customer data.

Government could require a completely different data center. This is to avoid any possible cross contamination. Separation of concerns.

You can do max retries and gives a 3x the time and then possible trigger an alarm. You don't always want it to go right away. This is to not get unwanted warning at the middle of the night. An example of the time extension would be logging into Apple services and getting an incorrect password attempts too many times.

Search UI -> Query Service (SQL -> Lucene query language) -> Log Database (10ms)

### Search UI

* What is the best search UI?

Search: `________________` Start Date: `__ __ ____` End Date: `__ __ ____`

Sort

SQL: `_________________________`


## ELK Stack (Elasticsearch, LogStash, Kibana, Beats)

* [ELK Stack](https://www.elastic.co/what-is/elk-stack)

So, what is the ELK Stack? "ELK" is the acronym for three open source projects:

`Logstash` is a server-side data processing pipeline that ingests data from multiple sources simultaneously, transforms it, and then sends it to a "stash" like `Elasticsearch`.

`Kibana` lets users visualize data with charts and graphs in `Elasticsearch`.

The Elastic Stack is the next evolution of the ELK Stack.



# System Design

1. Use cases + Constraints/estimations/bottlenecks
  - brainstorm; MVP
  - narrow it down
  - # of users
  - amount of data
  - requests per second
2. Entity Relationship Diagram
3. Rationale + calculations for core components
4. Diagram -scale up the system
  - client layer
  - application layer
  - database layer

## Good Tools

* [Repl](Repl.it)
* [Excalidraw](https://excalidraw.com/)

## Question 1

Design Amazon Suggestion Carousel

* Can I see an example of this?
* It's a carousel the recommend other products that are related.
  - related but not same all the time

## Use Cases

* Appears when viewing product page - 1
* List of similar products - 0
* single rec - 1
  - Name - 1
  - Image - 1
  - Price - 1
  - Rating - 1
  - Prime? - 1
  - Rating Count - 1
* Product Stock - 0
* Input: Search Item - 1
* Output: List <Product> - 1
* Combination of Multiple Products - 0
* Recommendation Algo
  - Based on attributes - 0 but could be secondary check
  - Based on description - 0
  - Based on same order - 1
  - Based on product category - 0

* Orders
  - A + B
  - A + T
  - A + B
  - A + Z
    - Select A

### Constraints/Estimations/Bottlenecks

* # of users: powers of 10
  - 100 million users => 10 million daily users
  - 1 million concurrent users
  - Requests per second
    - new page once /5 seconds
    - 200,000 requests/second

* Peak conditions
  - Holidays
  - Prime Day

## Entity Relationship Diagram

* Product
* Order
* Suggestion

### Product

* product_id: String 10
* name: String 10
* image: String 100
* price: Double 10
* rating: Float 10
* rating_count: String 10
* prime: Boolean 10
160 characters * 2 bytes
1 billion products
  320 Billion bytes
  320 GB

### Order

* order_id: String 10
* items: <Product[]> 50
* quantity: <Int[]> 10
* date: Timestamp 10
80 characters * 2 * 100 orders * 100 million users
  1_600_000 mil bytes
  1.6 trillion bytes
  1600 GB
  1.6 TB

### Suggestion

* suggestion_id: String 10
* input: Product 10
* recs: <Product[]> 1000
* date: Timestamp 10
1030 characters * 2 * 1 bill
  2.06 tril bytes
  2.06 TB 

### Relationships

* Product -> Order: Many to Many

* Order -> Product: Many to Many

* Product -> Suggestion: Many to Many

* Suggestion -> Product: Many to Many

### Calculation




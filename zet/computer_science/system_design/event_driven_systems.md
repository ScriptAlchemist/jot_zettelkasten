# The Different Types of Events in Event-Driven Systems

## Thursday, September 29, 2022, 1:07:26PM EDT

Event-driven systems come in all sorts of shapes and sizes. The obvious
commonality is; they all use events to communicate information. These
events come in many shapes and sizes, and determining what does into an
event has an immense impact on the design of your system.

In this we will go over three different types of events. I hope
clarifying these types will allow you to have better discussions about
event-driven architectures and integrations.

## Three Event Archetypes

Each type has its own unique characteristics, strengths, and weaknesses.
None of these types of events is necessarily better than the other, but
given a situation, a particular type may be the better fit.

* The Domain Event
* The Trigger or Signal Event
* The RESTful or "Fat" Event

Let's go over each of them to see what they are, and when they are
useful.

## The Domain Event

For anybody who has an interest in Domain-Driven Design, this well be
the most familiar type of event. A domain event is a record of history,
capturing the intent and any relevant context about an important moment
in time. Domain events focus on the "domain", which means they focus on
things that are relevant to the business. Because they record history
they are expressed in the past tense.

Domain event are named in a way that the intent is clearly expressed. It
is advised to use human language to name these events, try to avoid
"beep boop" language. Instead of naming the event `OrderStateChanged` or
`OrderEvent`, use something like `OrderWasShipped`.

Unlike the other event types, domains events are great for capturing
intent. Because domains events only capture the relevant context of an
important moment, they are also great for capturing change. This gives
event consumers great insights into what is going on. Event-sourced
systems take this a step further, making domain events the corner-stone
of the software model.

Domain events are particularly well suited to use in the creation of
read models. In situations where the read-case requirements are very
different from the data model useful to make decisions. The change and
intent centric representation is great for aggregation, both in the
creation of read models and in analytical data models.

### Example

```php
class OrderWasShipped
{
  public function __construct(
    private OrderId $orderId,
    private MomentOfShipment $shippedAt,
    private ShipmentAddress $shipmentAddress,
  );

  public function orderId(): OrderId
  {
    return $this->orderId;
  }

  public function shippedAt(): MomentOfShipment 
  {
    return $this->shippedAt;
  }

  public function shipmentAddress(): ShipmentAddress
  {
    return $this->shipmentAddress;
  }
}
```

### Up-sides

* Domain events are great for capturing intent and "change". 
* They allow you to build powerful read models that scale much better than complex queries on the original data model.
* Domain intents are powerful for creating analytical models, which can
  provide great insights into what is going on in the business.

### Down-sides

* Domain events expose what goes on inside a domain.
* If a consumer relies on this information, they are coupled to it.
* If domain events are used to create decision models, coupling on these events can put a strain on the development velocity.
* Coupling increases the cost of change, so it's always good to know what you expose and to whom.
* As a default practice, consider every domain event "private", only meant for internal consumption.
* Only through deliberate exposure consumers get access to the events,
  similar to how APIs are used instead of direct database access.

## The Trigger or Signal Event

The trigger or signal event is the tiniest event there is. This event
`usually consists of only an ID to reference an aggregate or entity, and
maybe a time stamp.` As the name *trigger* suggests, these events are
used to trigger a reaction on the consuming side. Triggers are most
often used to notify other business processes of a change. In cases
where you're storing sensitive data (looking at you, GDPR) the use of
triggers can help prevent exposing event infrastructure.

### Example

```php
class OrderWasShipped
{
  public function __construct(
    private OrderId $orderId
  );

  public function orderId(): OrderId
  {
    return $this->orderId;
  }
}
```

### Up-sides

* Triggers are useful when a domain event could contain sensitive data.
* In the cases, the producer sends out a signal and expects the consumer
  to use a secure API to fetch the corresponding ID. 
* Triggers do not easily cause information-level coupling, simply
  because they don't contain any.

### Down-sides

* Since triggers do not contain any information, consumers are always
  reliant on an API.
* When many consumers consume many events, this can put some unexpected
  load on your systems.
* The absence of information also limits the ability to aggregate data.
* Due to the asynchronous nature, the data retrieved by from the API
  might be in a different state that the consumer expects.
* A consumer must always check if the resource retrieves from the API is
  what they expect and be prepared to handle any of the possible states
  a resource may be in.
* If an order is shipped, but the merchant immediately cancels the
  shipment, the consumer may retrieve a shipment resource the doesn't
  match the status the event name suggests. When delayed processing of
  events occurs, this can create unexpected results.

## The RESTful or "Fat" Event

The last archetype is the "fat" event. I prefer the term RESTful event,
because it describes better what is in the payload. This type of event
contains the full resource representation as you would retrieve from a
RESTful API. It is an excellent integration event and is most useful for
outside consumers.

When compared to triggers, RESTful events prevent consumers from making
a roundtrip to the API. If you compare it to the domain event, it
prevents a consumer having to combine multiple events to get the full
picture.

### Example

```php
class OrderWasShipped
{
  public function __construct(
    private OrderId $orderId,
    private OrderLines $orderLines,
    private DiscountCodes $discoutCodes,
    private OrderAmount $orderAmount,
    private MomentOfShipment $shippedAt,
    private ShipmentAddress $shipmentAddress,
  );

  public function orderId(): OrderId
  {
    return $this->orderId;
  }

  public function orderLines(): OrderLines
  {
    return $this->orderLines;
  }

  public function discountCodes(): DiscountCodes
  {
    return $this->discountCodes;
  }

  public function orderAmount(): OrderAmount
  {
    return $this->orderAmount;
  }

  public function shippedAt(): MomentOfShipment
  {
    return $this->shippedAt;
  }

  public function shipmentAddress(): ShipmentAddress
  {
    return $this->shipmentAddress;
  }
}
```

### Up-sides

* RESTful events are great for pushing state out to consumers.
* In one event, consumers know everything about the resource.
* Per resource, only the last event is needed to be back up to date,
  which is great for disaster recovery.
* In cases where another service is dependent on state from your
  service, the use of RESTful events is a great way to push the state
  there.
* Doing so will remove the direct dependency on the service in cases
  where eventual consistency is acceptable.

### Down-sides

* RESTful events have only been useful as an integration too for
  `outside` consumers.
* They are not useful for internal modelling. They are big, and more
  anonymous, and convey less intent, making them less suitable for
  internal modelling.
* RESTful events often require yo to build and anti-corruption layer to
  translate other types of events into RESTful ones, which is `extra`
  work

## Having meaningful discussions about events

During technical discussions, it's tempting to jump to solutions. Just
add the field just expose this internal event to an external consumer,
fix the problem. I'd hope by having identified a couple of types of
events, you can take this information into the discussions you're
having. Try to identify which type of events are at plan, which
characteristics they have, and how those affect the situations in which
you apply them. Exposing a domain event? Be mindful of information-level
coupling. Adding more and more information into and event because a
consumer needs them? Perhaps switch to a RESTful event. In the end, just
keep in mind that the different styles of communication work best if
they are applied in the right context. It's up to yo the be aware of
this and make the right choice.

## Bonus: Transforming Events in the ACL

One of the beautiful qualities of event and message based designs is the
possibility of translation. Translation layers, often referred to as
anti-corruption layers, facilitate decoupling at information-level. ACLs
filter and transform information. This can be done at either side of the
integration, producer or consumer. ACLs can also be implemented as
relays, pieces of logic that consume and forward (produce) messages.
EventSauce recently shipped with a comprehensive toolset to build your
own ACLs. Read all about them in the [docs](https://eventsauce.io/docs/advanced/anti-corruption-layer/)



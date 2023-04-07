# Vehicle Controls System Design

## What questions can we ask?

* Global?
  - Localization
  - Latency
  - Internalization
* Daily Active Users?
  - Hundreds of millions to billion
* Sentry Mode?
  - Video streaming
* Offline mode?
  - Summons
* Latency?
* Battery?
* Storage requirement?
* Require a persistent connection to the server 24hr/7d?
No persistent connection

```
Mobile App
    |
    V
LB (Consistent Hashing)
    |
    V
app server -> cache
           -> data store (vin, user) MongoDB
           -> car (wake), wait, socket connection, http connection, etc, tell me about yourself?

           -> Streaming server -> car

Driving 34mph 40mph
Charging


                      1
                      2
                      3
                      ...
                      1000
 pipeline1.teslaapi.com
 pipeline2.teslaapi.com
 ...
 pipeline1000
```

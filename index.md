---
layout: default
---

# Gatling CheatSheet

## Simplest simulation
        
```scala 
import io.gatling.core.Predef._
import io.gatling.http.Predef._

class SimplestSimulation extends Simulation {
  setUp(scenario("Petstore")
    .exec(http("Home").get("http://10.34.199.64:8000/petstoreee7-7.0"))
    .inject(atOnceUsers(1)))
}   
```

### Using an HTTP proxy

```scala 
setUp(scenario("Hello")
    .exec(http("World").get("http://www.google.fr"))
    .inject(atOnceUsers(1)))
    .protocols(http.proxy(Proxy("proxy", 3128)))}   
```

## Scenario

## Pauses

```scala 
scenario("sample")
  // ...
  .pause(2, 3) // will make a random pause of 2-3 seconds
  .pause(2) // will make a fixed pause of 2 seconds  
```

```scala 
import scala.concurrent.duration.DurationInt

scenario("sample")
  // ...
  .pause(2.milliseconds) 
```


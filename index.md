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

Official doc : http://gatling.io/docs/2.2.3/general/scenario.html

### Pauses

```scala 
scenario("sample")
  // ...
  .pause(2, 3) // will make a random pause of 2-3 seconds
  .pause(2) // will make a fixed pause of 2 seconds  
```

```scala 
scenario("sample")
  // ...
  .pause(200.milliseconds) // fixed pause of 0.2 second
```

Note : for the syntax ```2.milliseconds``` or ```5.seconds``` to compile, you need to add this import :

```scala 
import scala.concurrent.duration.DurationInt
```

More info : http://gatling.io/docs/2.2.3/general/scenario.html#pause

### Loops

```scala 
scenario("sample")
  .repeat(3)( // repeat 3 times
  	 exec(http("google").get("http://www.google.fr"))
  )
```

More info : http://gatling.io/docs/2.2.3/general/scenario.html#scenario-loops

## Injection of Virtual Users

```scala 
val scn=scenario("My Scenario")

setUp( 
  scn.inject( 
    nothingFor(4.seconds), 
    atOnceUsers(10), 
    rampUsers(10) over(5.seconds))
```

### Rampup

```scala 
rampUsers(10) over(5.seconds) 
// linear rampup 
// 10 users added over 5 seconds (1 extra user every 500 ms)
```

```scala 
constantUsersPerSec(10) during(5.seconds) 
// adds 10 users every second
// (so a total of 50 users after 5 seconds)
```

### At once

```scala 
nothingFor(4.seconds)
// no new users added during 4 seconds
```

```scala 
atOnceUsers(10)
// 10 users added immediately
// not really recommended since it can hammer down the tested server
```

```scala 
heavisideUsers(10) over(2.seconds)
// better approximation of a peak of users
```

More info : http://gatling.io/docs/2.2.3/general/simulation_setup.html


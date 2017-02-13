---
layout: default
published: true
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

### GET requests

```scala 
http("name")
  .get("http://www.google.fr")
  .queryParam("query", "gatling")
  .queryParam("source", "mobile")
  .header("Accept-Language", "fr")
```

### POST requests

Form params :

```scala 
http("name")
  .post("http://mysite.com/users")
  .formParam("firstname", "gatling")
  .formParam("lastname", "stress tool")
  .header("Accept-Language", "fr")
```

Body file : (must be in `src/test/resources/bodies`)

```scala 
http("name")
  .post("http://mysite.com/users")
  .body(RawFileBody("bodyFileName.xml")).asXml
  .header("Content-type","application/json")
```


## Scenario

Official doc : <http://gatling.io/docs/2.2.3/general/scenario.html>

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

More info : <http://gatling.io/docs/2.2.3/general/scenario.html#pause>

### Loops

```scala 
scenario("sample")
  .repeat(3)( // repeat 3 times
  	 exec(http("google").get("http://www.google.fr"))
  )
```

More info : <http://gatling.io/docs/2.2.3/general/scenario.html#scenario-loops>

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
constantUsersPerSec(10) during(5.seconds) 
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

More info : <http://gatling.io/docs/2.2.3/general/simulation_setup.html>

## Checks and Assertions

### Checks

Checking status and json data :

```scala 
http("name").get("/path")
  .check(status.is(200))
  .check(jsonPath("$.name").is("some name"))
```

Saving response data to gatling session

```scala 
http("name").get("/path")
  .check(header("location").saveAs("newLocation"))
  .check(jsonPath("$.name").saveAs("name"))
  
// You can now use $newLocation and $name in your requests :
http("get home").get("/users/${name}")
```

More info : <http://gatling.io/docs/2.2.3/http/http_check.html#http-check>

### Assertions

```scala 
setUp(scn).assertions( 
  global.responseTime.mean.lessThan(50), // mean resp time < 50 ms    
  forAll.failedRequests.percent.lessThan(5) // for each request, < 5% failure
)
// data : responseTime (in milliseconds), requestsPerSec
// requests : allRequests, failedRequests, successfulRequests
// units : percent, permillion, count
// aggregations : min, max, mean, stdDev, percentile1 to percentile4
// comparison : lessThan(threshold), greaterThan(threshold), between(thresholdMin, thresholdMax), 
//              is(value), in(sequence)
```

More info : <http://gatling.io/docs/2.2.3/general/assertions.html>

## Configuration
      
### Reading configuration

```scala 
val config = ConfigFactory.load("gatling.conf")

val maxPlot = config.getInt("gatling.charting.maxPlotPerSeries")   
```

### Sample config file

Put it in `src/test/resources/`

```
gatling {
  charting {
    maxPlotPerSeries= 1000 # Number of points per graph
    indicators {
      percentile1 = 50      # Value for the 1st percentile
      percentile2 = 75      # Value for the 2nd percentile  
    } 
  } 
}
```

### Overriding configuration by system properties

```
mvn gatling:test -Dgatling.simulationClass=demo.MySim -Dgatling.charting.maxPlotPerSeries=500
```

### Logs

Sample logback.xml :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%-5level] %logger{15} - %msg%n%rEx
      </pattern>
      <immediateFlush>false</immediateFlush>
    </encoder>
  </appender>

  <!-- Uncomment for logging ALL HTTP request and responses -->
  <!-- <logger name="io.gatling.http.ahc" level="TRACE" /> -->
  <!-- <logger name="io.gatling.http.response" level="TRACE" /> -->
  <!-- Uncomment for logging ONLY FAILED HTTP request and responses -->
  <!-- <logger name="io.gatling.http.ahc" level="DEBUG" /> -->
  <!-- <logger name="io.gatling.http.response" level="DEBUG" /> -->

  <root level="WARN">
    <appender-ref ref="CONSOLE" />
  </root>

</configuration>
```

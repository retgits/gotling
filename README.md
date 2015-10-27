# gotling
Simple golang-based load test application using YAML documents as specification.

_Please note that this is my very first golang program and is probably full of anti-patterns and bad use of golang constructs._

## What it does
- Provides high-throughput load testing of HTTP services 
- Supports GET, POST, PUT and DELETE
- Live metrics using HTML5 canvas from canvasjs
- Request URLs and bodies can contain ${paramName} parameters
- ${paramName} values can be extracted from HTTP response bodies and bound to a User context

## Usage
Define your test setup in a .yml file

    ---
    iterations: 10          # Number of iterations per user
    users: 10               # Number of users
    rampup: 20              # seconds
    actions:                # List of actions. Currently supports http, sleep
      - http:
          method: GET                                  # GET, POST, PUT, DELETE
          url: http://localhost:8183/courses           # URL. Can include ${paramName} parameters
          accept: json                                 # Only 'json' is currently supported
          response:                                    # Defines response handling
            jsonpath: $[*].id+                         # JsonPath expression to capture data from JSON response
            variable: courseId                         # Parameter name for captured value
            index: first                               # If > 1 results found - which result to store in 'variable': 
                                                       # first, random, last
      - sleep:
          duration: 3                                  # Sleep duration in seconds. Will block current user
      - http:
          method: GET
          url: http://localhost:8183/courses/${courseId}
          accept: json
          response:
            jsonpath: $.author+
            variable: author
            index: first
      - sleep:
            duration: 3

## Realtime dashboard
Access at http://localhost:8182

Click "connect" to connect to the currently executing test.

![Gotling dashboard](gotling-dashboard.png)

## Uses the following libraries
- gopkg.in/yaml.v2
- NodePrime/jsonpath - https://github.com/NodePrime/jsonpath/blob/master/README.md
- gorilla/websocket
- canvasjs

## License
Licensed under the MIT license.

See LICENSE
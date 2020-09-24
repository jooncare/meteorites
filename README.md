# Coding Challenge
This respository contains a coding challenge. The objective of the
exercise is to write a small service with endpoints that return
information about Earth meteorite landings.

Your backend will expose the endpoints defined below. The data store
for this problem is the y77d-th95.json file in this directory, which
came from [here](https://data.nasa.gov/resource/y77d-th95.json). You
are welcome to read and deserialize the data in the file on each
request.

# Requirements and Guidelines
- Endpoints return JSON with `Content-type` set to `application/json`.
- The service runs on port 8273. You can accomplish this by encoding
  it or providing a command-line argument to specify the port.
- If you need to implement some unspecified behavior, make a decision
  on what to do and explain your decision in a commit message.
- You should include a README in your submission that describes how to
  set up and run the service. If your service has third-party
  dependencies, indicate how those should be installed.
- You do not need to have a complete, standard packaging setup. For
  example, if you use Python, including a requirements.txt file for
  dependencies is fine.
- You are welcome to use any language and web application framework
  you want, so long as the tools to run the service are readily and
  freely available.
- Do your work in this repository. See the next section for more
  details.

## Returning Your Solution
Please observe the following requirements for returning your solution.
- Do your work in commits in this repository.
- Make commits with a reasonable granularity and informative commit messages.
- When it's time to submit, create a patch file for your changes by
  doing `git format-patch origin/main..HEAD --stdout >
  solution.patch` and return solution.patch in an email!

# Testing
Use the examples below, in addition to other cases you may create, to
test your service for correctness.

# Expected Endpoints
## /names
This endpoint returns the names of all of the meteorites in an array.

Example:
```
$ http "http://localhost:8273/names"
HTTP/1.0 200 OK
Content-Length: 11907
Content-Type: application/json

[
  "Aachen",
  "Aarhus",
  "Abee",
  .
  .
  .
  "Tjabe",
  "Tjerebon",
  "Tomakovka"
]
```

The dots aren't actually expected. They are to avoid pasting a large
list into this file. :)

## /name/[name]
This endpoint returns the JSON record of the meteorite with the
matching name with status code 200. If no meteorite with the matching
name exists, instead return a response with status code 404.

Examples:
```
$ http "http://localhost:8273/name/Aachen"
HTTP/1.0 200 OK
Content-Length: 225
Content-Type: application/json

{
    "fall": "Fell",
    "geolocation": {
        "coordinates": [
            6.08333,
            50.775
        ],
        "type": "Point"
    },
    "id": "1",
    "mass": "21",
    "name": "Aachen",
    "nametype": "Valid",
    "recclass": "L5",
    "reclat": "50.775000",
    "reclong": "6.083330",
    "year": "1880-01-01T00:00:00.000"
}

$ http "http://localhost:8273/name/NotReal"
HTTP/1.0 404 NOT FOUND
Content-Length: 0
Content-Type: text/html; charset=utf-8
```

## /mass
This endpoint accepts query string parameters to limit the results.
- `gt` returns meteorites whose mass exceeds the given floating-point
  value.
- `lt` returns meteorites whose mass is less than the given
  floating-point value.

Conditions:
- All conditions (`gt` and `lt`) must be satisfied. Thus, for example,
  if `gt=5` and `lt=10` are given, only meteorites whose mass is
  greater than five _and_ less than ten are returned.
- If no conditions are given, all meteorites are returned.
- At most one instance of `gt` will be in the request.
- At most one instance of `lt` will be in the request.
- You can assume the inputs are legal.

Examples:

```
$ http "http://localhost:8273/mass?gt=22000000"
HTTP/1.0 200 OK
Content-Length: 254
Content-Type: application/json

[
    {
        "fall": "Fell",
        "geolocation": {
            "coordinates": [
                134.65333,
                46.16
            ],
            "type": "Point"
        },
        "id": "23593",
        "mass": "23000000",
        "name": "Sikhote-Alin",
        "nametype": "Valid",
        "recclass": "Iron, IIAB",
        "reclat": "46.160000",
        "reclong": "134.653330",
        "year": "1947-01-01T00:00:00.000"
    }
]

$ http "http://localhost:8273/mass?gt=3000000&lt=23000000"
HTTP/1.0 200 OK
Content-Length: 238
Content-Type: application/json

[
    {
        "fall": "Fell",
        "geolocation": {
            "coordinates": [
                126.16667,
                44.05
            ],
            "type": "Point"
        },
        "id": "12171",
        "mass": "4000000",
        "name": "Jilin",
        "nametype": "Valid",
        "recclass": "H5",
        "reclat": "44.050000",
        "reclong": "126.166670",
        "year": "1976-01-01T00:00:00.000"
    }
]

$ http "http://localhost:8273/mass?lt=0.2"
HTTP/1.0 200 OK
Content-Length: 251
Content-Type: application/json

[
    {
        "fall": "Fell",
        "geolocation": {
            "coordinates": [
                27.26667,
                44.11667
            ],
            "type": "Point"
        },
        "id": "55584",
        "mass": "0.15",
        "name": "Silistra",
        "nametype": "Valid",
        "recclass": "Achondrite-ung",
        "reclat": "44.116670",
        "reclong": "27.266670",
        "year": "1917-01-01T00:00:00.000"
    }
]

$ http "http://localhost:8273/mass?lt=0.1"
HTTP/1.0 200 OK
Content-Length: 3
Content-Type: application/json

[]
```

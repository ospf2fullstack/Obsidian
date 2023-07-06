---
title: API's 
publish: true
---
# API
When using REST API's, you have two primary ways of doing a GET request. 

Depending on the platform, the programmer typically trails the TLD (Top Level Domain) with the api syntax. So, .com/v1 for version 1 of the API. This is a great way, if you're the developer to write an API so you can support reverse capabilities.
## CuRL
A basic curl command will pull the index.html content and log it to the terminal. 
```bash
curl google.com 
```

## Postman
In Postman, you create a new collection, create a new GET request. I like to start with SpaceX OpenAPI

### Call a Collection
```
# this will list all dragon rockets
https://api.spacexdata.com/v3/dragons
```

### Call Explicit Collection Contents
To populate a specific 
```
# this will call, explicitly the dragon1
https://api.spacexdata.com/v3/dragons/dragon1
```

Typically, these trailing inputs can be replaced with variables, Key Value Pairs. 
```
https://api.spacexdata.com/v3/dragons/{id}

# in the query params, you can add a Key of id and a value of 1. This 
# in the URI, that will now be populated as ?id=1
# the output will be the same as the above command: 
https://api.spacexdata.com/v3/dragons/dragon1
https://api.spacexdata.com/v3/dragons/?id=1
```
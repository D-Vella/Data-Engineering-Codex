# Requests Library

## Description:
This Requests library is used to make API requests in Python. 

## Documentation:
The documentation for the library can be found at https://requests.readthedocs.io/en/latest/

## Exmaple usage

```python
import requests

api_response = requests.get('https://api.github.com/events')
api_response.text # output the reponse from the API
```

## Notes:

### Setting Headers:
APIs will often call for certain header settings. You can add them as such:
```python
import requests

my_headers = {'user-agent': 'my-app/0.0.1', 'Accept':'application/json'}
api_response = requests.get('https://api.github.com/events', headers=my_headers)
```

### Parameters:
APIs will make use of parameters (in the URL they are after the `?` and will be chained with the `&` symbol.) frequently this is used for paging. The values need to be text but can put constructed into a `dict` and passed as part of the `params` argument:

```python
import requests

my_headers = {'user-agent': 'my-app/0.0.1', 'Accept':'application/json'}
my_params = {'page':'1','pagesize':'500'}
api_response = requests.get('https://api.github.com/events', headers=my_headers, params=my_params)
```

### Body Content:
Depnding in the API you may need to have a payload or body in the request. 

```python
import requests

my_headers = {'user-agent': 'my-app/0.0.1', 'Accept':'application/json'}
my_params = {'page':'1','pagesize':'500'}

body_content = """
<tsRequest>
  <credentials name="administrator" password="passw0rd">
    <site contentUrl="" />
  </credentials>
</tsRequest>
"""
api_response = requests.get('https://api.github.com/events', headers=my_headers, params=my_params, data=body_content)
```

### Checking Response status:
There are 2 methods here:
1. use `request.raise_for_status()` this will raise a `HTTPError` if it gets an error status code like 404. If you get 200 (OK) then nothing happens.
2. use `request.status_code` to get the interger code value.


## Recommended Practices:

1. Include a timeout in a request using the `timeout=5` parameter.

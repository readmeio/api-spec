This is the specification that all `api` SDKs will adhere to. The [Python library](https://github.com/readmeio/api-python) is the canonical example of the spec. (The Node version is better, but also includes the command line tool)

Existing implementations:

  * [Node](https://github.com/readmeio/api)
  * [Python](https://github.com/readmeio/api-python)
  
# Code Examples

The SDK should implement a code snippet that works similar to the following code snippet, but optimized to feel like the like the specific language. Here it is in two languages:

**Python:**

```python
import api
api = api.config('API_KEY')

result, meta = api('math').run('multiply', {
    'x': 1,
    'y': 2
})

if meta.error:
    print 'oh no'
else:
    print result
```


**Node:**

```node
var api = require('api').config('API_KEY');

api('math').run('multiply', {
    'x': 1,
    'y': 2,
}, function(err, res) {
  if(err) return console.log(err);
  console.log(res);
})
```

# What's happening

The `config` function sets up the API with the config key. You should be able to set up multiple different API keys in the same file (meaning that the `config` function doesn't set it globally, but rather returns an instance of the API module). For example, in the Node version, you should be able to do `var api1 = require('api').config('key');var api2 = require('api').config('anotherkey');`.

We have the concept of "services", which contain "actions". A service would be analogous to an API, and a action would be analogous to an endpoint. For example, the "math" service might have actions like "multiply" and "add".

The variable returned by `config` should (language permitting) be a function, which accepts the service as the only parameter. The service is in the format `organization/service@version`, with only service being required. `organization` will be required for private services, ~~and `version` will primarily be used in development mode~~. (**NOTE:** The API doesn't accept @version yet!)

This should return an object, with the function `run`. This accepts two parameters, an action name and a hash of data to pass along (whatever the language equivilant of a JSON blob would be). (**NOTE:** Can it ever accept just one variable? What about if only one non-object variable is passed in, it goes to the first param in the list?) Some languages could have a third parameter that's a callback (like JS).

What happens next is up to the language. There's three things that need to be returned: a result, an error, and some (in the future) metadata. In Node, we can use either a callback or promises. In other synchronous languages, it's harder. For Python, we return a result and a meta object. The meta object has an `error` value. Most languages should probably do it this way, unless throwing an exception from an API failure is common in the language.

# How the request works

The request that is made when `run` is called is a simple POST to the endpoint `https://api.readme.build/v1/run/{service}/{action}`. The body should be the JSON blob of data passed in, and the API key should be passed as basic auth, with the username being the API key and the password being blank. (So, `headers['Authorization'] = 'Basic ' + base64(key + ':');`, although most request libraries simplify this.)

If it's successful, you'll get a 200 error and the body will be the exact content to return. If not, you'll get an error status code (**TODO:** What is this code?). The body will contain a JSON blob with `{"error": "message to print out", "code": "ErrorName"}`. Print out the `.error` value in red to the console.


If you get the following headers back, do the following:

| Header  | Action |
| ------------- | ------------- |
| X-Build-Deprecated  | If `true` (likely a string, depends on the language), print out to the console in yellow "{service} v{version} is deprecated! Run `api update {service}` to use the latest version" (**TODO:** Should probably be a URL?) |

# TODO:

Python needs:

  * Split up `organization/service@version`
  
Everywhere:

  * Deal with updating to the newest version for soft deprecations
  * Will there be a hard deprecation header? or just an error?
  * How do we deal with non-JSON responses (like just an integer)
  * Do we handle team names yet? How so? Versions?

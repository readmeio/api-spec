This is a language-specific API client for [ReadMe Build](https://readme.build). It's based on the [api spec](https://github.com/readmeio/api-spec).

# Installation

Here's how to install the package...

# Usage

Here's how you call it:

```python
import api
api = api.config('...')

val, res = api('temp-deprecated').run('sayHello', {
    'name': 'hi'
})

if res.error:
    print 'oh no'
else:
    print val
```

# Running tests

How do you run tests?

# Deploying to the package manager

How is this deployed to a package manager?

# Credits

  * Credit

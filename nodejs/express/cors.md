sources
(Express js documentation)["https://expressjs.com/en/resources/middleware/cors.html"]



CORS is a Node.js middleware for Express/Connect that sets 
CORS response headers. These headers tell browsers which origins 
can read responses from your server.

[!IMPORTANT] How CORS Works: This package sets response 
headers—it doesn’t block requests. CORS is enforced by browsers: 
they check the headers and decide if JavaScript can read the response. Non-browser clients (curl, Postman, other servers) ignore CORS 
entirely. See the MDN CORS guide for details.


``` bash
npm install cors
```

```typescript

//Enables all requests

var express = require('express')
var cors = require('cors')
var app = express()

// Adds headers: Access-Control-Allow-Origin: *
app.use(cors())

app.get('/products/:id', function (req, res, next) {
  res.json({msg: 'Hello'})
})

app.listen(80, function () {
  console.log('web server listening on port 80')
})
```

### Enable cors for a single route

```typescript
var express = require('express')
var cors = require('cors')
var app = express()

// Adds headers: Access-Control-Allow-Origin: *
app.get('/products/:id', cors(), function (req, res, next) {
  res.json({msg: 'Hello'})
})

app.listen(80, function () {
  console.log('web server listening on port 80')
})
```

### Configuring cors

```typescript
var corsOptions = {
    origin: function (origin,callback) {
        return callback("",null);

        //error
        return callback("", new Error());
    }
}

```

### Enabling CORS Pre-Flight

```typescript
var express = require('express')
var cors = require('cors')
var app = express()

app.options('/products/:id', cors()) // preflight for DELETE
app.del('/products/:id', cors(), function (req, res, next) {
  res.json({msg: 'Hello'})
})

app.listen(80, function () {
  console.log('web server listening on port 80')
})
```

or enable pre-flight everywhere.

```typescript
app.options('*', cors()) // include before other routes
````

NOTE: When using this middleware as an application level 
middleware (for example, app.use(cors())), pre-flight 
requests are already handled for all routes.



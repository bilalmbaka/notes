sources:
(Express docs)["https://expressjs.com/en/resources/middleware/morgan.html"]


```typescript
var morgan = require('morgan')

///Using a predefined format string
morgan('tiny')
morgan('combined')
morgan('common')
morgan('dev')
morgan('short')

```

### creating a token for custom logs

```typescript
morgan.token("body", (req: express.Request, _: express.Response) => {
  return JSON.stringify(req.body);
});
```


### write to a log file

```typescript
var express = require('express')
var morgan = require('morgan')
var path = require('path')
var rfs = require('rotating-file-stream') // version 2.x

var app = express()

// create a rotating write stream
var accessLogStream = rfs.createStream('access.log', {
  interval: '1d', // rotate daily
  path: path.join(__dirname, 'log')
})

// setup the logger
app.use(morgan('combined', { stream: accessLogStream }))

app.get('/', function (req, res) {
  res.send('hello, world!')
})
```

### split / dual logging

````
var express = require('express')
var fs = require('fs')
var morgan = require('morgan')
var path = require('path')

var app = express()

// log only 4xx and 5xx responses to console
app.use(morgan('dev', {
  skip: function (req, res) { return res.statusCode < 400 }
}))

// log all requests to access.log
app.use(morgan('common', {
  stream: fs.createWriteStream(path.join(__dirname, 'access.log'), { flags: 'a' })
}))

app.get('/', function (req, res) {
  res.send('hello, world!')
})
```
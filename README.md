engine.io-session
=================

[![NPM version](https://badge.fury.io/js/engine.io-session.png)](http://badge.fury.io/js/engine.io-session)
[![Build Status](https://travis-ci.org/matkl/engine.io-session.png?branch=master)](https://travis-ci.org/matkl/engine.io-session)

An [engine.io](https://github.com/LearnBoost/engine.io) plugin that allows you to read sessions created by the [Connect](http://senchalabs.github.com/connect) session middleware.

It obtains the session id from the HTTP request headers and fetches the session data from a specified session store.

## Installation

```
npm install engine.io-session
```

## Usage

### Example using Connect

```js
var http = require('http');
var connect = require('connect');
var eio = require('engine.io');
var eioSession = require('engine.io-session');

var app = connect();
var cookieParser = connect.cookieParser();
var sessionStore = new connect.session.MemoryStore();
var sessionKey = 'sid';
var sessionSecret = 'your secret here';

app.use(cookieParser);
app.use(connect.session({ store: sessionStore, key: sessionKey, secret: sessionSecret }));
app.use(function(req, res) {
  // Save variable to session.
  req.session.foo = 'bar';
  res.end();
});

var httpServer = http.createServer(app).listen(5000);
var server = eio.attach(httpServer);

server.on('connection', eioSession({
  cookieParser: cookieParser,
  store: sessionStore,
  key: sessionKey,
  secret: sessionSecret
}));

server.on('session', function(socket, session) {
  // Receive variable from session.
  console.log(session.foo); // bar
});
```

### Example using Express

Same as above. Replace `connect` with `express`.

## Events

The `session` events will be fired on the `Server` and `Socket` objects.

### Server

- `session`
    - Called when a session for a socket is available.
    - **Arguments**
      - `Socket`: the Socket object
      - `session`: the session data

### Socket

- `session`
    - Called when a session for this socket is available.
    - **Arguments**
      - `session`: the session data

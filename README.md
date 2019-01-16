@curaelabs/express-ipfilter: A light-weight IP address based filtering system
=================================================================================

This package provides easy IP based access control. This can be achieved either by blacklisting certain IPs and whitelisting all others, or whitelisting certain IPs and blacklisting all others.

## Version
0.0.1

## Installation

Recommended installation is with npm. To add node-ipfilter to your project, do:

    npm install @curaelabs/express-ipfilter

## Usage with Express

> NOTE: Starting with version 0.1.0, allow forwarded IP addresses through headers (forward, Cloudflare, Codio) are disabled by **default**. You must explicitly enable them by adding them to the `allowedHeaders` list.

Blacklisting certain IP addresses, while allowing all other IPs:

```javascript
// Init dependencies
var express = require('express'),
    ipfilter = require('express-ipfilter').IpFilter;

// Blacklist the following IPs
var ips = ['127.0.0.1'];

// Create the server
app.use(ipfilter(ips));
app.listen(3000);
```

Whitelisting certain IP addresses, while denying all other IPs:

```javascript
// Init dependencies
// Init dependencies
var express = require('express'),
    ipfilter = require('express-ipfilter').IpFilter;

// Whitelist the following IPs
var ips = ['127.0.0.1'];

// Create the server
app.use(ipfilter(ips, {mode: 'allow'}));

module.exports = app;
```

Using CIDR subnet masks for ranges:

```javascript
var ips = ['127.0.0.1/24'];

// Create the server
app.use(ipfilter(ips, {mode: 'allow'}));

module.exports = app;
```

Using IP ranges:

```javascript
var ips = [['127.0.0.1','127.0.0.10']];

// Create the server
app.use(ipfilter(ips, {mode: 'allow'}));

module.exports = app;
```

Using a function to get Ips:

```javascript
var ips = function() { return ['127.0.0.1']; };

// Create the server
app.use(ipfilter(ips, {mode: 'allow'}));

module.exports = app;
```

## Error Handling

When an IP is denied, an IpDeniedError will be thrown by the middleware. If you do not handle the error, it will cause your app to crash due to an unhandled exception. Here is an example of how to handle the error, which can also be found in the example app:

```
if (app.get('env') === 'development') {
  app.use(function(err, req, res, _next) {
    console.log('Error handler', err);
    if(err instanceof IpDeniedError){
      res.status(401);
    }else{
      res.status(err.status || 500);
    }

    res.render('error', {
      message: 'You shall not pass',
      error: err
    });
  });
}
```

You will need to require the `IpDeniedError` type in order to handle it.


## Options

| Property      | Description   | Type  | Default|
| ------------- |-------------| -----|--------|
| mode   | whether to *deny* or *allow* to the IPs provided | string|deny|
| log   | console log actions | boolean|true|
| logLevel | level of logging (*all*,*deny*,*allow*) | string | all
| allowedHeaders | an array of strings for header names that are acceptable for retrieving an IP address | array | [] |
| excluding   | routes that should be excluded from ip filtering | array|[]|
| detectIp | define a custom function that takes an Express request object and returns an IP address to test against | function | built-in detection |

> A note on detectIp

If you need to parse an IP address in a way that is not supported by default, you can write your own parser and pass that to `ipfilter`.

```
function customDetection(req){
  var ipAddress;

  ipAddress = req.connection.remoteAddress.replace(/\//g, '.');

  return ipAddress;
}

ipfilter(ids, {detectIp: customDetection});

```

## Contributing

See the `CONTRIBUTING.MD` document for more information on contributing.

### Building from source

You can run `grunt` to build the source.  This will run `eslint` and `babel` against `src/ipfilter.js`.

There is an included `example` project that will load the package from the local build for testing.

### Running Tests

Run tests by using

`grunt test`

This will run `eslint`,`babel`, and `mocha` and output coverage data into `coverage`.  Any pull request you submit needs to be accompanied by a test.

## Changelog

0.0.1

* First revision - forked from express-filter
* Updated all packages
* Removed vulnerabilities

## Credits

BaM Interactive - [code.bamideas.com](http://code.bamideas.com)

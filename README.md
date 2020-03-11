[![Build Status](https://travis-ci.org/bluzelle/blzjs.svg?branch=devel)](https://travis-ci.org/bluzelle/blzjs) [![Coverage Status](https://coveralls.io/repos/github/bluzelle/blzjs/badge.svg)](https://coveralls.io/github/bluzelle/blzjs)
<a href="https://bluzelle.com/"><img src='https://raw.githubusercontent.com/bluzelle/api/master/source/images/Bluzelle%20-%20Logo%20-%20Big%20-%20Colour.png' alt="Bluzelle" style="width: 100%"/></a>

**blzjs** is a JavaScript library that can be used to access the Bluzelle database service.

# blzjs API documentation
Read below for detailed documentation on how to use the Bluzelle database service.

![#1589F0](https://placehold.it/15/1589F0/000000?text=+) The Bluzelle JavaScript library works with promises (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) to model asynchronous behavior. Ensure that dependent calls to the Bluzelle database are within `.then()` blocks or within asynchronous functions (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function). Also ensure that promises exceptions are caught and handled.

![#1589F0](https://placehold.it/15/1589F0/000000?text=+) Keys and values in the Bluzelle database are plain strings to ensure compatibility for all forms of serialization. JavaScript applications will probably want to use `JSON.stringify(obj)` and `JSON.parse(str)` to convert object data to and from string format.

![#1589F0](https://placehold.it/15/1589F0/000000?text=+) Some API functions take *gas_info* as a parameter. This is a JavaScript object containing parameters related to gas consumption as follows:
```javascript
{
    'max_gas': '0', // maximum amount of gas to consume for this call (integer)
    'max_fee': '0', // maximum amount to charge for this call (integer, in bnt)
    'gas_price': '0.0' // maximum price to pay for gas (floating point, in bnt)
};
````
All values are optional. The `max_gas` value will always be honored if present, otherwise a default value will be used. If both `max_fee` and `gas_price` are specified, `gas_price` will be ignored and calculated based on the provided `max_fee`.

![#1589F0](https://placehold.it/15/1589F0/000000?text=+) The example code in the `samples` directory requires Node.js in order to run. For instructions on how to install Node.js for your platform, please see http://nodejs.org

The samples require configuration to be set in the file `blz-config.js`. You can copy and modify the `blz-config.js.sample` file for this purpose.

## Exports

### version (tbd)

The bluzelle-js version.

```javascript
const {version} = require('bluzelle');

assert(version === '0.6.843'); // your version may be different
```

### bluzelle\({...}\)

Configures the Bluzelle connection. This may be called multiple times to create multiple clients.

```javascript
const {bluzelle} = require('bluzelle');

const api = await bluzelle({
    address: 'bluzelle1xhz23a58mku7ch3hx8f9hrx6he6gyujq57y3kp',
    mnemonic: 'volcano arrest ceiling physical concert sunset absent hungry tobacco canal census era pretty car code crunch inside behind afraid express giraffe reflect stadium luxury',
    endpoint: "http://localhost:1317",
    uuid:     "20fc19d4-7c9d-4b5c-9578-8cedd756e0ea",
    chain_id: "bluzelle"
});
```

| Argument | Description |
| :--- | :--- |
| **address** | The address of your Bluzelle account |
| **mnemonic** | The mnemonic of the private key for your Bluzelle account |
| endpoint | \(Optional\) The hostname and port of your rest server. Default: http://localhost:1317 |
| uuid | \(Optional\) Bluzelle uses `UUID`'s to identify distinct databases on a single swarm. We recommend using [Version 4 of the universally unique identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_%28random%29). Defaults to the account address. |
| chain_id | \(Optional\) The chain id of your Bluzelle account. Default: bluzelle |

`bluzelle` and `version`are the only exports of the Bluzelle library. The calls below are methods of the object generated by calling `bluzelle`.

## General Functions

### account\()

Retrieve information about the currently active Bluzelle account.

```javascript
// promise syntax
api.account().then(() => { ... }, error => { ... });

// async/await syntax
await api.account();
```

Returns a promise resolving to a JSON object representing the account information, e.g.

```
{ address: 'bluzelle1lgpau85z0hueyz6rraqqnskzmcz4zuzkfeqls7',
  coins: [ { denom: 'bnt', amount: '9899567400' } ],
  public_key: 'bluzellepub1addwnpepqd63w08dcrleyukxs4kq0n7ngalgyjdnu7jpf5khjmpykskyph2vypv6wms',
  account_number: 3,
  sequence: 218 }
```

Throws an exception if a response is not received from the connection.



## Database Functions

### create\(key, value[, gas_info]\)

Create a field in the database.

```javascript
// promise syntax
api.create('mykey', '{ a: 13 }').then(() => { ... }, error => { ... });

// async/await syntax
await api.create('mykey', '{ a: 13 }');
```

| Argument | Description |
| :--- | :--- |
| key | The name of the key to create |
| value | The string value to set the key |
| gas_info (Optional) | Object containing gas parameters (see above) |

Returns a promise resolving to nothing.

Throws an exception when a response is not received from the connection, the key already exists, or invalid value.

### read\(key, prove\)

Retrieve the value of a key without consensus verification. Can optionally require the result to have a cryptographic proof (slower).

```javascript
// promise syntax
api.read('mykey').then(value => { ... }, error => { ... });

// async/await syntax
const value = await api.read('mykey');
```

| Argument | Description |
| :--- | :--- |
| key | The key to retrieve |
| prove | A proof of the value is required from the network (requires 'config trust-node false' to be set) |

Returns a promise resolving the string value of the key.

Throws an exception when the key does not exist in the database.
Throws an exception when the prove is true and the result fails verification.

### update\(key, value\[, gas_info]\)

Updates a field in the database.

```javascript
// promise syntax
api.update('mykey', '{ a: 13 }').then(() => { ... }, error => { ... });

// async/await syntax
await api.update('mykey', '{ a: 13 }');
```

| Argument | Description |
| :--- | :--- |
| key | The name of the key to create |
| value | The string value to set the key |
| gas_info (Optional) | Object containing gas parameters (see above) |

Returns a promise resolving to nothing.

Throws an exception when the key doesn't exist, or invalid value.

### delete\(key[, gas_info]\)

Deletes a field from the database.

```javascript
// promise syntax
api.delete('mykey').then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.delete('mykey');
```

| Argument | Description |
| :--- | :--- |
| key | The name of the key to delete |
| gas_info (Optional) | Object containing gas parameters (see above) |

Returns a promise resolving to nothing.

Throws an exception when the key is not in the database.

### has\(key\)

Query to see if a key is in the database. This function bypasses the consensus and cryptography mechanisms in favor of speed.


```javascript
// promise syntax
api.has('mykey').then(hasMyKey => { ... }, error => { ... });

// async/await syntax
const hasMyKey = await api.has('mykey');
```

| Argument | Description |
| :--- | :--- |
| key | The name of the key to query |

Returns a promise resolving to a boolean value - `true` or `false`, representing whether the key is in the database.

### keys\(\)

Retrieve a list of all keys. This function bypasses the consensus and cryptography mechanisms in favor of speed.

```javascript
// promise syntax
api.keys().then(keys => { ... }, error => { ... });

// async/await syntax
const keys = await api.keys();
```

Returns a promise resolving to an array of strings. ex. `["key1", "key2", ...]`.

### txread\(key[, gas_info]\)

Retrieve the value of a key via a transaction (i.e. uses consensus).

```javascript
// promise syntax
api.txread('mykey').then(value => { ... }, error => { ... });

// async/await syntax
const value = await api.txread('mykey');
```

| Argument | Description |
| :--- | :--- |
| key | The key to retrieve |
| gas_info (Optional) | Object containing gas parameters (see above) |

Returns a promise resolving the string value of the key.

Throws an exception when the key does not exist in the database.

### txhas\(key[, gas_info]\)

Query to see if a key is in the database via a transaction (i.e. uses consensus).

```javascript
// promise syntax
api.txhas('mykey').then(hasMyKey => { ... }, error => { ... });

// async/await syntax
const hasMyKey = await api.txhas('mykey');
```

| Argument | Description |
| :--- | :--- |
| key | The name of the key to query |
| gas_info (Optional) | Object containing gas parameters (see above) |

Returns a promise resolving to a boolean value - `true` or `false`, representing whether the key is in the database.

### txkeys\([gas_info]\)

Retrieve a list of all keys via a transaction (i.e. uses consensus).

```javascript
// promise syntax
api.txkeys().then(keys => { ... }, error => { ... });

// async/await syntax
const keys = await api.txkeys();
```

| Argument | Description |
| :--- | :--- |
| gas_info (Optional) | Object containing gas parameters (see above) |

Returns a promise resolving to an array of strings. ex. `["key1", "key2", ...]`.


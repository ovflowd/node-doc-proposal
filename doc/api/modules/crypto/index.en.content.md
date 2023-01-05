# Crypto

The `node:crypto` module provides cryptographic functionality that includes a
set of wrappers for OpenSSL's hash, HMAC, cipher, decipher, sign, and verify
functions.

```mjs
const { createHmac } = await import('node:crypto');

const secret = 'abcdefg';
const hash = createHmac('sha256', secret)
               .update('I love cupcakes')
               .digest('hex');
console.log(hash);
// Prints:
//   c0fa1bc00531bd78ef38c628449c5102aeabd49b5dc3a2a516ea6ea959d6658e
```

```cjs
const crypto = require('node:crypto');

const secret = 'abcdefg';
const hash = crypto.createHmac('sha256', secret)
                   .update('I love cupcakes')
                   .digest('hex');
console.log(hash);
// Prints:
//   c0fa1bc00531bd78ef38c628449c5102aeabd49b5dc3a2a516ea6ea959d6658e
```

## Determining if crypto support is unavailable

It is possible for Node.js to be built without including support for the
`node:crypto` module. In such cases, attempting to `import` from `crypto` or
calling `require('node:crypto')` will result in an error being thrown.

When using CommonJS, the error thrown can be caught using try/catch:

```cjs
let crypto;
try {
  crypto = require('node:crypto');
} catch (err) {
  console.error('crypto support is disabled!');
}
```

When using the lexical ESM `import` keyword, the error can only be
caught if a handler for `process.on('uncaughtException')` is registered
_before_ any attempt to load the module is made (using, for instance,
a preload module).

When using ESM, if there is a chance that the code may be run on a build
of Node.js where crypto support is not enabled, consider using the
[`import()`][] function instead of the lexical `import` keyword:

```mjs
let crypto;
try {
  crypto = await import('node:crypto');
} catch (err) {
  console.error('crypto support is disabled!');
}
```

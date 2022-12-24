## Class: `Blob`

A [`Blob`][] encapsulates immutable, raw data that can be safely shared across
multiple worker threads.

### `new buffer.Blob([sources[, options]])` {#api/modules/buffer/blob/constructor}

Creates a new `Blob` object containing a concatenation of the given sources.

{ArrayBuffer}, {TypedArray}, {DataView}, and {Buffer} sources are copied into
the 'Blob' and can therefore be safely modified after the 'Blob' is created.

String sources are encoded as UTF-8 byte sequences and copied into the Blob.
Unmatched surrogate pairs within each string part will be replaced by Unicode
U+FFFD replacement characters.

### `blob.arrayBuffer()`

<!-- YAML
added:
  - v15.7.0
  - v14.18.0
-->

* Returns: {Promise}

Returns a promise that fulfills with an {ArrayBuffer} containing a copy of
the `Blob` data.

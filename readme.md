# tiny-inflate

This is a port of Joergen Ibsen's [tiny inflate](https://bitbucket.org/jibsen/tinf) to JavaScript.
Minified it is about 3.7KB, or 1.5KB gzipped. While being very small, it is also reasonably fast
(about 30% - 50% slower than [pako](https://github.com/nodeca/pako) on average), and should be 
good enough for many applications. If you need the absolute best performance, however, you'll
need to use a larger library such as pako that contains additional optimizations.

## Installation

    npm install tiny-inflate

## Example

To use tiny-inflate, you only need a buffer of data compressed with `deflate`, `zlib`, or `gzip`.

If you have the decompressed size, you can allocate your output buffer ahead of time to save memory.
(Note that this is unecessary for GZIP data because `tiny-inflate` automatically detects the output
size from the header.)

Input and output buffers must be `Uint8Array`s. Since `Buffer`s are instances of `Uint8Array`s, you can 
also pass those in directly.

`tiny-inflate` will try to automatically detect what compression method the data is in, but in rare cases 
it fails. If that happens, you can use `inflate.gunzip()` for GZIP data (like `pako.ungzip()`), 
`inflate.inflate()` for deflated data (like `pako.inflateRaw()`), and `inflate.decompress()` for Zlib data 
(like `pako.inflate()`).


Example: decoding a Base64, GZIP string to the source string
```javascript
var inflate = require('tiny-inflate');

var compressedBuffer = Buffer.from('H4sIAAAAAAAAA/NIzcnJVyjPL8pJUQQAlRmFGwwAAAA=', 'base64');

var outputUint8Array = inflate(compressedBuffer); /* Can also use inflate.gunzip(compressedBuffer) */

var outputBuffer = Buffer.from(outputUint8Array);

console.log(outputBuffer.toString('utf8')); /* Hello world! */
```
Example: efficiently decoding a Base64, Zlib string with known uncompressed length
```javascript
var inflate = require('tiny-inflate');

var compressedBuffer = Buffer.from('eJzzSM3JyVcozy/KSVEEAB0JBF4=', 'base64');

var outputSize = 12; /* Assuming you know this previously... */

var outputArray = new Uint8Array(outputSize); /* Then you can create an efficient output space */

/* Output array that is passed in is mutated - no need to extract return value */ 
inflate(compressedBuffer, outputArray); /* Can also use inflate.inflate(compressedBuffer, outputArray) */

console.log(Buffer.from(outputArray).toString('utf8')); /* Hello world! */
```

## License

MIT

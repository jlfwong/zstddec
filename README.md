# zstddec

[![Latest NPM release](https://img.shields.io/npm/v/zstddec.svg)](https://www.npmjs.com/package/zstddec)
[![Minzipped size](https://badgen.net/bundlephobia/minzip/zstddec)](https://bundlephobia.com/result?p=zstddec)
[![Build Status](https://travis-ci.com/donmccurdy/zstddec.svg?branch=master)](https://travis-ci.com/donmccurdy/zstddec)

[ZSTD (Zstandard)](https://github.com/facebook/zstd) decoder for Web and Node.js, using WebAssembly.

## Installation

```shell
npm install --save zstddec
```

## API

```javascript
import { ZSTDecoder } from 'zstddec';

const decoder = new ZSTDDecoder();

await decoder.init();

const decompressedArray = decoder.decode( compressedArray, uncompressedSize );
```

> **Limitations:** The decoder may fail with the error `wasm function signature contains illegal type` when the `uncompressedSize` is not known in advance and given to the `decode()` method. This is presumably a bug in the WASM bindings, which I am not yet sure how to fix.

## Contributing

To build the project locally, run:

```
npm install
npm run dist
```

To test changes:

```
npm test
```

## Building from source

Compiled from https://github.com/facebook/zstd/tree/dev/contrib/single_file_libs, with the
following steps:

```shell
./combine.sh -r ../../lib -o zstddeclib.c zstddeclib-in.c
emcc zstddeclib.c -Oz -s EXPORTED_FUNCTIONS="['_ZSTD_decompress', '_ZSTD_findDecompressedSize', '_ZSTD_isError', '_malloc', '_free']" -s ALLOW_MEMORY_GROWTH=1 -s MALLOC=emmalloc -o zstddec.wasm
base64 zstddec.wasm > zstddec.txt
```

The base64 string written to `zstddec.txt` is embedded as the `wasm` variable at the bottom
of this file. The rest of this file is written by hand, in order to avoid an additional JS
wrapper generated by Emscripten.

## License

JavaScript wrapper is provided under the MIT License, and the WASM ZSTD decoder is provided under the BSD 3-Clause License.

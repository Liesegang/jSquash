# Cloudflare Worker Example

For this example, we will be using the [Cloudflare Worker](https://workers.cloudflare.com/) platform to upgrade images to WebP.

We can use the latest Wrangler CLI to run the example locally and deploy it to Cloudflare Workers.

## Running the example locally

1. Run `npm install`
2. Run `npm run start` to start the development server

## Usage of jSquash packages in Cloudflare Worker

One caveat is wrangler won't dynamically bundle the WASM modules with the worker.

You will need to ensure you configure the Worker to set these as global variables in the [wrangler.toml](wrangler.toml) file.
```
# wrangler.toml
[wasm_modules]
# Manually specify the path to the WASM module for each codec
JPEG_DEC_WASM = "node_modules/@jsquash/jpeg/codec/dec/mozjpeg_dec.wasm"
```

The `encode` and `decode` modules both export an `init` function that can be used to manually load the wasm module.

```js
import decode, { init as initJpegDecode } from '@jsquash/jpeg/decode';

const WASM_MODULE = // A WebAssembly.Module object of the compiled wasm binary
await initJpegDecode(WASM_MODULE);
const image = await fetch('./image.jpeg').then(res => res.arrayBuffer()).then(decode);
```
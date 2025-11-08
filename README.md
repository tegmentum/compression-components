# Compression Components

This repository serves as an index for WebAssembly Component Model implementations of compression algorithms. Each algorithm is maintained in its own repository.

## Available Compression Algorithms

### Brotli
**Repository**: [tegmentum/brotli-wasm](https://github.com/tegmentum/brotli-wasm)
**Interface**: `tegmentum:compression-algorithm/compression-provider`
**Best for**: HTTP compression, static assets, text and structured data
**Compression Ratio**: ★★★★★
**Speed**: ★★★☆☆

### LZ4
**Repository**: [tegmentum/lz4-wasm](https://github.com/tegmentum/lz4-wasm)
**Interface**: `tegmentum:compression-algorithm/compression-provider`
**Best for**: Real-time streaming, low latency requirements
**Compression Ratio**: ★★★☆☆
**Speed**: ★★★★★

### DEFLATE
**Repository**: [tegmentum/deflate-wasm](https://github.com/tegmentum/deflate-wasm)
**Interface**: `tegmentum:compression-algorithm/compression-provider`
**Best for**: ZIP files, PNG images, legacy compatibility
**Compression Ratio**: ★★★★☆
**Speed**: ★★★☆☆
**Status**: Planned

### Zstandard (ZSTD)
**Repository**: [tegmentum/zstd-wasm](https://github.com/tegmentum/zstd-wasm)
**Interface**: `tegmentum:compression-algorithm/compression-provider`
**Best for**: Balanced compression and speed
**Compression Ratio**: ★★★★☆
**Speed**: ★★★★☆
**Status**: Planned

## Unified CLI Tool

**Repository**: [tegmentum/wasm-compress-tools](https://github.com/tegmentum/wasm-compress-tools)

A unified command-line interface that can use any of the compression algorithms above:

```bash
# Compress with Brotli
compress --algo brotli < input.txt > output.br

# Compress with LZ4
compress --algo lz4 < input.txt > output.lz4

# Decompress (auto-detects algorithm)
decompress < input.br > output.txt
```

## Architecture

All compression components implement the same standard interface defined in WIT:

```wit
package tegmentum:compression-algorithm@0.1.0;

interface compression-provider {
    resource compressor {
        constructor(level: u8);
        compress: func(input: list<u8>) -> result<list<u8>, error-code>;
        compress-chunk: func(input: list<u8>, is-final: bool) -> result<list<u8>, error-code>;
        reset: func();
    }

    resource decompressor {
        constructor();
        decompress: func(input: list<u8>) -> result<list<u8>, error-code>;
        decompress-chunk: func(input: list<u8>, is-final: bool) -> result<list<u8>, error-code>;
        reset: func();
    }

    get-info: func() -> algorithm-info;
    get-performance-profile: func() -> performance-profile;
    get-capabilities: func() -> capabilities;
}
```

This allows components to be composed together and used interchangeably.

## Component Model Benefits

- **Composable**: Mix and match compression algorithms
- **Portable**: Run anywhere with wasmtime
- **Language-agnostic**: Use from any language with Component Model bindings
- **Standard interface**: All algorithms implement the same WIT interface
- **Future-proof**: Based on WebAssembly standards

## Usage in Your Project

### Using Individual Components

```bash
# Clone a specific algorithm
git clone https://github.com/tegmentum/brotli-wasm.git
cd brotli-wasm

# Build the component
cargo build --target wasm32-wasip2 --release

# Use it
wasmtime run --dir=. target/wasm32-wasip2/release/examples/cli.wasm compress input.txt
```

### Component Composition

```bash
# Compose components together
wasm-tools compose \
  your-app.wasm \
  -d brotli.wasm \
  -d lz4.wasm \
  -o composed.wasm
```

## Contributing

Each compression algorithm is maintained in its own repository. Please submit issues and pull requests to the specific algorithm repository.

For general questions or suggestions about the compression ecosystem, open an issue in this repository.

## License

Each component has its own license. See the individual repositories for details.

## References

- [WebAssembly Component Model](https://component-model.bytecodealliance.org/)
- [WIT Language Reference](https://component-model.bytecodealliance.org/design/wit.html)
- [WASI Preview 2](https://github.com/WebAssembly/WASI/tree/main/preview2)

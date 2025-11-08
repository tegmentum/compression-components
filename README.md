# Compression Components

This repository serves as an index for WebAssembly Component Model implementations of compression algorithms. Each algorithm is maintained in its own repository.

## Available Compression Algorithms

### ✅ Production Ready (With CLI Tools)

#### Brotli
**Repository**: [tegmentum/brotli-wasm](https://github.com/tegmentum/brotli-wasm)
**Best for**: HTTP compression, static assets, text and structured data
**Compression Ratio**: ★★★★★ | **Speed**: ★★★☆☆

#### LZ4
**Repository**: [tegmentum/lz4-wasm](https://github.com/tegmentum/lz4-wasm)
**Best for**: Real-time streaming, low latency requirements
**Compression Ratio**: ★★★☆☆ | **Speed**: ★★★★★

#### ZSTD (Zstandard)
**Repository**: [tegmentum/zstd-wasm](https://github.com/tegmentum/zstd-wasm)
**Best for**: Balanced compression and speed
**Compression Ratio**: ★★★★☆ | **Speed**: ★★★★☆

#### BZIP2
**Repository**: [tegmentum/bzip2-wasm](https://github.com/tegmentum/bzip2-wasm)
**Best for**: Archival, backup storage, text files
**Compression Ratio**: ★★★★☆ | **Speed**: ★★★☆☆

#### DEFLATE
**Repository**: [tegmentum/deflate-wasm](https://github.com/tegmentum/deflate-wasm)
**Best for**: ZIP files, PNG images, raw DEFLATE format
**Compression Ratio**: ★★★★☆ | **Speed**: ★★★☆☆

#### GZIP
**Repository**: [tegmentum/gzip-wasm](https://github.com/tegmentum/gzip-wasm)
**Best for**: .gz files, tar.gz archives, HTTP compression
**Compression Ratio**: ★★★★☆ | **Speed**: ★★★☆☆

#### Snappy
**Repository**: [tegmentum/snappy-wasm](https://github.com/tegmentum/snappy-wasm)
**Best for**: Databases (RocksDB, LevelDB), low-latency compression
**Compression Ratio**: ★★★☆☆ | **Speed**: ★★★★★

#### LZMA
**Repository**: [tegmentum/lzma-wasm](https://github.com/tegmentum/lzma-wasm)
**Best for**: 7-Zip archives, maximum compression
**Compression Ratio**: ★★★★★ | **Speed**: ★★☆☆☆

#### LZMA2
**Repository**: [tegmentum/lzma2-wasm](https://github.com/tegmentum/lzma2-wasm)
**Best for**: XZ archives, modern LZMA variant
**Compression Ratio**: ★★★★★ | **Speed**: ★★☆☆☆

#### XZ
**Repository**: [tegmentum/xz-wasm](https://github.com/tegmentum/xz-wasm)
**Best for**: .xz files, Linux package compression
**Compression Ratio**: ★★★★★ | **Speed**: ★★☆☆☆

#### LZ4HC
**Repository**: [tegmentum/lz4hc-wasm](https://github.com/tegmentum/lz4hc-wasm)
**Best for**: Better compression than LZ4, same fast decompression
**Compression Ratio**: ★★★★☆ | **Speed**: ★★★☆☆ (compress) / ★★★★★ (decompress)

#### Density
**Repository**: [tegmentum/density-wasm](https://github.com/tegmentum/density-wasm)
**Best for**: Extremely fast compression (GB/s), real-time streaming
**Compression Ratio**: ★★★☆☆ | **Speed**: ★★★★★
**Algorithms**: Chameleon (fastest), Cheetah (balanced), Lion (best compression)

#### LZO
**Repository**: [tegmentum/lzo-wasm](https://github.com/tegmentum/lzo-wasm)
**Best for**: Kernel usage, embedded systems, fast decompression
**Compression Ratio**: ★★★☆☆ | **Speed**: ★★★★☆ (compress) / ★★★★★ (decompress)

### 📦 Repository Created (Implementation Needed)

#### Zopfli
**Repository**: [tegmentum/zopfli-wasm](https://github.com/tegmentum/zopfli-wasm)
**Best for**: Maximum DEFLATE compression (slower encoding)
**Compression Ratio**: ★★★★★ | **Speed**: ★★☆☆☆

#### S2
**Status**: ❌ Not Implementable
**Reason**: No Rust implementation available (Go-only from Klaus Post)
**Alternative**: Use Snappy (backward compatible for decompression)

#### PPMd
**Status**: ❌ Not Implementable
**Reason**: Available Rust crate (`ppmd-core`) only supports file-based operations, lacks buffer API
**Alternative**: Use LZMA/LZMA2 for high-ratio text compression

#### Lizard
**Status**: ❌ Not Implementable
**Reason**: Obsolete algorithm, no Rust implementation, no longer maintained
**Alternative**: Use LZ4, LZ4HC, or Zstandard for better performance

#### Blosc
**Status**: ❌ Not Implementable
**Reason**: Requires C bindings, wraps algorithms already available (LZ4, Snappy, Zstd)
**Alternative**: Use the individual compression algorithms directly

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

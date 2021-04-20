[![Build Status](https://travis-ci.org/clearcodehq/gozstd.svg)](https://travis-ci.org/clearcodehq/gozstd)
[![GoDoc](https://godoc.org/github.com/clearcodehq/gozstd?status.svg)](http://godoc.org/github.com/clearcodehq/gozstd)
[![Go Report](https://goreportcard.com/badge/github.com/clearcodehq/gozstd)](https://goreportcard.com/report/github.com/clearcodehq/gozstd)
[![codecov](https://codecov.io/gh/clearcodehq/gozstd/branch/master/graph/badge.svg)](https://codecov.io/gh/clearcodehq/gozstd)

# gozstd - go wrapper for [zstd](http://facebook.github.io/zstd/)


## Features

  * Vendors upstream [zstd](https://github.com/facebook/zstd) without any modifications.
  * [Simple API](https://godoc.org/github.com/clearcodehq/gozstd).
  * Optimized for speed. The API may be easily used in zero allocations mode.
  * `Compress*` and `Decompress*` functions are optimized for high concurrency.
  * Proper [Writer.Flush](https://godoc.org/github.com/clearcodehq/gozstd#Writer.Flush)
    for network apps.
  * Supports the following features from upstream [zstd](https://facebook.github.io/zstd/):
      * Block / stream compression / decompression with all the supported compression levels
        and with dictionary support.
      * [Dictionary](https://github.com/facebook/zstd#the-case-for-small-data-compression)
        building from a sample set. The created dictionary may be saved to persistent storage /
	transfered over the network.
      * Dictionary loading for compression / decompression.
      
    Pull requests for missing upstream `zstd` features are welcome.

## Quick start


### How to install `gozstd`?

```
go get -u github.com/clearcodehq/gozstd
```

### How to compress data?

The easiest way is just to use [Compress](https://godoc.org/github.com/clearcodehq/gozstd#Compress):

```go
	compressedData := Compress(nil, data)
```

There is also [StreamCompress](https://godoc.org/github.com/clearcodehq/gozstd#StreamCompress)
and [Writer](https://godoc.org/github.com/clearcodehq/gozstd#Writer) for stream compression.

### How to decompress data?

The easiest way is just to use [Decompress](https://godoc.org/github.com/clearcodehq/gozstd#Decompress):

```go
	data, err := Decompress(nil, compressedData)
```

There is also [StreamDecompress](https://godoc.org/github.com/clearcodehq/gozstd#StreamDecompress)
and [Reader](https://godoc.org/github.com/clearcodehq/gozstd#Reader) for stream decompression.

### How to cross-compile gozstd?

If you're cross-compiling some code that uses gozstd and you stumble upon the following error:
```
# github.com/clearcodehq/gozstd
/go/pkg/mod/github.com/clearcodehq/gozstd@v1.6.2/stream.go:31:59: undefined: CDict
/go/pkg/mod/github.com/clearcodehq/gozstd@v1.6.2/stream.go:35:64: undefined: CDict
/go/pkg/mod/github.com/clearcodehq/gozstd@v1.6.2/stream.go:47:20: undefined: Writer
```

You can easily fix it by enabling [CGO](https://golang.org/cmd/cgo/) and using a cross-compiler (e.g. `arm-linux-gnueabi-gcc`):
```bash
env CC=arm-linux-gnueabi-gcc GOOS=linux GOARCH=arm CGO_ENABLED=1 go build ./main.go 
```

**NOTE**: Check [#21](https://github.com/clearcodehq/gozstd/issues/21) for more info.

### Who uses gozstd?

* [VictoriaMetrics](https://github.com/VictoriaMetrics/VictoriaMetrics)


## FAQ

  * Q: _Which go version is supported?_  
    A: `go1.10` and newer. Pull requests for older go versions are accepted.

  * Q: _Which platforms/architectures are supported?_  
    A: `linux/amd64`, `linux/arm`, `linux/arm64`, `freebsd/amd64`, `darwin/amd64`, `windows/amd64`. Pull requests for other platforms/architectures
       are accepted.
       
  * Q: _I don't trust `libzstd*.a` binary files from the repo or these files dont't work on my OS/ARCH. How to rebuild them?_
    A: Just run `make clean libzstd.a` if your OS/ARCH is supported.
    
  * Q: _How do I specify custom build flags when recompiling `libzstd*.a`?_
    A: You can specify MOREFLAGS=... variable when running `make` like this: `MOREFLAGS=-fPIC make clean libzstd.a`.

  * Q: _Why the repo contains `libzstd*.a` binary files?_  
    A: This simplifies package installation to usual `go get` without additional steps for building the `libzstd*.a`

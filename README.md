# afsctool

> AFSC (Apple File System Compression) tool is a utility that can be used
to apply HFS+ compression to file(s), decompress HFS+ compressed file(s), or
get information about existing HFS+ compressed file(s).
Mac OS 10.6 or later is required. See: https://brkirch.wordpress.com/afsctool/

Refer to [RJVB/afsctool](https://github.com/RJVB/afsctool) for details.

Currently 09/11/22, the original repo [RJVB/afsctool](https://github.com/RJVB/afsctool) is having some build issues, and the HomeBrew one doesn't come with `LZFSE` support.

So, my main modifications are:

- Make the compiler happy.
- Use GitHub Actions to release binaries **with `LZFSE` support**.

TODO:

- [ ] `arm64` support
- [ ] Fix build issues with `macosx12.0` SDK

## Installation

- Download binaries from [releases](https://github.com/charlie0129/afsctool/releases).
  - The artifacts have their target SDK and architecture in their names. Choose the one that fits your system.
  - Note that the target SDK version *don't* have to be exactly the same as your system version.
- Unpackage the gzipped package.
  - `tar zxf afsctool-xxx-xxx-xxx.tar.gz`
- Move the binary to somewhere in your PATH.
  - For example: `cp afsctool /usr/local/bin`

## Compile
### Install Dependencies

afsctool depends on zlib (v1.2.8 or newer) and Google's sparsehash library and on CMake
and pkgconfig for building. The OS zlib copy may be recent enough (it is on 10.12 and later) but to be
certain to obtain the latest versions of both, use a package manager like MacPorts, Fink
or HomeBrew. Be sure to follow the general installation and usage instructions for those
projects, in particular how to update your PATH.

using MacPorts:

```shell
port install sparsehash zlib cmake pkgconfig
```

using HomeBrew:

```shell
brew install google-sparsehash zlib cmake pkgconfig
PKG_CONFIG_PATH=/usr/local/opt/zlib/lib/pkgconfig
```

(Setting PKG_CONFIG_PATH is only required with HomeBrew.)

### Build

With the dependencies installed you can now build afsctool. In a directory of your choice:

```shell
git clone --recurse-submodules git://github.com/RJVB/afsctool
mkdir afsctool/build
cd afsctool/build
cmake -Wno-dev ..
make
```

This will leave the afsctool executable in `afsctool/build`; you can move it anywhere
you like from there. You can also do an "official" install, to /usr/local/bin by
default:
```shell
cd afsctool/build
sudo make install/fast V=1 VERBOSE=1
```

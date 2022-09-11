# afsctool

> AFSC (Apple File System Compression) tool is a utility that can be used
to apply HFS+ compression to file(s), decompress HFS+ compressed file(s), or
get information about existing HFS+ compressed file(s).
Mac OS 10.6 or later is required. See: https://brkirch.wordpress.com/afsctool/

This is a fork of [RJVB/afsctool](https://github.com/RJVB/afsctool).

Currently 09/11/22, the original repo [RJVB/afsctool](https://github.com/RJVB/afsctool) is having some build issues, and the HomeBrew one doesn't come with `LZFSE` support.

So, my main modifications are:

- Make the compiler happy.
- Use GitHub Actions to release binaries **with `LZFSE` support**.

## Installation

- Download binaries from [releases](https://github.com/charlie0129/afsctool/releases).
  - The artifacts have their target SDK and architecture in their names. Choose the one that fits your system.
  - Note that the target SDK version *don't* have to be exactly the same as your system version.
- Unpackage the gzipped package.
  - `tar zxf afsctool-xxx-xxx-xxx.tar.gz`
- Move the binary to somewhere in your PATH.
  - For example: `cp afsctool /usr/local/bin`

## Examples

`afsctool` can use the filesystem API to do transparent compression. Decompression is done by the filesystem on-the-fly, so you can save disk space while everything works just like before. In fact, Apple already does transparent compression internally (e.g. apps downloaded from the App Store and some system application). So, this is something Apple is already doing behind the scene.

However, unlike ZFS compression, Apple's implementation of filesystem compression doesn't automatically do compression on newly added files. **Thus, you should only apply this to files that do not or rarely change.** Application files are the prime candidate for this.

Let's say we want to compress `IntelliJ IDEA`.

1. Compress `IntelliJ IDEA.app`

   - Run `afsctool -c -J6 -T LZFSE /Applications/IntelliJ\ IDEA.app`
   - `-c`: apply compression
   - `-J6`: compress using 6 threads to speed up the process. You can adjust how many threads you want by yourself.
   - `-T LZFSE`: use `LZFSE` compressor. `LZFSE` is a fast and efficient compression method. That's why I created this repo and release binaries with `LZFSE` support (while the `afsctool` from HomeBrew doesn't have `LZFSE` built-in).

2. Check the results

   - `afsctool -v /Applications/IntelliJ\ IDEA.app`

     ```
     /Applications/IntelliJ IDEA.app/:
     Number of HFS+/APFS compressed files: 3189
     Total number of files: 3439
     Total number of folders: 1118
     Total number of items (number of files + number of folders): 4557
     Folder size (uncompressed; reported size by Mac OS 10.6+ Finder): 2891214455 bytes / 2.9 GB (gigabytes, base-10)
     Folder size (compressed): 1011724848 bytes / 965.7 MiB
     Compression savings: 65.0% over 3189 of 3439 files
     Approximate total folder size (files + file overhead + folder overhead): 1013166648 bytes / 966.2 MiB
     ```

   - As we can see, we reduced IDEA to only **35%** of its original size: `2.9 GB` -> `965.7 MiB`.

3. Then you can forget about it. Everything will work just like before.

## Compiling Guide

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

## TODO:

- [ ] `arm64` support
- [ ] Fix build issues with `macosx12.0` SDK

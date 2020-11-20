# Turtl core-rs
<a href="https://circleci.com/gh/turtl/core-rs"><img src="https://circleci.com/gh/turtl/core-rs.svg?style=shield&circle-token=:circle-token"></a> (the failing New York Test)

_Opening an issue? See the [Turtl project tracker](https://github.com/turtl/project-tracker/issues)_

This is the Rust core for Turtl. It houses the logic for Turtl's main client
operations and is meant to be embedded as a shared/static library that is
standard across all platforms. The idea is, if it *can* go in the core, it
*should* go in the core. Pretty much everything except UI goes here:

- User join/login/deletion
- Talking to the server/syncing data
- Encryption/Decryption of data
- In-memory storage of profile data
- Permissions checking
- Searching of notes
- Sharing/Collaboration handling
- Local storage
- Bookmark handling

When building a UI (Android/iOS/Desktop/etc etc) you should have to worry about
two things: loading/talking to the core and building the interface around the
core. All logic (syncing/crypto/storage) lives in the core.

Although the core project is posted, the new server it talks to (NodeJS/Postgres)
is not yet public (yes, a fond farewell to Lisp). Stay tuned!

This project is unfinished and *alpha* status. I won't be responding to issues
or bug reports on it yet. Use at your own risk.

## Building the core

Let's go over how to build the Turtl core.

### Prerequisites

- Rust >= 1.27.0
- OpenSSL 1.0.x (shared or static)
- Libsodium 1.0.16 (static preferred)

### Compiling

```bash
make
```

The `Makefile` includes (optionally) a non-versioned file called `vars.mk` which
can be used to house any ENV vars specific to your build environment:

```makefile
# Example vars.mk
export PATH := $(PATH):/your/custom/path
export RUSTFLAGS := -L/opt/lib64 -lmylib
export SODIUM_LIB_DIR := /opt/libsodium/lib
export SODIUM_STATIC := static
export OPENSSL_LIB_DIR=/usr/lib/openssl-1.0
export OPENSSL_INCLUDE_DIR=/usr/include/openssl-1.0
```

NOTE: If your system uses OpenSSL 1.1.0, you need to install OpenSSL 1.0.0 and
tell `make` to use it with `OPENSSL_LIB_DIR=/usr/lib/openssl-1.0 OPENSSL_INCLUDE_DIR=/usr/include/openssl-1.0 make`
for example. This would be a good place to use `vars.mk`.

NOTE 2: If your system has libsodium version different than 1.0.16 and your build fails, do this:

```bash
cargo build
wget https://download.libsodium.org/libsodium/releases/old/libsodium-1.0.16.tar.gz
tar xzf libsodium-1.0.16.tar.gz
cd libsodium-1.0.16
./configure
make
cp -r src/libsodium/.libs/* ../target/debug/deps/
cargo build
```
After every `cargo clean` or `make clean` you should do the last command

## Using

This section is a work in progress. To use the Turtl core embedded library,
you'll have to know two things:

- How to interface: [see turtl_core.h](https://github.com/turtl/core-rs/blob/master/include/turtl_core.h)
- How to use: [see dispatch.rs](https://github.com/turtl/core-rs/blob/master/src/dispatch.rs)
for a list of commands you can send the core, and also check the [integration tests](https://github.com/turtl/core-rs/tree/master/integration-tests/tests)
for example usage. The integration tests strive for complete coverage of all the
publicly-exported capabilities of the core library, so they should help somewhat
with documentation (I know it's not a substitute, but I'd rather release the
project then toil away on docs than the other way around).

## Notes

This is a haphazard collection of random notes with no real organization. Sorry.

- If you want to build the core for linux or android, see [buildo](https://github.com/turtl/buildo).
- When building turtl core for iOS, you *need* to use a libsodium.a that was
compiled *without* `--enable-minimal`!! This is worth noting because `libsodium/src/dist-build/ios.sh`
uses `--enable-minimal` when building...remove these directives before building
libsodium!

## Projects using the core

- https://github.com/toschoch/python-turtlpy  
A Python client for Turtl!


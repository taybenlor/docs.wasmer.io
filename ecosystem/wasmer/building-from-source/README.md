---
id: runtime-cli-building-from-source
title: Runtime Command Line Building from Source
sidebar_label: Building from Source
---

# Building from Source

## Installing Rustup

Building Wasmer from source requires [Rust](https://rustup.rs/).

The easiest way to install Rust on your system is via Rustup. To get Rustup on Linux and macOS, you can run the following:

```bash
curl https://sh.rustup.rs -sSf | sh
```

{% hint style="info" %}
To install Rust on Windows, download and run [rustup-init.exe](https://win.rustup.rs/), then follow the onscreen instructions.
{% endhint %}

## Installing Additional Dependencies

### MacOS

If you have [Homebrew](https://brew.sh/) installed:

```text
brew install cmake
```

Or, if you have [MacPorts](https://www.macports.org/install.php):

```bash
sudo port install cmake
```

### Debian-based

```bash
sudo apt install cmake pkg-config libssl-dev
```

### FreeBSD

```text
pkg install cmake
```

### Windows

Windows support is _experimental_. WASI is fully supported, but Emscripten support is in the works.

1. Install [Visual Studio](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)
2. Install [Rust for Windows](https://win.rustup.rs/)
3. Install [Git for Windows](https://git-scm.com/download/win). Allow it to add `git.exe` to your PATH \(default settings for the installer are fine\).
4. Install [CMake](https://cmake.org/download/). Ensure CMake is in your PATH.
5. \(optional\) Install [LLVM 8.0](https://prereleases.llvm.org/win-snapshots/LLVM-8.0.0-r351033-win64.exe)

## Building the Wasmer Runtime

Wasmer is built with [Cargo](https://crates.io/), the Rust package manager.

First, let's clone Wasmer:

```text
git clone https://github.com/wasmerio/wasmer.git
cd wasmer
```

Wasmer has three different backends at the moment:

### Singlepass Backend

The Singlepass backend requires nightly, so if you want to use it, set Rust Nightly:

```bash
rustup default nightly
```

And then, build Wasmer:

```text
make release
```

**Note**: you should see this as the first line in the console:  
`Available backends: singlepass`

{% hint style="info" %}
The singlepass backend requires Rust nightly, as it's using the [Dynasm crate](https://github.com/CensoredUsername/dynasm-rs) which depends on Rust features only available in Rust nightly
{% endhint %}

### Cranelift Backend

The Cranelift backend will work with both nightly and stable versions of Rust if you are in a X86 machine, so you don't need to do anything in your system to enable it.  
In ARM systems is disabled by default \(as is not yet available there\)

```text
make release
```

**Note**: should see this as the first line in the console:  
`Available backends: cranelift`

### LLVM Backend

If you want support for the Wasmer LLVM backend, then you will also need to ensure:

* Ensure that LLVM 8.0.x &gt; is installed on your system
  * You can also [download and use a prebuilt LLVM binary](https://releases.llvm.org/download.html)
* In case `llvm-config` is not accessible, set the correct environment variable for LLVM to access: For example, the environment variable for LLVM 8.0.x would be: `LLVM_SYS_80_PREFIX=/path/to/unpacked/llvm-8.0` 

And create a Wasmer release

```bash
make release
```

**Note**: you should see this as the first line in the console:  
`Available backends: llvm`

### All backends

Once you have LLVM and Rust nightly installed, you can just run:

```bash
make release
```

**Note**: you should see this as the first line in the console:  
`Available backends: singlepass cranelift llvm`

{% hint style="info" %}
For compiling with all backends, you will need to set the [`nightly` toolchain as the default for Rust](./#singlepass-backend) \(for Singlepass\) and you will also need [LLVM installed in your system](./#llvm-backend) \(LLVM\)
{% endhint %}

## Running your Wasmer binary

Once you run the `make release` command, you will have a new binary ready to be used!

```text
./target/release/wasmer quickjs.wasm
```

## Building Wasmer C-API from source

Wasmer provides a pre-compiled version for the C-API on its [release page](https://github.com/wasmerio/wasmer/releases).

However, you can also compile the shared library from source:

```text
make capi
```

This will generate the shared library \(depending on your system\):

* Windows: `target/release/wasmer_runtime_c_api.dll`
* macOS: `target/release/libwasmer_runtime_c_api.dylib`
* Linux: `target/release/libwasmer_runtime_c_api.so`

{% hint style="warning" %}
By default, the Wasmer C API shared library is built with Cranelift as the _default_ backend.  
You can generate the C-API for a specific backend with:

* **Singlepass**: `make capi-singlepass`
* **Cranelift**: `make capi-cranelift`
* **LLVM**: `make capi-llvm`
{% endhint %}

If you want to generate the library and headers for using them easily, you can execute:

```bash
make build-capi-package
```

This command will generate a `capi` directory, that you can then use easily in the [Wasmer C API examples](./).


# linjector-rs

export ANDROID_NDK_HOME=/home/xx/android/ndk/26.1.10909125
export PATH=$PATH:${ANDROID_NDK_HOME}/toolchains/llvm/prebuilt/linux-x86_64/bin

rustup target add \
        aarch64-linux-android \
        armv7-linux-androideabi \
        i686-linux-android \
        x86_64-linux-android

rustup toolchain install nightly

rustup target add --toolchain nightly \
        aarch64-linux-android \
        armv7-linux-androideabi \
        i686-linux-android \
        x86_64-linux-android

vim ~/.cargo/config

[target.aarch64-linux-android]
linker = "/home/xx/android/ndk/26.1.10909125/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android30-clang"

[target.armv7-linux-androideabi]
linker = "/root/Android/Sdk/ndk/26.1.10909125/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi30-clang"

[target.i686-linux-android]
linker = "/root/Android/Sdk/ndk/26.1.10909125/toolchains/llvm/prebuilt/linux-x86_64/bin/i686-linux-android30-clang"

[target.x86_64-linux-android]
linker = "/root/Android/Sdk/ndk/26.1.10909125/toolchains/llvm/prebuilt/linux-x86_64/bin/x86_64-linux-android30-clang"

cargo build --target aarch64-linux-android --release --no-default-features

Android port of [linux_injector](https://github.com/namazso/linux_injector). Library injection using /proc/mem, without ptrace. Only aarch64 is supported.

To get an idea of how it works, you can read the [blog post](https://erfur.github.io/blog/dev/code-injection-without-ptrace).

## Usage

```
Inject code into a running process using /proc/mem

Usage: linjector-cli [OPTIONS] --file <FILE>

Options:
  -p, --pid <PID>
          pid of the target process

  -a, --app-package-name <APP_PACKAGE_NAME>
          target application's package name, (re)start the application and do injection

  -f, --file <FILE>
          path of the library/shellcode to inject

  -i, --injection-type <INJECTION_TYPE>
          type of injection
          
          [default: raw-dlopen]

          Possible values:
          - raw-dlopen:    Use dlopen to inject a library
          - memfd-dlopen:  Use memfd_create and dlopen to inject a library
          - raw-shellcode: Inject raw shellcode

      --func-sym <FUNC_SYM>
          function to hijack for injection, in the form "lib.so!symbol_name"

      --var-sym <VAR_SYM>
          variable to hijack for injection, in the form "lib.so!symbol_name"

  -d, --debug
          enable debug logs

      --logcat
          print logs to logcat

  -h, --help
          Print help (see a summary with '-h')

  -V, --version
          Print version
```

## Modes

**Currently only raw dlopen mode works**. Since SELinux doesn't allow calling dlopen on a memfd, memfd dlopen will not work. Shellcode mode is not yet implemented.


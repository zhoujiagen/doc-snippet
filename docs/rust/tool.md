# Rust Tools

## 资源

- [rust-lang/rustup](https://github.com/rust-lang/rustup)
- [Edition Guide](https://doc.rust-lang.org/edition-guide/index.html): Guide to the Rust editions.
- [cargo Book](https://doc.rust-lang.org/cargo/index.html): A book on Rust’s package manager and build system.
- [rustdoc Book](https://doc.rust-lang.org/rustdoc/index.html): Learn how to make awesome documentation for your crate.
- [rustc Book](https://doc.rust-lang.org/rustc/index.html): Familiarize yourself with the knobs available in the Rust compiler.
- [Compiler Error Index](https://doc.rust-lang.org/error-index.html): In-depth explanations of the errors you may see from the Rust compiler.
- [crates.io](https://crates.io/): The Rust community’s crate registry
- [TOML](https://github.com/toml-lang/toml): Tom's Obvious, Minimal Language.

## rustup

> a tool for managing Rust installations

```
$ rustup --version
rustup 1.22.1 (b01adbbc3 2020-07-08)
```

### Usage

```
$ rustup --help
rustup 1.22.1 (b01adbbc3 2020-07-08)
The Rust toolchain installer

USAGE:
    rustup [FLAGS] [+toolchain] <SUBCOMMAND>

FLAGS:
    -v, --verbose    Enable verbose output
    -q, --quiet      Disable progress output
    -h, --help       Prints help information
    -V, --version    Prints version information

ARGS:
    <+toolchain>    release channel (e.g. +stable) or custom toolchain to set override

SUBCOMMANDS:
    show           Show the active and installed toolchains or profiles
    update         Update Rust toolchains and rustup
    check          Check for updates to Rust toolchains
    default        Set the default toolchain
    toolchain      Modify or query the installed toolchains
    target         Modify a toolchain's supported targets
    component      Modify a toolchain's installed components
    override       Modify directory toolchain overrides
    run            Run a command with an environment configured for a given toolchain
    which          Display which binary will be run for a given command
    doc            Open the documentation for the current toolchain
    man            View the man page for a given command
    self           Modify the rustup installation
    set            Alter rustup settings
    completions    Generate tab-completion scripts for your shell
    help           Prints this message or the help of the given subcommand(s)

DISCUSSION:
    rustup installs The Rust Programming Language from the official
    release channels, enabling you to easily switch between stable,
    beta, and nightly compilers and keep them updated. It makes
    cross-compiling simpler with binary builds of the standard library
    for common platforms.

    If you are new to Rust consider running `rustup doc --book` to
    learn Rust.
```


## cargo

> Rust’s compilation manager, package manager, and general-purpose tool

```
$ cargo --version
cargo 1.45.0 (744bd1fbb 2020-06-15)
```

### Usage


```
$ cargo --help
Rust's package manager

USAGE:
    cargo [OPTIONS] [SUBCOMMAND]

OPTIONS:
    -V, --version           Print version info and exit
        --list              List installed commands
        --explain <CODE>    Run `rustc --explain CODE`
    -v, --verbose           Use verbose output (-vv very verbose/build.rs output)
    -q, --quiet             No output printed to stdout
        --color <WHEN>      Coloring: auto, always, never
        --frozen            Require Cargo.lock and cache are up to date
        --locked            Require Cargo.lock is up to date
        --offline           Run without accessing the network
    -Z <FLAG>...            Unstable (nightly-only) flags to Cargo, see 'cargo -Z help' for details
    -h, --help              Prints help information

Some common cargo commands are (see all commands with --list):
    build       Compile the current package
    check       Analyze the current package and report errors, but don't build object files
    clean       Remove the target directory
    doc         Build this package's and its dependencies' documentation
    new         Create a new cargo package
    init        Create a new cargo package in an existing directory
    run         Run a binary or example of the local package
    test        Run the tests
    bench       Run the benchmarks
    update      Update dependencies listed in Cargo.lock
    search      Search registry for crates
    publish     Package and upload this package to the registry
    install     Install a Rust binary. Default location is $HOME/.cargo/bin
    uninstall   Uninstall a Rust binary

See 'cargo help <command>' for more information on a specific command.
```

```
# 创建项目
cargo new --bin hello
# 运行程序
cargo run
# 执行测试
cargo test
# 清理
cargo clean
```


## rustc

> the Rust compiler

```
$ rustc --version
rustc 1.45.0 (5c1f21c3b 2020-07-13)
```

### Usage

```
$ rustc --help
Usage: rustc [OPTIONS] INPUT

Options:
    -h, --help          Display this message
        --cfg SPEC      Configure the compilation environment
    -L [KIND=]PATH      Add a directory to the library search path. The
                        optional KIND can be one of dependency, crate, native,
                        framework, or all (the default).
    -l [KIND=]NAME      Link the generated crate(s) to the specified native
                        library NAME. The optional KIND can be one of
                        static, framework, or dylib (the default).
        --crate-type [bin|lib|rlib|dylib|cdylib|staticlib|proc-macro]
                        Comma separated list of types of crates
                        for the compiler to emit
        --crate-name NAME
                        Specify the name of the crate being built
        --edition 2015|2018
                        Specify which edition of the compiler to use when
                        compiling code.
        --emit [asm|llvm-bc|llvm-ir|obj|metadata|link|dep-info|mir]
                        Comma separated list of types of output for the
                        compiler to emit
        --print [crate-name|file-names|sysroot|target-libdir|cfg|target-list|target-cpus|target-features|relocation-models|code-models|tls-models|target-spec-json|native-static-libs]
                        Compiler information to print on stdout
    -g                  Equivalent to -C debuginfo=2
    -O                  Equivalent to -C opt-level=2
    -o FILENAME         Write output to <filename>
        --out-dir DIR   Write output to compiler-chosen filename in <dir>
        --explain OPT   Provide a detailed explanation of an error message
        --test          Build a test harness
        --target TARGET Target triple for which the code is compiled
    -W, --warn OPT      Set lint warnings
    -A, --allow OPT     Set lint allowed
    -D, --deny OPT      Set lint denied
    -F, --forbid OPT    Set lint forbidden
        --cap-lints LEVEL
                        Set the most restrictive lint level. More restrictive
                        lints are capped at this level
    -C, --codegen OPT[=VALUE]
                        Set a codegen option
    -V, --version       Print version info and exit
    -v, --verbose       Use verbose output

Additional help:
    -C help             Print codegen options
    -W help             Print 'lint' options and default settings
    --help -v           Print the full set of options rustc accepts
```


## rustdoc

> the Rust documentation tool

```
$ rustdoc --version
rustdoc 1.45.0 (5c1f21c3b 2020-07-13)
```

### Usage

```
$ rustdoc --help
rustdoc [options] <input>

Options:
    -h, --help          show this help message
    -V, --version       print rustdoc's version
    -v, --verbose       use verbose output
    -r, --input-format [rust]
                        the input type of the specified file
    -w, --output-format [html]
                        the output type to write
    -o, --output PATH   where to place the output
        --crate-name NAME
                        specify the name of this crate
        --crate-type [bin|lib|rlib|dylib|cdylib|staticlib|proc-macro]
                        Comma separated list of types of crates
                        for the compiler to emit
    -L, --library-path DIR
                        directory to add to crate search path
        --cfg           pass a --cfg to rustc
        --extern NAME[=PATH]
                        pass an --extern to rustc
        --extern-html-root-url NAME=URL
                        base URL to use for dependencies
        --plugin-path DIR
                        removed
    -C, --codegen OPT[=VALUE]
                        pass a codegen option to rustc
        --passes PASSES list of passes to also run, you might want to pass it
                        multiple times; a value of `list` will print available
                        passes
        --plugins PLUGINS
                        removed
        --no-defaults   don't run the default passes
        --document-private-items
                        document private items
        --document-hidden-items
                        document items that have doc(hidden)
        --test          run code examples as tests
        --test-args ARGS
                        arguments to pass to the test runner
        --target TRIPLE target triple to document
        --markdown-css FILES
                        CSS files to include via <link> in a rendered Markdown
                        file
        --html-in-header FILES
                        files to include inline in the <head> section of a
                        rendered Markdown file or generated documentation
        --html-before-content FILES
                        files to include inline between <body> and the content
                        of a rendered Markdown file or generated documentation
        --html-after-content FILES
                        files to include inline between the content and
                        </body> of a rendered Markdown file or generated
                        documentation
        --markdown-before-content FILES
                        files to include inline between <body> and the content
                        of a rendered Markdown file or generated documentation
        --markdown-after-content FILES
                        files to include inline between the content and
                        </body> of a rendered Markdown file or generated
                        documentation
        --markdown-playground-url URL
                        URL to send code snippets to
        --markdown-no-toc
                        don't include table of contents
    -e, --extend-css PATH
                        To add some CSS rules with a given file to generate
                        doc with your own theme. However, your theme might
                        break if the rustdoc's generated HTML changes, so be
                        careful!
    -Z FLAG             internal and debugging options (only on nightly build)
        --sysroot PATH  Override the system root
        --playground-url URL
                        URL to send code snippets to, may be reset by
                        --markdown-playground-url or
                        `#![doc(html_playground_url=...)]`
        --display-warnings
                        to print code warnings when testing doc
        --crate-version VERSION
                        crate version to print into documentation
        --sort-modules-by-appearance
                        sort modules by where they appear in the program,
                        rather than alphabetically
        --theme FILES   additional themes which will be added to the generated
                        docs
        --check-theme FILES
                        check if given theme is valid
        --resource-suffix PATH
                        suffix to add to CSS and JavaScript files, e.g.,
                        "light.css" will become "light-suffix.css"
        --edition EDITION
                        edition to use when compiling rust code (default:
                        2015)
        --color auto|always|never
                        Configure coloring of output:
                        auto = colorize, if output goes to a tty (default);
                        always = always colorize output;
                        never = never colorize output
        --error-format human|json|short
                        How errors and other messages are produced
        --json CONFIG   Configure the structure of JSON diagnostics
        --disable-minification
                        Disable minification applied on JS files
    -W, --warn OPT      Set lint warnings
    -A, --allow OPT     Set lint allowed
    -D, --deny OPT      Set lint denied
    -F, --forbid OPT    Set lint forbidden
        --cap-lints LEVEL
                        Set the most restrictive lint level. More restrictive
                        lints are capped at this level. By default, it is at
                        `forbid` level.
        --index-page PATH
                        Markdown file to be used as index page
        --enable-index-page
                        To enable generation of the index page
        --static-root-path PATH
                        Path string to force loading static files from in
                        output pages. If not set, uses combinations of '../'
                        to reach the documentation root.
        --disable-per-crate-search
                        disables generating the crate selector on the search
                        box
        --persist-doctests PATH
                        Directory to persist doctest executables into
        --generate-redirect-pages
                        Generate extra pages to support legacy URLs and tool
                        links
        --show-coverage
                        calculate percentage of public items with
                        documentation
        --enable-per-target-ignores
                        parse ignore-foo for ignoring doctests on a per-target
                        basis
        --runtool The tool to run tests with when building for a different target than host

        --runtool-arg One (of possibly many) arguments to pass to the runtool

        --test-builder  specified the rustc-like binary to use as the test
                        builder
```


```
# 生成项目文档
#cargo doc --no-deps --open
cargo doc --no-deps
```

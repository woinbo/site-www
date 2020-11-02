---
title: dart compile
description: Command-line tool for AOT-compiling Dart source code.
---

Use the `dart compile` command
(which replaces `dart2native` and `dart2js` [PENDING: check])
to AOT (ahead-of-time) compile
a Dart program to a [target platform](/platforms).
Here's an example of compiling a Dart command-line program
into a self-contained executable:

```terminal
$ dart compile exe bin/main.dart  # [PENDING: check!!]
```

{{site.alert.note}}
  To execute Dart code without AOT compiling it,
  use [`dart run`](/tools/dart-run).
{{site.alert.end}}

The following table shows supported outputs of `dart compile`.

|----------------+---------------------------+-----------------------------------|
| Subcommand     | Output                    | More information                  |
|----------------|---------------------------|-----------------------------------|
| `aot-snapshot` | AOT snapshot              | Run this using [`dartaotruntime`][]. [More info.](#aot) |
| `exe`          | Self-contained executable | Includes a Dart runtime. [More info.](#exe)                |
| `jit-snapshot` | JIT snapshot              | [PENDING: what is this?]          |
| `js`           | JavaScript                | [PENDING: describe]               |
| `kernel`       | Kernel snapshot           | [PENDING: what is this?]          |
{:.table .table-striped .nowrap}

[`dartaotruntime`]: /tools/dartaotruntime

For usage information about `dart compile`, run `dart compile [<subcommand>] --help`:

```terminal
$ dart compile exe --help
```

* A standalone executable for Windows, macOS, or Linux.
  A **standalone executable** is native machine code that's compiled from
  the specified Dart file and its dependencies,
  plus a small Dart runtime that handles
  type checking and garbage collection.

An **AOT snapshot** doesn't include the Dart runtime.
Consider using snapshots if you're distributing multiple programs
and disk space is limited.


## Creating standalone executables

{% comment %}
$ dart compile exe --help
Compile Dart to a self-contained executable.

Usage: dart compile exe [arguments] <dart entry point>
-h, --help                          Print this usage information.
-o, --output                        Write the output to <file name>.
                                    This can be an absolute or relative path.
-D, --define=<key=value>            Define an environment declaration. To specify multiple
                                    declarations, use multiple options or use commas to
                                    separate key-value pairs.
                                    For example: dart compile exe -Da=1,b=2 main.dart
    --enable-asserts                Enable assert statements.
-p, --packages=<path>               Get package locations from the specified file instead
                                    of .packages.
                                    <path> can be relative or absolute.
                                    For example: dart compile exe --packages=/tmp/pkgs
                                    main.dart
-S, --save-debugging-info=<path>    Remove debugging information from the output and save
                                    it separately to the specified file.
                                    <path> can be relative or absolute.
{% endcomment %}

Here's an example of using `dart compile exe` to create a standalone executable:

```terminal
$ dart compile bin/main.dart -o bin/my_app
```

You can distribute and run that executable like you would
any other executable file:

```terminal
$ cp bin/my_app .
$ ./my_app
```


## Creating AOT snapshots

{% comment %}
$ dart compile aot-snapshot --help
Compile Dart to an AOT snapshot.

Usage: dart compile aot-snapshot [arguments] <dart entry point>
-h, --help                          Print this usage information.
-o, --output                        Write the output to <file name>.
                                    This can be an absolute or relative path.
-D, --define=<key=value>            Define an environment declaration. To specify multiple
                                    declarations, use multiple options or use commas to
                                    separate key-value pairs.
                                    For example: dart compile aot-snapshot -Da=1,b=2
                                    main.dart
    --enable-asserts                Enable assert statements.
-p, --packages=<path>               Get package locations from the specified file instead
                                    of .packages.
                                    <path> can be relative or absolute.
                                    For example: dart compile aot-snapshot
                                    --packages=/tmp/pkgs main.dart
-S, --save-debugging-info=<path>    Remove debugging information from the output and save
                                    it separately to the specified file.
                                    <path> can be relative or absolute.
{% endcomment %}

To create an AOT snapshot, add `-k aot` to the command:

```terminal
$ dart2native bin/main.dart -k aot
```

You can then run the app using the [`dartaotruntime` command][]:

```terminal
$ dartaotruntime bin/main.aot
```

## Known limitations

The `dart compile` command has some known limitations:

No cross-compilation support ([issue 28617][])
: The compiler supports creating machine code only for
  the operating system it’s running on.
  You need to run the compiler three times —
  on macOS, Windows, and Linux —
  to create executables for all three operating systems.
  A workaround is to use a CI (continuous integration) provider
  that supports all three operating systems.

No signing support ([issue 39106][])
: The format of the executables isn’t compatible with
  standard signing tools such as codesign and SignTool.

No support for dart:mirrors and dart:developer
: Code compiled with the `aot` or `exe` subcommand
  can use all of the other libraries
  that the Dart VM supports.
  [PENDING: get into other platforms?]
  For a complete list of the core libraries you can use,
  see the **All** and **AOT** entries in the
  [table of core Dart libraries](/guides/libraries).

[issue 28617]: https://github.com/dart-lang/sdk/issues/28617
[issue 39106]: https://github.com/dart-lang/sdk/issues/39106

{{site.alert.tip}}
  If one of these issues is important to you,
  let the Dart team know by adding a "thumbs up" to the issue.
{{site.alert.end}}


## Options

The first argument to `dart2native` is the path to the main Dart file:

```none
dart2native <main-dart-file> [<options>]
```

You can use the following options:

`-D <key>=<value>` or `--define=<key>=<value>`
: Defines an environment declaration,
  which can be read using the [`String.fromEnvironment()` constructor][].
  To specify multiple declarations, use multiple options or
  use commas to separate key-value pairs.

`--enable-asserts`
: Enables [assert statements][].

`-h` or `--help`
: Displays help for all options.

`-k (aot|exe)` or `--output-kind=(aot|exe)`
: Specifies the output type, where `exe` is the default
  (a standalone executable). To generate an AOT snapshot,
  use `-k aot`.

`-o <path>` or `--output=<path>`
: Generates the output into `<path>`. If you don't use this option,
  the output goes into a file next to the main Dart file.
  Standalone executables have the suffix `.exe`, by default;
  the AOT snapshot suffix is `.aot`.

`-p <path>` or `--packages=<path>`
: Specifies the path to the package resolution configuration file.
  For more information, see
  [Package Resolution Configuration File.](https://github.com/lrhn/dep-pkgspec/blob/master/DEP-pkgspec.md)

`-v` or `--verbose`
: Displays more information.

## dart2aot

Releases before Dart 2.6 contained
a tool named `dart2aot` that produced AOT snapshots.
The `dart2native` command replaces `dart2aot` and
has a superset of the `dart2aot` functionality.


[assert statements]: /guides/language/language-tour#assert
[`dartaotruntime` command]: /tools/dartaotruntime
[static analysis]: /guides/language/analysis-options
[`String.fromEnvironment()` constructor]: https://api.dart.dev/stable/dart-core/String/String.fromEnvironment.html

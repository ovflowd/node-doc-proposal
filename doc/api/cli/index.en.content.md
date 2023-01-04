# Command-line API

<!-- Note: When a Heading has no manual `id` {} it will fallback to the `name` field + the contents of the heading -->
<!-- in this case it would be `#api/cli/command-line-api` -->

Node.js comes with a variety of CLI options. These options expose built-in
debugging, multiple ways to execute scripts, and other helpful runtime options.

To view this documentation as a manual page in a terminal, run `man node`.

## Synopsis

`node [options] [V8 options] [<program-entry-point> | -e "script" | -] [--] [arguments]`

`node inspect [<program-entry-point> | -e "script" | <host>:<port>] …`

`node --v8-options`

Execute without arguments to start the [REPL][].

For more info about `node inspect`, see the [debugger][] documentation.

## Program entry point

The program entry point is a specifier-like string. If the string is not an
absolute path, it's resolved as a relative path from the current working
directory. That path is then resolved by [CommonJS][] module loader. If no
corresponding file is found, an error is thrown.

If a file is found, its path will be passed to the [ECMAScript module loader][]
under any of the following conditions:

* The program was started with a command-line flag that forces the entry
  point to be loaded with ECMAScript module loader.
* The file has an `.mjs` extension.
* The file does not have a `.cjs` extension, and the nearest parent
  `package.json` file contains a top-level [`"type"`][] field with a value of
  `"module"`.

Otherwise, the file is loaded using the CommonJS module loader. See
[Modules loaders][] for more details.

### ECMAScript modules loader entry point caveat

When loading [ECMAScript module loader][] loads the program entry point, the `node`
command will only accept as input only files with `.js`, `.mjs`, or `.cjs`
extensions; and with `.wasm` extensions when
[`--experimental-wasm-modules`][] is enabled.

## Options {#api/cli/section/options}

<!-- YAML
changes:
  - version: v10.12.0
    pr-url: https://github.com/nodejs/node/pull/23020
    description: Underscores instead of dashes are now allowed for
                 Node.js options as well, in addition to V8 options.
-->

All options, including V8 options, allow words to be separated by both
dashes (`-`) or underscores (`_`). For example, `--pending-deprecation` is
equivalent to `--pending_deprecation`.

If an option that takes a single value (such as `--max-http-header-size`) is
passed more than once, then the last passed value is used. Options from the
command line take precedence over options passed through the [`NODE_OPTIONS`][]
environment variable.

### `-` {#api/cli/sections/options/-}

Alias for stdin. Analogous to the use of `-` in other command-line utilities,
meaning that the script is read from stdin, and the rest of the options
are passed to that script.

### `--` {#api/cli/sections/options/--}

Indicate the end of node options. Pass the rest of the arguments to the script.
If no script filename or eval/print script is supplied prior to this, then
the next argument is used as a script filename.

### `--abort-on-uncaught-exception` {#api/cli/sections/options/--abort-on-uncaught-exception}

Aborting instead of exiting causes a core file to be generated for post-mortem
analysis using a debugger (such as `lldb`, `gdb`, and `mdb`).

If this flag is passed, the behavior can still be set to not abort through
[`process.setUncaughtExceptionCaptureCallback()`][] (and through usage of the
`node:domain` module that uses it).

### `--build-snapshot` {#api/cli/sections/options/--build-snapshot}

Generates a snapshot blob when the process exits and writes it to
disk, which can be loaded later with `--snapshot-blob`.

When building the snapshot, if `--snapshot-blob` is not specified,
the generated blob will be written, by default, to `snapshot.blob`
in the current working directory. Otherwise it will be written to
the path specified by `--snapshot-blob`.

```console
$ echo "globalThis.foo = 'I am from the snapshot'" > snapshot.js

# Run snapshot.js to intialize the application and snapshot the
# state of it into snapshot.blob.
$ node --snapshot-blob snapshot.blob --build-snapshot snapshot.js

$ echo "console.log(globalThis.foo)" > index.js

# Load the generated snapshot and start the application from index.js.
$ node --snapshot-blob snapshot.blob index.js
I am from the snapshot
```

The [`v8.startupSnapshot` API][] can be used to specify an entry point at
snapshot building time, thus avoiding the need of an additional entry
script at deserialization time:

```console
$ echo "require('v8').startupSnapshot.setDeserializeMainFunction(() => console.log('I am from the snapshot'))" > snapshot.js
$ node --snapshot-blob snapshot.blob --build-snapshot snapshot.js
$ node --snapshot-blob snapshot.blob
I am from the snapshot
```

For more information, check out the [`v8.startupSnapshot` API][] documentation.

Currently the support for run-time snapshot is experimental in that:

1. User-land modules are not yet supported in the snapshot, so only
   one single file can be snapshotted. Users can bundle their applications
   into a single script with their bundler of choice before building
   a snapshot, however.
2. Only a subset of the built-in modules work in the snapshot, though the
   Node.js core test suite checks that a few fairly complex applications
   can be snapshotted. Support for more modules are being added. If any
   crashes or buggy behaviors occur when building a snapshot, please file
   a report in the [Node.js issue tracker][] and link to it in the
   [tracking issue for user-land snapshots][].

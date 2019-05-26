# level-bench

> **Benchmark [`abstract-leveldown`](https://github.com/Level/abstract-leveldown) and [`levelup`](https://github.com/Level/levelup) stores.**  
> Currently only suitable for use in Node.js.

[![level badge][level-badge]](https://github.com/Level/awesome)
[![npm](https://img.shields.io/npm/v/level-bench.svg?label=&logo=npm)](https://www.npmjs.com/package/level-bench)
[![Node version](https://img.shields.io/node/v/level-bench.svg)](https://www.npmjs.com/package/level-bench)
[![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)
[![Backers on Open Collective](https://opencollective.com/level/backers/badge.svg?color=orange)](#backers)
[![Sponsors on Open Collective](https://opencollective.com/level/sponsors/badge.svg?color=orange)](#sponsors)

## Example

```
npm i level-bench leveldown rocksdb
npx level-bench run write leveldown
npx level-bench run write rocksdb
npx level-bench plot write
```

Yields:

![Example plot](example.png)

## Highlights

- Target the current working directory or something npm-installed
- Compare benchmarks of different targets or options
- Derives plot labels from benchmark metadata (package, platform, ..)
- Uses unique temporary directories for every db
- Can optionally wrap the db in `encoding-down` and/or `levelup`
- Also takes `level` or something else that's already a `levelup` interface.

## Usage

### `level-bench run <benchmark> [target]`

Run a benchmark. The `benchmark` argument must be one of the named benchmarks listed below.

The `target` argument should be a path or an npm package name that is installed nearby (for example `level-bench run write leveldown`). It defaults to the current working directory. A `package.json` must exist alongside the resolved `target`.

To wrap `target` with `encoding-down` or `levelup` (you must install these dependencies yourself) pass `--encode` and/or `--levelup` (or `-el` for short). Alternatively `target` can be something that exports a `levelup` interface, for example `level-bench run write level`.

If `target` does not create persistent databases (like `memdown` or `level-mem`) you must pass `--mem`.

Options for the db can be provided via `--db <subargs>`. For example `--db [--cacheSize 16mb]` or `--db [--valueEncoding json]`.

Benchmark-specific options can be provided via `-b <subargs>`. For example `-b [-n 1e6 --concurrency 1]`. These options are listed below.

Results are by default written to `.benchmarks/<benchmark>.<time>.csv` and an accompanying JSON file for metadata. To write results to a custom file specify `--out example.csv` (`-o` for short). The metadata is used to derive a distinct benchmark name. When this doesn't suffice (for example because you're benchmarking a spinning disk versus an SSD, a fact that isn't included in the metadata) or when labels in the plot become too long, you can specify a custom name with `--name example`.

#### Examples

We can compare the performance of two git branches:

```
git checkout master && npm i
level-bench run write

git checkout wip && npm i
level-bench run write
```

Or check the overhead of `encoding-down`:

```
level-bench run write memdown --mem
level-bench run write memdown --mem --encode
```

Or a specific encoding:

```
level-bench run write level --db [--valueEncoding utf8]
level-bench run write level --db [--valueEncoding json]
```

Or compare the effect of options:

```
level-bench run write leveldown
level-bench run write leveldown --db [ --no-compression ]
```

Then plot both (or more) runs with:

```
level-bench plot write
```

<!-- Lastly, for the adventurous, you can swap out the prototype of `target` with for example some branch of `abstract-leveldown`:

```
npm i memdown Level/abstract-leveldown#improved

level-bench run write memdown --name baseline
level-bench run write memdown --proto abstract-leveldown --name improved
``` -->

#### Options

Yet to document.

### `level-bench plot <benchmark> [files]`

Plot the results using `gnuplot` (which must be installed and available in `PATH`). The `files` argument should be ([glob patterns](https://github.com/mrmlnc/fast-glob) resolving to) CSV files as generated by `level-bench run`. If not provided, defaults to `.benchmarks/<benchmark>.*.csv`.

The plot is written to `.benchmarks/<benchmark>.<time>.png` by default. This can be overridden with `--out <filename>` (`-o` for short).

#### Options

Yet to document.

## Benchmarks

### `write`

Perform concurrent `put()` operations on random string keys and values. Options:

- `-n`: amount of operations, default 1e6
- `--concurrency`: default 4
- `--valueSize`: size of value, as a number in bytes or string with unit (e.g. `--valueSize 1kb`)

_Previously known as `db-bench.js` in `leveldown`._

### `write-random`

Perform concurrent `put()` operations on random UUID string keys. Options:

- `-n`: amount of operations, default 1e7
- `--concurrency`: default 10
- `--valueSize`: size of value, as a number in bytes or string with unit (e.g. `--valueSize 1kb`)

### `write-sorted`

Perform concurrent `put()` operations on sorted string keys. Options:

- `-n`: amount of operations, default 1e7
- `--concurrency`: default 10
- `--valueSize`: size of value, as a number in bytes or string with unit (e.g. `--valueSize 1kb`)

<!-- ### Other ideas

- Write batches in different sizes (feature: define a matrix)
- Write while increasing the number of open iterators (and thus snapshots) -->

## Limitations

The target `abstract-leveldown` implementation must take a `location` as its first argument (if persistent) or ignore that argument (if transient). Options are passed to both the constructor with the signature `(location, options)` and to `db.open(options, callback)`.

## Contributing

[`Level/bench`](https://github.com/Level/bench) is an **OPEN Open Source Project**. This means that:

> Individuals making significant and valuable contributions are given commit-access to the project to contribute as they see fit. This project is more like an open wiki than a standard guarded open source project.

See the [Contribution Guide](https://github.com/Level/community/blob/master/CONTRIBUTING.md) for more details.

## Donate

To sustain [`Level`](https://github.com/Level) and its activities, become a backer or sponsor on [Open Collective](https://opencollective.com/level). Your logo or avatar will be displayed on our 28+ [GitHub repositories](https://github.com/Level) and [npm](https://www.npmjs.com/) packages. 💖

### Backers

[![Open Collective backers](https://opencollective.com/level/backers.svg?width=890)](https://opencollective.com/level)

### Sponsors

[![Open Collective sponsors](https://opencollective.com/level/sponsors.svg?width=890)](https://opencollective.com/level)

## License

[MIT](LICENSE.md) © 2019-present [Contributors](CONTRIBUTORS.md).

[level-badge]: https://leveljs.org/img/badge.svg

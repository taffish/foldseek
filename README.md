# taf-foldseek

TAFFISH wrapper for [Foldseek](https://github.com/steineggerlab/foldseek), a fast protein structure search, clustering, and multimer comparison suite for PDB/mmCIF structure datasets.

This repository packages upstream Foldseek release `10-941cd33` as the TAFFISH
package version `10-r2`. The short TAFFISH version keeps package names stable
and readable, while the Dockerfile, upstream metadata, and smoke tests pin the
exact upstream release tag and binary commit.

Release `10-r2` is a help-only TAFFISH update. It keeps the upstream software,
Dockerfile, runtime dependencies, smoke tests, and command behavior unchanged
from `10-r1`, and refreshes the terminal `taf-foldseek --help` text.

## Installation

Install from the public TAFFISH Hub index:

```sh
taf update
taf install foldseek
```

Install the exact release:

```sh
taf install foldseek 10-r2
```

For local testing before the app is published to the public index:

```sh
taf install --from .
```

## Usage

Show TAFFISH app help:

```sh
taf-foldseek --help
```

Show the TAFFISH package version:

```sh
taf-foldseek --version
```

Show upstream Foldseek version:

```sh
taf-foldseek foldseek version
```

Show upstream command help:

```sh
taf-foldseek foldseek
taf-foldseek foldseek easy-search
taf-foldseek foldseek createdb
```

Search one structure against a folder or Foldseek database:

```sh
taf-foldseek foldseek easy-search query.pdb target_structures result.m8 tmp --threads 8
```

Create and index a reusable structure database:

```sh
taf-foldseek foldseek createdb target_structures targetDB --threads 8
taf-foldseek foldseek createindex targetDB tmp --threads 8
taf-foldseek foldseek easy-search query.pdb targetDB result.m8 tmp --threads 8
```

Cluster structures:

```sh
taf-foldseek foldseek easy-cluster structures clusterRes tmp --threads 8
```

Run a reciprocal best-hit search:

```sh
taf-foldseek foldseek easy-rbh query_structures target_structures rbh.m8 tmp --threads 8
```

Run database-format search and convert the output:

```sh
taf-foldseek foldseek createdb query_structures queryDB
taf-foldseek foldseek createdb target_structures targetDB
taf-foldseek foldseek search queryDB targetDB resultDB tmp --threads 8
taf-foldseek foldseek convertalis queryDB targetDB resultDB result.m8
taf-foldseek foldseek convert2pdb targetDB target.pdb
```

Download one of the supported public structure databases:

```sh
taf-foldseek foldseek databases PDB pdb tmp
taf-foldseek foldseek easy-search query.pdb pdb result.m8 tmp --threads 8
```

Because this is a command-mode TAFFISH tool, the first non-option argument is
the in-container command. Foldseek's actual executable is named `foldseek`, so
the clearest form is:

```sh
taf-foldseek foldseek easy-search ...
taf-foldseek foldseek easy-cluster ...
taf-foldseek foldseek createdb ...
```

Do not use `taf-foldseek easy-search ...` as the normal form. In command mode,
TAFFISH will interpret `easy-search` as an executable inside the container, not
as a subcommand of `foldseek`.

The `--` separator is only useful for option-leading arguments to the default
`foldseek` command:

```sh
taf-foldseek -- -h
taf-foldseek -- --help
```

For Foldseek modules such as `version`, `easy-search`, or `createdb`, use the
explicit `taf-foldseek foldseek <module> ...` form.

This README lists common usage patterns, not the full upstream manual. The
TAFFISH wrapper calls the upstream `foldseek` command directly, so official
Foldseek modules and options are available as upstream implements them. Use
`taf-foldseek foldseek` or the upstream user guide for the complete module list.

## Package

```text
name: foldseek
command: taf-foldseek
version: 10-r2
kind: tool
image: ghcr.io/taffish/foldseek:10-r2
upstream release: 10-941cd33
upstream binary commit: 941cd33ff0771cd2e3f144e3293e22a2b87e9fda
```

## Container

The container image is built from `docker/Dockerfile`. It starts from
`alpine:3.20`, downloads official Foldseek release binaries from GitHub,
and verifies the downloaded archives with pinned sha256 checksums. The
official Foldseek CPU binaries used here are static Linux binaries, which makes
the small Alpine runtime appropriate.

For `linux/amd64`, the image installs the official `foldseek-linux-avx2`
CPU binary. This upstream binary requires AVX2 support. On `linux/amd64`
systems without AVX2, the launcher exits with a clear message instead of
silently running an unsupported binary.

For `linux/arm64`, the image installs the official `foldseek-linux-arm64`
binary. The upstream README from the binary package is kept under
`/opt/foldseek`.

The image includes these user-facing commands and runtime tools:

```text
foldseek
bash
gawk
grep
curl
wget
xargs
tar
gzip
```

The downloader and archive tools are included because upstream workflows such
as `foldseek databases` can call external download and archive utilities.
Foldseek can use `aria2c`, `curl`, or `wget` for downloads; this image provides
`curl` and `wget` rather than bundling `aria2c`. The core structure search and
clustering workflows are provided by the upstream `foldseek` binary itself.

This first TAFFISH package is a CPU Foldseek build. Upstream release
`10-941cd33` also publishes a GPU archive, but that CUDA-enabled binary is not
bundled in this image. `--gpu 1` workflows require a future GPU-specific
TAFFISH app or a custom image with the upstream GPU binary and backend GPU
runtime options.

The image is built and validated for:

```text
linux/amd64
linux/arm64
```

The TAFFISH metadata declares a Docker smoke check:

```text
exist: foldseek, bash, gawk, grep, curl, wget, xargs, tar, gzip
test:  foldseek version reports the pinned upstream binary commit
test:  top-level Foldseek help is available
test:  easy-search, createdb, databases, and key module help surfaces are available
test:  shell runtime is usable
test:  a tiny PDB easy-search produces a tabular hit
test:  a tiny PDB easy-cluster produces cluster outputs
test:  a tiny PDB easy-rbh produces a tabular reciprocal hit
test:  createdb, search, convertalis, and convert2pdb run as a DB workflow
```

During TAFFISH Hub indexing, this smoke metadata verifies that the published
image exposes the expected command surface, reports the pinned upstream binary
commit, includes the helper runtime tools needed by upstream workflows, and
can run representative local structure search, clustering, RBH, and
database-format tasks. It does not download remote databases or exhaustively
validate every Foldseek module.

Each smoke command is self-contained because the public index runs every
`[smoke].test` entry in a fresh temporary container. No smoke entry depends on
files created by an earlier entry.

## Upstream

- Project: Foldseek
- Source: <https://github.com/steineggerlab/foldseek>
- Release: <https://github.com/steineggerlab/foldseek/releases/tag/10-941cd33>
- Upstream license: GPL-3.0
- Citation:
  - van Kempen et al. 2023, DOI: `10.1038/s41587-023-01773-0`
  - Barrio-Hernandez et al. 2023, DOI: `10.1038/s41586-023-06510-w`
  - Kim et al. 2024, DOI: `10.1101/2024.04.14.589414`

## Maintainer Notes

Useful checks before publishing:

```sh
taf check
taf compile -- foldseek version
taf publish --release --dry-run
docker build -t ghcr.io/taffish/foldseek:10-r2 -f docker/Dockerfile .
docker build --platform linux/amd64 -t ghcr.io/taffish/foldseek:10-r2-amd64-test -f docker/Dockerfile .
docker build --platform linux/arm64 -t ghcr.io/taffish/foldseek:10-r2-arm64-test -f docker/Dockerfile .
docker run --rm ghcr.io/taffish/foldseek:10-r2 foldseek version
docker run --rm ghcr.io/taffish/foldseek:10-r2 foldseek easy-search
```

The repository wrapper files are licensed under Apache-2.0. Upstream Foldseek
is distributed under GPL-3.0, and bundled third-party components remain under
their own upstream licenses.

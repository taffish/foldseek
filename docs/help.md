taf-foldseek 10-r1

TAFFISH wrapper for Foldseek, a fast protein structure search, clustering, and
multimer comparison suite for PDB/mmCIF structure datasets.

Usage:
  taf-foldseek [TAF-APP-OPTION]
  taf-foldseek [FOLDSEEK-CMD] [FOLDSEEK-ARGS...]
  taf-foldseek [FOLDSEEK-OPTION] [FOLDSEEK-ARGS...]

TAF app options:
  -h, --help       Show this help text
  -v, --version    Show package and command version
  --compile        Print generated shell code instead of running it
  --               Stop parsing TAFFISH wrapper options

Upstream option calls:
  taf-foldseek -- -h
  taf-foldseek -- --help

Upstream commands:
  taf-foldseek foldseek version
  taf-foldseek foldseek
  taf-foldseek foldseek easy-search
  taf-foldseek foldseek createdb

Recommended examples:
  taf-foldseek --version
  taf-foldseek foldseek version
  taf-foldseek -- -h
  taf-foldseek foldseek easy-search query.pdb target_structures result.m8 tmp --threads 8
  taf-foldseek foldseek createdb target_structures targetDB --threads 8
  taf-foldseek foldseek createindex targetDB tmp --threads 8
  taf-foldseek foldseek easy-search query.pdb targetDB result.m8 tmp --threads 8
  taf-foldseek foldseek easy-cluster structures clusterRes tmp --threads 8
  taf-foldseek foldseek easy-rbh query_structures target_structures rbh.m8 tmp --threads 8
  taf-foldseek foldseek search queryDB targetDB resultDB tmp --threads 8
  taf-foldseek foldseek convertalis queryDB targetDB resultDB result.m8
  taf-foldseek foldseek convert2pdb targetDB target.pdb
  taf-foldseek foldseek databases PDB pdb tmp

Common Foldseek modules:
  easy-search           Search structures or structure DBs and write tabular/HTML/PDB output
  easy-cluster          Sensitive structure clustering from structure files or DBs
  easy-rbh              Reciprocal best-hit workflow
  easy-multimersearch   Multimer-level search
  easy-multimercluster  Multimer-level clustering
  createdb              Convert PDB/mmCIF/tar files, directories, TSV lists, or DBs to Foldseek DB
  createindex           Precompute an index for repeated searches
  databases             List and download supported public structure databases
  search                Database-format sensitive structure search
  cluster               Database-format structure clustering
  rbh                   Database-format reciprocal best-hit search
  multimersearch        Database-format multimer-level search
  multimercluster       Database-format multimer-level clustering
  convertalis           Convert alignment DB to BLAST-tab, SAM, or custom formats
  convert2pdb           Convert Foldseek structure DB to PDB output
  createmultimerreport  Convert complex DB to TSV report

Notes:
  - This command runs Foldseek inside the TAFFISH container image.
  - The TAFFISH package and upstream executable are both named foldseek. The
    clearest command-mode form is taf-foldseek foldseek ...
  - taf-foldseek --help and taf-foldseek --version are handled by the TAFFISH
    command wrapper. Use taf-foldseek foldseek version for the upstream binary
    commit.
  - The -- separator is mainly useful for option-leading top-level foldseek
    arguments such as taf-foldseek -- -h. Foldseek modules such as version,
    easy-search, and createdb should be called as taf-foldseek foldseek <module>.
  - Do not use taf-foldseek easy-search ... as the normal form. In command mode,
    easy-search would be interpreted as an executable, not as a subcommand of
    foldseek.
  - The wrapper calls the upstream foldseek binary directly. Official Foldseek
    modules and options are available as upstream implements them.
  - The image includes official CPU binaries. On linux/amd64 it uses the
    official AVX2 binary and therefore requires AVX2 CPU support; on linux/arm64
    it uses the official ARM64 binary.
  - GPU archives from upstream release 10-941cd33 are not bundled in this CPU
    image. GPU workflows require a GPU-specific build and container backend GPU
    runtime options.
  - Workflows such as databases may download large remote reference databases
    and need sufficient network, disk, and temporary-directory space. This
    image provides curl and wget for those upstream download paths.
  - Input structures can be PDB/mmCIF files, gzipped structures, directories,
    tar archives, TSV file lists, or existing Foldseek databases as supported
    by upstream createdb.
  - Input, output, and tmp paths should be accessible from the current working
    directory or mounted user paths. Large searches and clustering jobs can
    require substantial temporary disk space and memory.

Container:
  image: ghcr.io/taffish/foldseek:10-r1
  supported backends: apptainer, podman, docker
  supported platforms: linux/amd64 with AVX2, linux/arm64
  upstream release: 10-941cd33
  upstream binary commit: 941cd33ff0771cd2e3f144e3293e22a2b87e9fda

Upstream:
  project: Foldseek
  source:  https://github.com/steineggerlab/foldseek
  release: https://github.com/steineggerlab/foldseek/releases/tag/10-941cd33
  license: GPL-3.0
  citation: van Kempen et al. 2023; Barrio-Hernandez et al. 2023; Kim et al. 2024
  doi: 10.1038/s41587-023-01773-0; 10.1038/s41586-023-06510-w; 10.1101/2024.04.14.589414

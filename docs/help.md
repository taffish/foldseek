taf-foldseek 10-r2

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

Help and version:
  taf-foldseek -- -h
  taf-foldseek -- --help
  taf-foldseek foldseek version
  taf-foldseek foldseek

Examples:
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

Common modules:
  easy-search, easy-cluster, easy-rbh, easy-multimersearch,
  easy-multimercluster, createdb, createindex, databases, search, cluster, rbh,
  multimersearch, multimercluster, convertalis, convert2pdb,
  createmultimerreport.

Notes:
  Use taf-foldseek foldseek <module> for modules such as easy-search or
  createdb. Do not use taf-foldseek easy-search ... as the normal form; command
  mode would treat easy-search as an executable.
  The -- separator is mainly for option-leading top-level foldseek arguments.
  This is the CPU image. GPU workflows require a GPU-specific build and
  container backend GPU runtime options.
  Public database workflows may download large remote structure databases and
  need network, disk, and temporary-directory space.
  Inputs can be PDB/mmCIF files, gzipped structures, directories, tar archives,
  TSV file lists, or existing Foldseek databases.

Container:
  image: ghcr.io/taffish/foldseek:10-r2
  platforms: linux/amd64 with AVX2, linux/arm64
  upstream release: 10-941cd33
  upstream binary commit: 941cd33ff0771cd2e3f144e3293e22a2b87e9fda

Upstream:
  source:  https://github.com/steineggerlab/foldseek
  release: 10-941cd33
  license: GPL-3.0
  citation: van Kempen et al. 2023; Barrio-Hernandez et al. 2023

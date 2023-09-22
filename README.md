# krakenXtract

[![Release](https://github.com/Sam-Sims/krakenXtract/actions/workflows/release.yaml/badge.svg)](https://github.com/Sam-Sims/krakenXtract/actions/workflows/release.yaml)
![GitHub release (with filter)](https://img.shields.io/github/v/release/sam-sims/krakenxtract)
![crates.io](https://img.shields.io/crates/v/krakenxtract
)

Extract reads from a FASTQ file based on taxonomic classification via Kraken2.


## Motivation

Heavily inspired by the great [KrakenTools](https://github.com/jenniferlu717/KrakenTools). 

Having been wanting to experiment with Rust for a while, this is essentially an implementation of the `extract_kraken_reads.py` script, [re-implemented](https://www.reddit.com/media?url=https%3A%2F%2Fi.redd.it%2Fgood-for-you-crab-v0-5v9ygeh9r1c91.jpg%3Fs%3Dd759db5275e32c6e2bd5c22bddbd783acca46247) in Rust. 

The main motivation was to provide a speedup when extracting a large number of reads from large FASTQ files as well as keeping the output compressed (if needed) - and to learn Rust!

## Current features

- Extract all reads from a `fastq` file based on a taxonomic id
- Extract all the parents or the children of the specified taxon id
- Supports single or paired-end `fastq` files
- Supports both uncompressed or `gzip` inputs and outputs.
- Multithreaded
- ~4.4x speed up compared to KrakenTools

## Benchmarks (WIP)

For more detail see [benchmarks](benchmarks/benchmarks.md)

## Installation

#### `Precompiled`
Github release: [0.2.3](https://github.com/Sam-Sims/krakenXtract/releases/tag/v0.2.3)

#### `Cargo`
Requires [cargo](https://www.rust-lang.org/tools/install)
```
cargo install krakenxtract
```

#### `Build from source`

#### Install rust toolchain:

To install please refer to the rust documentation: [docs](https://www.rust-lang.org/tools/install)

#### Clone the repository:

```bash
git clone https://github.com/Sam-Sims/krakenxtract
```

#### Build and add to path:

```bash
cd kraken-extract
cargo build --release
export PATH=$PATH:$(pwd)/target/release
```

All executables will be in the directory kraken-extract/target/release.

## Usage

### Basic Usage:

```bash
krakenXtract -k <kraken_output> -i <fastq_file> -t <taxonomic_id> -o <output_file>
```
Or, if you have paired-end illumina reads:
```bash
krakenXtract -k <kraken_output> -i <R1_fastq_file> -i <R2_fastq_file> -t <taxonomic_id> -o <R1_output_file> -o <R2_output_file>
```
If you want to extract all children of a taxon:
```bash
krakenXtract -k <kraken_output> -r <kraken_report> -i <fastq_file> -t <taxonomic_id> --children -o <output_file>
```

### Arguments:

### Required:

#### Input

`-i, --input`

This option will specify the input files containing the reads you want to extract from. They can be compressed - (`gzip`, `bzip`, `lzma`, `zstd`). Paired end reads can be specified by:

Using `--input` twice: `-i <R1_fastq_file> -i <R2_fastq_file>`

Using `--input` once but passing both files: `-i <R1_fastq_file> <R2_fastq_file>`

  This means that bash wildcard expansion works: `-i *.fastq`

#### Output

`-o, --output`

This option will specify the output files containing the extracted reads. The order of the output files is assumed to be the same as the input. 

By default the compression will be inferred from the output file extension for supported file types (`gzip`, `bzip`, `lzma` and `zstd`). If the output type cannot be inferred, plaintext will be output.

#### Kraken Output

`-k, --kraken`

This option will specify the path to the Kraken2 output containing taxonomic classification of read IDs.

#### Taxid

`-t, --taxid`

This option will specify the taxon ID for reads you want to extract.

### Optional:

#### Output type

`-O, --output-type`

This option will manually set the compression mode used for the output file and will override the type inferred from the output path. 

Valid values are:

- `gz` to output gzip
- `bz` to output bzip
- `lzma` to output lzma
- `zstd` to output zstd
- `none` to not apply compresison

#### Compression level

`-l, --level`

This option will set the compression level to use if compressing the output. Should be a value between 1-9 with 1 being the fastest but largest file size and 9 is for slowest, but best file size. By default this is set at 6, but for the highest speeds 2 is a good trade off for speed/filesize.

#### Output fasta

`--output-fasta`

This option will output a fasta file, with read ids as headers.

#### Kraken Report

`-r, --report`

This option specifies the path to the report file generated by Kraken2. If you want to use `--parents` or `--children` then is argument is required.

#### Parents

`--parents`

This will extract reads classified at all taxons between the root and the specified `--taxid`.

#### Children

`--children`

This will extract all the reads classified as decendents or subtaxa of `--taxid` (Including the taxid).

#### Exclude

`--exclude`

This will output every read except those matching the taxid. Works with `--parents` and `--children`

## Future plans

- [x] Support unzipped fastq files
- [x] Support paired end FASTQ files
- [x] `--include-parents` and `--include-children` arguments
- [ ] Supply multiple taxonomic IDs to extract
- [x] Exclude taxonomic IDs
- [ ] `--append`
- [x] `--compression-mode`
- [x] More verbose output
- [ ] Proper benchmarks
- [x] Output fasta format (for blast??)
- [x] Output non `gz`
- [ ] Tests

## Version

- 0.3.0

## Changelog

### 0.3.0

- Support for paired-end files
- Major under the hood changes to use Noodles for fastq parsing, and Niffler to handle compression (RIP my own code)
- Output a fasta file with `--output-fasta`
- Streamline arguments related to compression types/level

### 0.2.3

- Code optimisations

### 0.2.2

- Increased verbosity of outputs to user
- `--no-compress` flag to output a standard, plaintext fastq file
- `--exclude` to exclude specified reads. Works with `--children` and `--parents`
- Docstrings under the hood

### 0.2.1

- Fixes to reduce memory usage

### 0.2.0

- Detect and handle `gz` files or plain files
- `--compression` arg to select compression type
- `zlib-ng` to speed up gzip handling
- `--children` and `--parents` to save children and parents based on kraken report

### 0.1.0

- First release

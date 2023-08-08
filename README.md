# kraken-extract

Extract reads from a FASTQ file based on taxonomic classification via Kraken2.

Written in Rust.

## Background

I recently wanted to extract reads from a large-ish (6GB) FASTQ file (~5.5 million reads), based on taxonomic classifications. For that I used the great [KrakenTools](https://github.com/jenniferlu717/KrakenTools). This however took a while both parse the Kraken2 output file and extract/write the matching reads. Having been wanting to experiment with Rust for a while, this inspired me to re-implement the `extract_kraken_reads.py` script in Rust as a learning exercise.

This is currently an early implementation (and my first Rust programme!), with plans to expand functionality.

## Current features:

- Extract all reads from a `fastq.gz` file based on a taxonomic id
- ~ 720% speed up over KrakenTools 

### Benchmarks (rough):

Based on 6.1Gb fastq.gz with 5,454,495 reads | 1.8Gb kraken output - 

Time to parse the kraken output, extract all matching reads, and write to new fastq file.

**KrakenTools (Output non gzip):**
| Type | Time       |
|------|------------|
| real | 14m 53s |

**kraken-extract:**
| Type | Time    |
|------|---------|
| real | 2m 04s |

## Installation

Clone the repository:
```
git clone https://github.com/Sam-Sims/kraken-extract
```

Build from source:
```bash
cd kraken-extract
cargo build --release
export PATH=$PATH:$(pwd)/target/release
```

All executables will be in the directory kraken-extract/target/release.

## Usage

```
./kraken-extract --kraken <kraken_output> --fastq <fastq_file> --taxid <taxonomic_id> --output <output_file>
```

## Future plans
- Support unzipped fastq files
- Support paired end FASTQ files
- `--include-parents` and `--include-children` arguments
- Supply multiple taxonomic IDs to extract
- Exclude taxonomic IDs
- `--append`
- `--compression-mode <fast/default/slow>`
- More verbose output
- Proper benchmarks

## Version
- 0.1.0

# Variant-aware Cas-OFFinder v2

Identifies potential CRISPR off-target sites by incorporating individual genetic variants from a phased, single-sample VCF file. Based on the [version-2 branch](https://github.com/pnucolab/variant-aware-cas-offinder/tree/version-2) of the Variant-aware Cas-OFFinder tool.

## Pipeline Steps

1. **Re-compress VCF** — Handles plain gzip VCF with `zcat | bgzip` + `tabix` index
2. **Normalize VCF** — `vcfallelicprimitives | bcftools norm -m- | vcfcreatemulti` (vcflib=1.0.3)
3. **Split by chromosome** — Avoids vcf2fasta segfaults by processing one chromosome at a time
4. **Generate allelic FASTA** — `vcf2fasta -f <ref> -p <prefix> -n NAN <per-chrom.vcf>`
5. **Run cas-offinder** — Executes per individual FASTA file, not per directory
6. **Combine results** — Merges all off-target results into a single output file

## Required Inputs

| File | Description |
|------|-------------|
| `Sample.vcf.gz` | Phased, single-sample VCF (can be plain gzip or bgzip) |
| `reference/` | Directory with reference genome FASTA + `.fai` index |
| `input.txt` | Cas-OFFinder query file (FASTA path line + PAM + target sequences) |

## Expected Outputs

| File | Description |
|------|-------------|
| `combined_off_target_result.txt` | All off-target sites across all chromosomes |

## How to Run

```bash
# Build the Docker image
docker build -t autopipe-variant-aware-cas-offinder-v2 .

# Run the pipeline
docker run --rm \
  -v /path/to/input_data:/input:ro \
  -v /path/to/output:/output \
  autopipe-variant-aware-cas-offinder-v2 \
  snakemake --cores all --snakefile /pipeline/Snakefile
```

## Configuration

Edit `config.yaml` to customize:

- `vcf_input` — Path to input VCF file (inside `/input`)
- `reference_dir` — Path to reference genome directory
- `reference_fasta` — Reference FASTA filename
- `query_input` — Cas-OFFinder query input file
- `device_id` — `C` for CPU, `G` for GPU, `G0` for GPU device 0
- `chromosomes` — List of chromosomes to process
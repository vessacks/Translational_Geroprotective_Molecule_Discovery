# Drug Targeting Mendelian Randomization Pipeline

This repository implements a multi-stage **Mendelian randomization (MR)** pipeline aimed at identifying and filtering genetic instruments for multiple drug targets, each with one or more “proxy strategies,” culminating in an MR analysis.

---

## Project Overview

- **Goal**: Provide a robust QC + MR pipeline for target genes associated with a set of candidate drugs. Each target gene is tested with multiple proxy strategies that define parameters like p-value cutoffs, LD clumping thresholds, confounder sets, etc.  
- **Scope**:  
  - We have ~13 drugs, each with its own confounder sets.  
  - Each drug may have subfolders or distinct dataset references.  
  - Each gene can have different parameters (e.g., distance cutoffs).  
  - We eventually perform a 2-stage MR analysis, after the SNP instruments are thoroughly QC’d.

---


## Directory Structure

```bash
drug_mr_project/
├── data/
│   ├── drug_a/
│   │   ├── strategy_a
│   │   │   ├── raw/
│   │   │   │   └── # Contains CSVs for each gene's QTL data, often separated by drug or strategy
│   │   │   ├── processed/
│   │   │   │   └── # Final sets of SNPs after full QC, used for MR
├── results/
│   ├── figures/               # Where plots go
│   └── tables/                # Where tables go
├── config/
│   ├── pipeline_params.yaml   # global pipeline parameters (p-value thresholds, r^2, distances, etc)
│   ├── drug_confounders.yaml  # per-drug confounder sets for phenoscanner queries
├── src/
│   ├── qc/
│   │   ├── filter_snps.py
│   │   ├── ld_clump.py
│   │   ├── compute_Fstats.py
│   │   ├── phenoscanner.py
│   │   └── ...
│   ├── harmonize/
│   │   └── harmonize_snps.py
│   ├── mr/
│   │   └── run_mr.py
│   ├── utils/
│   │   └── i_o.py
│   └── pipeline.py
├── environment.yml
├── README.md
```

## Configuration

We maintain configuration files in a `config/` directory:

1. **`pipeline_params.yaml`**: Holds global pipeline parameters (e.g., p-value thresholds, LD r² cutoffs, or distance bounds for cis definitions). For example:
   ```yaml
   p_value_cutoffs:
     strategy_a: 1e-6
     strategy_b: 5e-8

   ld_clump_r2:
     strategy_a: 0.1
     strategy_b: 0.01

   max_gene_distance:
     GENE1: 50000
     GENE2: 100000
     default: 50000

2. **`drug_confounders.yaml`**: Lists each drug’s confounder set for Phenoscanner filtering. For example:
    ```yaml
    drug_a:
    confounders: ["Blood_Pressure", "Lipids"]

    drug_b:
    confounders: ["Height", "ADHD"]

    drug_c:
    confounders: ["RA", "Obesity"]

## Quickstart (QC Steps)

### Purpose
This **QC** stage identifies valid SNP instruments for each `(drug, strategy, gene)` combination:

1. **Filtering** by p-value & gene distance  
2. **LD Clumping** (using the chosen r² threshold from the config)  
3. **Compute F Statistic** to ensure strong instruments  
4. **Phenoscanner Query** to exclude SNPs associated with drug-specific confounders  
5. **Harmonize** final SNP set (e.g., remove palindromic SNPs, align effect alleles)

## Contact 
For questions or collaboration requests:

Name: Robert Campbell
Email: h.robert.campbell@gmail.com
Institution: Nuffield Department of Population Health, Oxford University
We welcome suggestions or pull requests.
   

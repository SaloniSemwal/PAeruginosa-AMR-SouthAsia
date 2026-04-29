# Detailed Methodology

## 1. Data Collection

### BV-BRC
- Searched for P. aeruginosa genomes from India, Pakistan, Bangladesh, Nepal
- Downloaded Metadata, AMRPhenotypes, SpecialtyGenes CSV files per country
- Total: 355 genomes

### NCBI Datasets
- Downloaded Indian P. aeruginosa genomes (2018-2024)
- Used NCBI Datasets CLI tool on Windows
- Total: 41 additional genomes

### PubMLST
- Downloaded ST/CC reference XLSX files for all four countries
- Used only as ST-to-CC mapping reference
- Could not be merged with genomic data — internal lab IDs do not match GCA accessions

Total metadata: 396 genomes | FASTA files downloaded: 281

### Exclusions from FASTA download
- 71 genomes had JAGIMY/WGS format accessions — not supported by NCBI Datasets CLI
- 5 genomes unavailable on NCBI servers
- 39 genomes had no accession number in BV-BRC metadata
- Excluded WGS accessions listed in data/accessions/wgs_accessions.txt

---

## 2. Environment Setup

    conda create -n staramr_env -c conda-forge -c bioconda staramr
    conda activate staramr_env

Software versions:
- StarAMR v0.12.1
- BLAST 2.16.0
- MLST 2.32.2
- Perl 5.32.1
- Ubuntu 22.04.5 WSL2

---

## 3. FASTA Download

Downloaded in 4 batches using NCBI Datasets CLI:

    datasets download genome accession --inputfile batch1.txt --filename batch1.zip

Accession lists in data/accessions/

---

## 4. StarAMR Analysis

    staramr search --output-dir staramr_complete $(find ./fasta_all -name "*.fna")

Parameters:
- Identity threshold: 90%
- Overlap threshold: 60%
- ResFinder: acquired ARG detection
- PlasmidFinder: plasmid replicon detection
- PointFinder: NOT run — P. aeruginosa not supported
- Runtime: 6.39 minutes on Ubuntu WSL2

---

## 5. Intrinsic Gene Removal

Removed chromosomally encoded genes present in all P. aeruginosa:
- blaPAO variants
- blaOXA-50, blaOXA-395, blaOXA-396, blaOXA-486, blaOXA-488, blaOXA-494
- fosA_4, catB7, aph(3')-IIb

Before removal: 2906 detections
After removal: 1549 acquired ARG detections, 94 unique ARGs

---

## 6. Python Analysis (Kaggle)

Libraries: pandas, matplotlib, seaborn, numpy, scipy

Steps:
- Merged ARG data with metadata on GCA accession
- Assigned 5-year time bins (1995-2024)
- Calculated temporal prevalence per bin
- Calculated geographic prevalence per country
- Built co-occurrence matrix using presence/absence dot product
- Generated 5 figures

---

## 7. Planned Future Analysis

- MLST sequence type and clonal complex analysis
- Statistical tests: Shapiro-Wilk, Kruskal-Wallis, Mann-Whitney U
- Integration of WGS accessions if assemblies become available

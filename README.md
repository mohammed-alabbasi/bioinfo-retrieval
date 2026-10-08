# Programmatic Data Retrieval in Bioinformatics

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![R](https://img.shields.io/badge/R-4.3-276DC3?logo=r&logoColor=white)
![Environment](https://img.shields.io/badge/environment-conda%20%2F%20mamba-44A833?logo=anaconda&logoColor=white)
![VS Code](https://img.shields.io/badge/VS%20Code-Jupyter-007ACC?logo=visualstudiocode&logoColor=white)

Six self-contained Jupyter notebooks that retrieve real biological records from **NCBI**, **Ensembl**, **UniProt** and **GEO**, using Python (Biopython, `requests`) and R (biomaRt, GEOquery). Every notebook runs on its own from a clean kernel and saves what it retrieves.

## Overview

Every downstream analysis, whether quality control, translation, alignment, variant annotation or differential expression, begins with obtaining the correct record from the correct database in a form the next tool can consume. This repository implements six representative retrieval patterns end to end: fetching a single GenBank record by accession, querying a gene-level annotation from the Ensembl REST API, retrieving a protein record from UniProt, fetching several related mRNA records in one batched E-utilities call, translating gene symbols into stable identifiers with biomaRt, and downloading a complete published expression dataset from GEO as a Bioconductor `ExpressionSet`.

The notebooks are split between Python and R according to the object each task naturally returns. Record-by-record retrieval and JSON handling are done in Python, where Biopython's `SeqRecord` model and the `requests` library are the most direct tools. The last two notebooks are written in R because their results, a `data.frame` of identifiers and an `ExpressionSet`, are the native input of the Bioconductor ecosystem (GenomicRanges, limma, DESeq2) used in later analysis steps.

No notebook reads a file written by another; they can be run in any order.

## Data sources

| # | Notebook | Language | Source | Record | What is retrieved |
|---|----------|----------|--------|--------|-------------------|
| 1 | `01_example1_lacZ_entrez.ipynb` | Python | NCBI Entrez (E-utilities) via Biopython | *lacZ*, GenBank `V00613` (*Escherichia coli*) | Full GenBank record; CDS extracted, length and GC content computed |
| 2 | `02_example2_BRCA1_ensembl.ipynb` | Python | Ensembl REST API via `requests` | *BRCA1*, `ENSG00000012048` (*Homo sapiens*) | Gene-level annotation: symbol, chromosome, coordinates, biotype |
| 3 | `03_example3_insulin_uniprot.ipynb` | Python | UniProt REST API via `requests` | Insulin, `P01308` (*Homo sapiens*) | Protein name, organism and full precursor sequence |
| 4 | `04_example4_batch_insulin.ipynb` | Python | NCBI Entrez, batched `efetch` | *INS* `NM_000207` (human) and *Ins2* `NM_008387` (mouse) | Two mRNA records in a single call; CDS lengths compared |
| 5 | `05_example5_breast_panel_biomart.ipynb` | R | Ensembl BioMart via biomaRt | *BRCA1, BRCA2, ESR1, ERBB2, PGR* | Symbol to Ensembl gene ID, Entrez ID, chromosome and biotype |
| 6 | `06_example6_GSE2034_geoquery.ipynb` | R | NCBI GEO via GEOquery | `GSE2034` (platform `GPL96`) | Series matrix as an `ExpressionSet`; sample metadata exported |

## Repository structure

```
.
├── 01_example1_lacZ_entrez.ipynb
├── 02_example2_BRCA1_ensembl.ipynb
├── 03_example3_insulin_uniprot.ipynb
├── 04_example4_batch_insulin.ipynb
├── 05_example5_breast_panel_biomart.ipynb
├── 06_example6_GSE2034_geoquery.ipynb
├── environment.yml
├── .gitignore
├── results/        # summaries, tables and JSON written by the notebooks
└── data/           # created at runtime (raw records and downloads); git-ignored
```

The notebooks sit at the repository root so that the relative paths `data/` and `results/` resolve correctly. Each notebook creates the folders it needs on first run. Raw downloads in `data/` are excluded from version control; the notebooks regenerate them from the public databases.

## Getting started

### Prerequisites

- [Visual Studio Code](https://code.visualstudio.com/) with the **Python** (`ms-python.python`), **Jupyter** (`ms-toolsai.jupyter`) and **R** (`REditorSupport.r`) extensions.
- [Miniforge](https://github.com/conda-forge/miniforge), which provides `conda` and `mamba` with the conda-forge channel preconfigured.
- An internet connection. All data are fetched live from public databases.

On Windows, use WSL2 (`wsl --install`) together with the VS Code **WSL** extension and run everything inside the Linux environment. The bioconda channel does not provide native Windows builds for most bioinformatics packages.

### Installation

```bash
git clone https://github.com/<your-username>/<repository-name>.git
cd <repository-name>

mamba env create -f environment.yml
conda activate bioinfo-skill1-retrieval

# register the R kernel for Jupyter (once per environment)
R -e 'IRkernel::installspec()'
```

### Running the notebooks

1. Open the repository folder in VS Code (**File → Open Folder**) so that it is the workspace root.
2. Open a notebook and choose the kernel with **Select Kernel** in the top right: the `bioinfo-skill1-retrieval` Python interpreter for notebooks 01 to 04, and the **R** kernel for notebooks 05 and 06.
3. In notebooks 01 and 04, replace `you@example.com` with your own address. NCBI requires a contact email for E-utilities requests.
4. Run the cells from top to bottom.

## Expected results

Values below come from the public records themselves. Figures derived from live databases can shift slightly between annotation releases.

| # | Key results | Files written |
|---|-------------|---------------|
| 1 | *lacZ* coding sequence of 3,075 bp (β-galactosidase, about 1,024 residues), GC content of about 52% | `data/V00613.gb`, `data/lacZ_V00613_cds.fasta`, `results/example1_lacZ_summary.txt` |
| 2 | *BRCA1* on chromosome 17, biotype `protein_coding` | `results/example2_BRCA1_summary.json` |
| 3 | Preproinsulin of 110 amino acids; the mature hormone is the 51-residue product of its A and B chains | `data/P01308_insulin.fasta`, `results/example3_insulin_summary.json` |
| 4 | Both the human *INS* and mouse *Ins2* coding sequences are 333 bp | `data/insulin_INS_Ins2.gb`, `results/example4_cds_lengths.csv` |
| 5 | Five breast-cancer genes mapped to stable identifiers (table below) | `results/example5_breast_panel_ids.csv` |
| 6 | 286 samples, 22,283 probe sets on the Affymetrix HG-U133A platform (`GPL96`) | series matrix cached in `data/`, `results/example6_GSE2034_sample_metadata.csv` |

Identifier mapping produced by notebook 5:

| HGNC symbol | Ensembl gene ID | Entrez Gene ID | Chromosome |
|-------------|-----------------|----------------|------------|
| *BRCA1* | ENSG00000012048 | 672 | 17 |
| *BRCA2* | ENSG00000139618 | 675 | 13 |
| *ERBB2* | ENSG00000141736 | 2064 | 17 |
| *ESR1* | ENSG00000091831 | 2099 | 6 |
| *PGR* | ENSG00000082175 | 5241 | 11 |

Notebook 4 shows how conserved the insulin coding sequence is across about 90 million years of mammalian divergence: human and mouse share an identical coding length, matching the 110-residue precursor retrieved in notebook 3. `GSE2034` in notebook 6 is the Wang et al. (2005) cohort of 286 lymph-node-negative primary breast tumours.

## Environment

All versions are pinned in `environment.yml` for reproducibility.

| Component | Version | Used for |
|-----------|---------|----------|
| Python | 3.11 | Notebooks 01 to 04 |
| Biopython | 1.83 | Entrez access and GenBank parsing |
| requests | 2.31 | Ensembl and UniProt REST calls |
| R | 4.3 | Notebooks 05 and 06 |
| biomaRt (Bioconductor) | 2.58.0 | Ensembl BioMart queries |
| GEOquery (Bioconductor) | 2.70.0 | GEO series download |
| IRkernel, Jupyter, ipykernel | latest | R and Python kernels in VS Code |

## Notes

- **NCBI usage.** Set a valid email in notebooks 01 and 04. For heavier use, register an NCBI API key to raise the request limit.
- **Ensembl BioMart (notebook 5).** The notebook queries the live BioMart service. If the default endpoint is slow or unavailable, connect through a regional mirror (`useEnsembl(..., mirror = "useast")`) or pin a release with the `version` or `host` arguments of `useEnsembl()`.
- **GEO download (notebook 6).** The `GSE2034` series matrix is a large download. The notebook raises R's download timeout to 10 minutes, and the file is cached in `data/` so later runs reuse it.
- **Versions of public data.** Database records and annotations are updated over time, so counts or coordinates may differ slightly from those listed above.

## References

- Cock PJA, et al. Biopython: freely available Python tools for computational molecular biology and bioinformatics. *Bioinformatics* 25(11):1422-1423 (2009).
- Durinck S, Spellman PT, Birney E, Huber W. Mapping identifiers for the integration of genomic datasets with the R/Bioconductor package biomaRt. *Nature Protocols* 4:1184-1191 (2009).
- Davis S, Meltzer PS. GEOquery: a bridge between the Gene Expression Omnibus (GEO) and BioConductor. *Bioinformatics* 23(14):1846-1847 (2007).
- Wang Y, et al. Gene-expression profiles to predict distant metastasis of lymph-node-negative primary breast cancer. *The Lancet* 365(9460):671-679 (2005).

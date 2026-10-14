# Skill 1 — Data Retrieval | المهارة 1 — استرجاع البيانات

Six independent notebooks (four Python, two R), each fetching real public data and saving its own outputs. Every notebook runs on its own from a clean kernel.
ستة دفاتر مستقلة (أربعة بايثون واثنان R)، كل منها يجلب بيانات عامة حقيقية ويحفظ مخرجاته بنفسه، ويعمل كل دفتر بمفرده من kernel نظيف.

## Structure | الهيكل

```
bioinfo-skill1-retrieval/
├── 01_example1_Tn3_entrez.ipynb          Python · NCBI Entrez   · Transposon Tn3 V00613 (E. coli)
├── 02_example2_BRCA1_ensembl.ipynb        Python · Ensembl REST  · BRCA1 ENSG00000012048 (Human)
├── 03_example3_insulin_uniprot.ipynb      Python · UniProt REST  · Insulin P01308 (Human)
├── 04_example4_batch_insulin.ipynb        Python · NCBI Entrez   · NM_000207 + NM_008387 (Human + Mouse)
├── 05_example5_breast_panel_biomart.ipynb R      · biomaRt       · BRCA1/BRCA2/ESR1/ERBB2/PGR (Human)
├── 06_example6_GSE2034_geoquery.ipynb     R      · GEOquery      · GSE2034, 286 breast tumours
├── bioinformatics_skill1_retrieval_final.html
├── environment.yml
├── data/        ← fetched records and raw downloads  | السجلات المجلوبة والتنزيلات الخام
└── results/     ← summaries (txt / json / csv)       | الملخصات
```

## Quick start | البدء السريع

1. Install VS Code + extensions: Python (`ms-python.python`), Jupyter (`ms-toolsai.jupyter`), R (`REditorSupport.r`).
2. **Windows only:** run `wsl --install` (administrator PowerShell), restart, install the *Remote - WSL* extension, then use "WSL: Open Folder in WSL". bioconda has no native Windows builds.
3. Install Miniforge (github.com/conda-forge/miniforge) inside that shell: `bash Miniforge3-$(uname)-$(uname -m).sh`, then check `mamba --version`.
4. In this folder:
```bash
mamba env create -f environment.yml
conda activate bioinfo-skill1-retrieval
R -e 'IRkernel::installspec()'      # once: registers the R kernel
```
5. Open this folder in VS Code (File → Open Folder). In each notebook click **Select Kernel**: Python for 01–04, R for 05–06.

Before running Examples 1 and 4, replace `you@example.com` with your real email (NCBI requires it). Examples 5 and 6 need internet access (BioMart and GEO); GSE2034 downloads once into `data/`.
قبل تشغيل المثالين 1 و4 استبدل `you@example.com` ببريدك الحقيقي. المثالان 5 و6 يحتاجان إنترنت، وتُنزَّل GSE2034 مرة واحدة إلى `data/`.

Full explanation of every step: `bioinformatics_skill1_retrieval_final.html`.

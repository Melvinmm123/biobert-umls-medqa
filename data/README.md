# Data setup

Two datasets are required to reproduce the results in this repo. Neither is committed here — one is licensed (UMLS), one is external (PubMedQA).

## 1. PubMedQA

Public benchmark for biomedical QA. Free, no license required.

**Download:**
```bash
git clone https://github.com/pubmedqa/pubmedqa data/pubmedqa
```

The notebooks expect three preprocessed JSON files at the repo root:

preprocessed/
├── processed_train_set.json (800 examples)
├── processed_dev_set.json (100 examples)
└── processed_test_set.json (100 examples)

Each file is a dict keyed by PMID; each entry has `question`, `context`, and `label` (one of `yes` / `no` / `maybe`). Adapt raw PubMedQA JSON to that structure — the training notebook (`notebooks/biobert_umls_pubmedqa.ipynb`, cell 14) shows the expected schema.

## 2. UMLS (MRCONSO, MRSTY, MRREL)

UMLS requires a **free UTS license from the NIH**. Approval typically takes 1–3 business days.

**Request access:** https://uts.nlm.nih.gov/uts/signup-login

**Download the UMLS Metathesaurus Full Release**, then extract these three tables:

- **MRCONSO.RRF** — concept names and synonyms
- **MRSTY.RRF** — semantic types
- **MRREL.RRF** — inter-concept relationships

Place them under `data/umls/`.

## 3. Generate UMLS-augmented datasets

The training notebook loads augmented datasets from:

final_biobert/
├── train_dataset_mrsty.json
├── dev_dataset_mrsty.json
├── test_dataset_mrsty.json
├── train_dataset_mrrel.json
├── dev_dataset_mrrel.json
└── test_dataset_mrrel.json


These are produced by joining PubMedQA questions/contexts with UMLS concepts, semantic types, and relationships. The preprocessing pipeline is described in Section 2.2 of [`docs/report.pdf`](../docs/report.pdf).

## Directory layout after setup

biobert-umls-medqa/
├── data/
│ ├── umls/ # MRCONSO.RRF, MRSTY.RRF, MRREL.RRF
│ └── pubmedqa/ # raw PubMedQA JSON
├── preprocessed/ # processed PubMedQA (train/dev/test)
└── final_biobert/ # UMLS-augmented PubMedQA (train/dev/test × mrsty/mrrel)


None of the above directories are committed — regenerate them locally following the steps above.

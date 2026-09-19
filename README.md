# BioBERT + UMLS for Medical Question Answering

> Integrating UMLS knowledge (MRCONSO, MRSTY, MRREL) into BioBERT for the PubMedQA benchmark. Explores whether structured medical knowledge lifts biomedical QA performance — and where it doesn't.

**RIT MS AI · NLP research · Sep–Dec 2024** · **[📄 Full write-up (PDF)](docs/report.pdf)**

## Results

Six configurations fine-tuned on PubMedQA (1,000 train / 200 val / 500 test). See [`docs/report.pdf`](docs/report.pdf) for the full metric table and discussion.

Headline: **MRREL fine-tuned with dropout regularization was the only configuration that meaningfully lifted test accuracy without overfitting.** Naïve embedding-augmentation variants (MRSTY-carry, MRREL-carry) hit ≥99% train/val but collapsed on test — a clean overfitting-vs-signal story.

## Approach

Three UMLS sources integrated into a BioBERT QA pipeline:

- **MRCONSO** — concept names and synonyms (entity linking)
- **MRSTY** — semantic types like *Disease*, *Procedure* (concept filtering)
- **MRREL** — medical relations like *treats*, *causes* (context expansion)

Each variant augments tokenized question + context with UMLS-derived embeddings before the classification head. Trained with cross-entropy loss, Adam optimizer, batch size 8, 5–10 epochs, with dropout regularization on later variants.

## Repo layout

├── notebooks/
│ ├── biobert_umls_pubmedqa.ipynb # training pipeline
│ └── evaluation.ipynb # metrics across train/val/test
├── docs/
│ └── report.pdf # full research write-up
├── data/
│ └── README.md # data setup instructions
├── requirements.txt
├── LICENSE
└── README.md


## Reproduce

**1. Environment:**
```bash
git clone https://github.com/Melvinmm123/biobert-umls-medqa
cd biobert-umls-medqa
pip install -r requirements.txt
```

**2. Data:** UMLS and PubMedQA aren't distributed with this repo. Follow [`data/README.md`](data/README.md) — UMLS needs a free NIH license (1–3 day approval); PubMedQA is public.

**3. Train:**
```bash
jupyter notebook notebooks/biobert_umls_pubmedqa.ipynb
```

**4. Evaluate all configurations:**
```bash
jupyter notebook notebooks/evaluation.ipynb
```

## Key takeaways

- Naïve knowledge-graph augmentation can drive training accuracy above 99% without meaningfully lifting test performance — augmentation strategy matters more than augmentation *presence*.
- **MRREL (relations) carried more discriminative signal than MRCONSO (synonyms) or MRSTY (types) alone.** Consistent with the idea that *what a medical concept does* is more useful for QA than *what it's called*.
- **Fine-tuning with dropout regularization** was necessary to prevent the augmented models from memorizing.

## Limitations & future work

- PubMedQA has only 1,000 training examples. Data augmentation or transfer learning from a larger biomedical QA corpus would likely help more than adding another UMLS source.
- Future work: SNOMED CT integration for richer clinical relations, multilingual variants (e.g., MedMCQA), and ablation on *where* in the pipeline UMLS is injected (input concat vs intermediate representation vs attention biasing).

## Citation

Melel, M. M. (2024). Enhancing BioBERT with UMLS for Medical Question Answering.
RIT MS AI, NLP coursework.


Underlying works (BioBERT, UMLS, PubMedQA) cited in [`docs/report.pdf`](docs/report.pdf).

## Author
[Melvin Mathew](https://github.com/Melvinmm123) — RIT MS AI · melvin.melel@gmail.com

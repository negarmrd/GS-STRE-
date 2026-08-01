# GS-STRE
**A Pipeline-Aware Framework** for extracting Organism–Natural Product relations from biomedical abstracts, using a two-stage NER + RE pipeline trained to be explicitly aware of its own upstream errors (*pipeline-aware*).


## 📊 Dataset

The data foundation of this project is built on the **LOTUS** database. The file `processed_lotus_data` is derived from the raw LOTUS database: after pruning genome-scale documents (documents with an excessive number of recorded relations that would otherwise introduce noise), the remaining organism–chemical tuples are stored together with the abstract of their reference paper in this processed file. This file is the primary input to the *Generative Seeding* stage.
All datasets used in this project (raw and processed) are available in the **`data.zip`** archive.

---

## 🔁 Data Processing Pipeline

### 1. Generating the Seed Dataset with an LLM

We ran the [`Generative Seeding.ipynb`](https://github.com/negarmrd/GS-STRE-/blob/main/Generative%20Seeding.ipynb) notebook from `main`. This code uses an instruction-tuned LLM as an offline filter: for each (organism, chemical) tuple, it searches the reference abstract and extracts the corresponding text span only if the relation is explicitly supported by the text; tuples with no textual support are discarded. The output of this stage is the **`lotus_seed_dataset`**, available in `data.zip`.

### 2. Separating the Test Samples

We then ran the [`raw_dataset.ipynb`](https://github.com/negarmrd/GS-STRE-/blob/main/raw_dataset.ipynb) notebook from `main` to split out the evaluation (test) samples from the rest of the data, preventing any data leakage between training and testing.

---

## 🧠 Models

### NER — Abstract-Level Entity Recognition

Notebook: [`NERmodel_abstract.ipynb`](https://github.com/negarmrd/GS-STRE-/blob/main/NERmodel_abstract.ipynb)

For each abstract, this code first aggregates every organism/chemical span associated with that `pubmed_id` before tagging, resolving the partial-labeling problem, and then fine-tunes a PubMedBERT-based token classification model on this silver-standard corpus, optimizing the organism and chemical detection thresholds separately.

### RE — LoRA-Adapted Relation Classifier with Pipeline-Aware Training

Notebook: [`RE_lora_pipeline_aware.ipynb`](https://github.com/negarmrd/GS-STRE-/blob/main/RE_lora_pipeline_aware.ipynb)

This code reads the final `pipeline_aware_re_train.json` dataset (containing gold-positive samples and hard negatives mined from the NER model's errors), injects entity markers (`[O]...[/O]`, `[C]...[/C]`) into the abstract text, and fine-tunes a PubMedBERT sequence classifier with LoRA adapters (injected into the query/key/value projection matrices) for binary relation classification.

The final trained RE model weights are also included in this repository under **`RE_model`**.

---

## 📁 Repository Structure

```
.
├── Generative Seeding.ipynb
├── raw_dataset.ipynb
├── NERmodel_abstract.ipynb
├── RE_lora_pipeline_aware.ipynb
├── RE_model/
└── data.zip
```

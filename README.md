# GS-STRE
**A Pipeline-Aware Framework** for extracting Organism–Natural Product relations from biomedical abstracts, using a two-stage NER + RE pipeline trained to be explicitly aware of its own upstream errors (*pipeline-aware*).


## 📊 Dataset

The data foundation of this project is built on the **LOTUS** database. The file `processed_lotus_data` is derived from the raw LOTUS database: after pruning genome-scale documents (documents with an excessive number of recorded relations that would otherwise introduce noise), the remaining organism–chemical tuples are stored together with the abstract of their reference paper in this processed file. This file is the primary input to the *Generative Seeding* stage.
All datasets used in this project (raw and processed) are available in the **`data.zip`** archive.

---



###  Separating the Test Samples

We then ran the [`raw_dataset.ipynb`](https://github.com/negarmrd/GS-STRE-/blob/main/raw_dataset.ipynb) notebook from `main` to split out the evaluation (test) samples from the rest of the data, preventing any data leakage between training and testing.

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

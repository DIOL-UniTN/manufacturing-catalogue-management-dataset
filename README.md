# Enhancing Catalogue Management in Manufacturing with Hybrid Retrieval and LLM-Based Insertion — Dataset

<!--[![Journal](https://img.shields.io/badge/Journal-International_Journal_of_Intelligent_Manufacturing-success.svg)]()-->
[![Topic](https://img.shields.io/badge/Topic-Hybrid_Retrieval_&_LLMs-blue.svg)]()
[![Data](https://img.shields.io/badge/Data-Pseudo--anonymised-orange.svg)]()
[![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)](LICENSE)

This repository contains the datasets for the paper **"Enhancing Catalogue Management in Manufacturing with Hybrid Retrieval and LLM-Based Insertion"**<!--, to be published in the **International Journal of Intelligent Manufacturing**-->.

> 🔒 **All data in this repository are pseudo-anonymised.** The catalogues come from the materials catalogue of a real industrial manufacturing company. Before release, the data were pseudo-anonymised so that the company and any confidential information cannot be identified, while preserving the structure, vocabulary, and style of the original descriptions needed to reproduce the experiments in the paper. Item codes, descriptions, and queries **must not** be interpreted as real product codes or as references to real products, suppliers, or customers.

## 📖 Overview

Industrial companies maintain **materials catalogues** containing hundreds of thousands of items, each identified by a code and described by short and long free-text descriptions, often in **multiple languages**. Before inserting a new item, an operator must verify that an equivalent item does not already exist (**selection**) and, if it does not, write a new description that matches the conventions of the existing entries (**insertion**).

This repository releases the data used to evaluate both steps:

- **Catalogues** — three multilingual (Italian/English) materials catalogues.
- **Selection datasets** — free-text queries, each paired with the catalogue item it refers to and two distractor items, used to evaluate the retrieval methods.
- **Insertion datasets** — free-text queries describing new items, used to evaluate LLM-based generation of catalogue descriptions.

The code that uses these data is available in the companion code repository.

## 🗂️ Repository Structure

```
.
├── catalogues/
│   ├── catalog_01.csv
│   ├── catalog_02.csv
│   └── catalog_03.csv
├── datasets/
│   ├── selection/
│   │   └── dataset_<cc>_v<n>_<lang>.csv   # Selection (retrieval) evaluation sets
│   └── insertion/
│       └── llm_dataset_<cc>.csv           # Insertion (LLM generation) evaluation sets
├── LICENSE
└── README.md
```

`<cc>` ∈ {`01`, `02`, `03`} identifies the catalogue the file refers to, `<n>` ∈ {`1`, `2`, `3`} the dataset version, and `<lang>` ∈ {`ita`, `eng`} the language of the queries.

## 📦 Data

### Catalogues (`catalogues/`)

Comma-separated, UTF-8, one row per item:

```
id,short_ita,short_eng,long_ita,long_eng
```

| Column | Description |
| --- | --- |
| `id` | Pseudo-anonymised item code |
| `short_ita`, `short_eng` | Short description in Italian and English |
| `long_ita`, `long_eng` | Long description in Italian and English (may be empty) |

| Catalogue | Items | `long_ita` filled | `long_eng` filled |
| --- | ---: | ---: | ---: |
| `catalog_01.csv` | 2,451 | 1,614 | 1,618 |
| `catalog_02.csv` | 6,288 | 5,979 | 0 |
| `catalog_03.csv` | 9,701 | 6,679 | 0 |

Example:

```
5000000629-00,CUSCINETTO RADIALE SFERA 629,DEEP GROOVE B-BEARING 629,,
```

### Selection datasets (`datasets/selection/`)

Comma-separated, one row per query:

```
query,positive,hard_negative,soft_negative
```

| Column | Description |
| --- | --- |
| `query` | Free-text query, as an operator might write it |
| `positive` | `id` of the catalogue item the query refers to |
| `hard_negative` | `id` of a distractor item that is similar to the positive one |
| `soft_negative` | `id` of a distractor item that is less similar to the positive one |

All identifiers refer to the `id` column of the catalogue with the same number (e.g. `dataset_02_*` → `catalog_02.csv`).

> **Note.** In `dataset_01_v1_eng.csv` and `dataset_01_v1_ita.csv` the header order is `query,positive,soft_negative,hard_negative`. Always read the columns by name rather than by position.

Three versions are provided for each catalogue and language:

| Version | Query style | Example |
| --- | --- | --- |
| `v1` | Reformulations close to the catalogue descriptions | `anello compensatore 0810/020/01` |
| `v2` | Conversational, noisy queries with typos and filler words | `I want: 629 deep-groved bbearings specifc` |
| `v3` | Short, terse queries, one per item, on a sample of 500 items | `kit swtich search` |

Number of queries per file:

| Catalogue | v1 ita | v1 eng | v2 ita | v2 eng | v3 ita | v3 eng |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| `01` | 7,350 | 7,353 | 9,932 | 7,355 | 500 | 500 |
| `02` | 18,864 | 18,871 | 18,862 | 18,866 | 500 | 500 |
| `03` | 29,106 | 29,107 | 29,113 | 29,103 | 496 | 500 |

### Insertion datasets (`datasets/insertion/`)

Comma-separated, one row per query describing an item to be inserted into the corresponding catalogue:

```
query,language
```

| Column | Description |
| --- | --- |
| `query` | Free-text description of the new item |
| `language` | Language of the query (`ita` or `eng`) |

| File | Queries | `ita` | `eng` |
| --- | ---: | ---: | ---: |
| `llm_dataset_01.csv` | 100 | 42 | 58 |
| `llm_dataset_02.csv` | 100 | 50 | 50 |
| `llm_dataset_03.csv` | 100 | 50 | 50 |

## 🚀 Getting Started

```bash
git clone https://github.com/DIOL-UniTN/manufacturing-catalogue-management-dataset.git
```

Loading a catalogue and its selection dataset with pandas:

```python
import pandas as pd

catalogue = pd.read_csv("catalogues/catalog_01.csv", dtype=str, keep_default_na=False)
dataset = pd.read_csv("datasets/selection/dataset_01_v3_ita.csv", dtype=str)

items = catalogue.set_index("id")
row = dataset.iloc[0]
print(row["query"], "->", items.loc[row["positive"], "short_ita"])
```

Read the `id` columns as strings (`dtype=str`), so that codes such as `5000000629-00` are not altered.

To use the data with the code of the paper, place `catalogues/` and `datasets/` in the root of the code repository, or point to the files through its command-line arguments.

<!--## 📝 Citation

```bibtex
@article{genetti2026catalogue,
  title   = {Enhancing Catalogue Management in Manufacturing with Hybrid Retrieval and LLM-Based Insertion},
  author  = {Genetti, Stefano and Cecchin, Nicol\`o and Iacca, Giovanni},
  journal = {International Journal of Intelligent Manufacturing},
  year    = {2026}
}
```

## 🙏 Acknowledgements

We thank the industrial manufacturing company that provided the materials catalogue and the domain expertise used to build and validate the evaluation datasets.-->

## 👥 Authors

Stefano Genetti  
Nicolò Cecchin  
Giovanni Iacca

## 📄 License

Released under the [MIT License](LICENSE).

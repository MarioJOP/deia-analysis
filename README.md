# Understanding DEIA Discourse in Developer Subreddit Communities: Do LLMs Capture or Distort It?

## 📚 Context

Affirmative action policies and DEIA (Diversity, Equity, Inclusion and Accessibility) initiatives have become central topics in workplace and technology communities, particularly following recent U.S. policies rolling back diversity programs. In our paper ["Understanding DEIA Discourse in Developer Subreddit Communities: Do LLMs Capture or Distort It?"](ASE_2026___DEIA.pdf), we investigate how software engineers discuss DEIA on Reddit and whether Large Language Models (LLMs) can faithfully capture or tend to distort these discussions. This repository contains the replication package associated with the study, including the **PANDEIA** dataset, code, and instructions to reproduce our results.

## 🎯 Goal

This project analyzes DEIA-related discourse in online developer subreddit communities with respect to:
- **Polarization classification** (Pro-DEIA, Anti-DEIA, or Neutral)
- **Thematic subtheme identification** (Gender, Race, LGBTQIA+, Disability)
- **Temporal discourse evolution** — before, during, and after major DEIA policy events

It also evaluates how different prompting strategies affect LLM performance when compared to human-annotated ground truth.

## 🧪 Methodology

### Research Questions

- **RQ1** *(How people speak)*: What is the distribution of discourse polarization (Pro-DEIA, Anti-DEIA, and Neutral) in Reddit discussions?
- **RQ2** *(What they talk about)*: What are the predominant DEIA-related subthemes discussed in Reddit posts?
- **RQ3** *(How discourse evolves)*: How does DEIA-related discourse evolve before, during, and after the implementation of DEIA policy changes?

### 📦 Dataset — PANDEIA

- **Source**: 4 technology-related subreddits collected via [PRAW](https://praw.readthedocs.io/) (Python Reddit API Wrapper)
- **Total posts annotated**: 1,017
- **PANDEIA dataset** (IS_DEIA = Yes): **700 posts**
- **Annotation**: Each post independently reviewed by 2 annotators; disagreements resolved by a 3rd annotator (15 volunteers total)
- **Labels**: Polarization (Pro-DEIA / Anti-DEIA / Neutral), Subthemes (multi-label: Gender, Race, LGBTQIA+, Disability, None), and metadata
- **Note**: Due to Reddit's Terms of Service, the columns `Title`, `Text`, and `Author` are **not distributed** in this repository. See [Recovering post content](#recovering-post-content-from-reddit) below for instructions on how to retrieve them.

| Subreddit | Description |
|---|---|
| womenintech | Inclusive space for women in tech (technical and non-technical roles) |
| cscareerquestions | Career development and industry challenges for CS professionals |
| girlsgonewired | Women in technology sharing professional experiences |
| ExperiencedDevs | Experienced software engineers discussing advanced career topics |

### 🤖 Models

All models were run locally via [Ollama](https://ollama.com):

| Model | Parameters | Model Size | Quantization |
|---|---|---|---|
| [deepseek-r1:7b](https://ollama.com/library/deepseek-r1) | 7.62B | 4.7 GB | Q4_K_M |
| [mistral:7b](https://ollama.com/library/mistral) | 7.25B | 4.4 GB | — |
| [Llama-3.1-8B-Instruct](https://ollama.com/library/llama3.1) | 8.03B | 16 GB | BF16 |
| [gurubot/phi3-mini-abliterated:q4](https://ollama.com/gurubot/phi3-mini-abliterated:q4) | 3.82B | 2.4 GB | Q4_K_M |
| [qwen2.5-coder-3b-instruct-q6_k](https://ollama.com/library/qwen2.5-coder) | 3.4B | 2.8 GB | Q6_K |
| [falcon3:3b](https://ollama.com/library/falcon3) | 3.23B | 2.0 GB | Q4_K_M |

### 💬 Prompting Strategies

| Strategy | Description |
|---|---|
| Zero-shot | Direct instruction with no examples |
| One-shot | Single example provided alongside the target post |
| Few-shot | Multiple examples to enable in-context learning |

The prompts are defined in the [`prompts`](prompts) folder, organized by task:

| File | Task | Strategy |
|---|---|---|
| [`prompts/polarization/zeroshot.txt`](prompts/polarization/zeroshot.txt) | Polarization | Zero-shot |
| [`prompts/polarization/oneshot.txt`](prompts/polarization/oneshot.txt) | Polarization | One-shot |
| [`prompts/polarization/fewshot.txt`](prompts/polarization/fewshot.txt) | Polarization | Few-shot |
| [`prompts/subthemes/zeroshot.txt`](prompts/subthemes/zeroshot.txt) | Subthemes | Zero-shot |
| [`prompts/subthemes/oneshot.txt`](prompts/subthemes/oneshot.txt) | Subthemes | One-shot |
| [`prompts/subthemes/fewshot.txt`](prompts/subthemes/fewshot.txt) | Subthemes | Few-shot |

## 📂 Data & Results

### 🗂️ PANDEIA Dataset

The human-annotated ground truth is located at [`llm_results_public/PANDEIA_Manual_Labeling_Final.csv`](llm_results_public/PANDEIA_Manual_Labeling_Final.csv).

It contains **700 DEIA-related posts** with the following columns:

| Column | Description |
|---|---|
| `ID` | Reddit post ID |
| `Date` | Post date |
| `Subreddit` | Source subreddit |
| `Url` | Permalink to the original post |
| `Score` | Reddit score (upvotes) |
| `num_comment` | Number of comments |
| `IS_DEIA?` | Whether the post is DEIA-related |
| `Agreement_Polarization` | Consensus label: Pro-DEIA / Anti-DEIA / Neutral |
| `Agreement_Subthemes` | Consensus subtheme(s): Gender, Race, LGBTQIA+, Disability, None |
| `Keywords` | Keywords extracted during annotation |
| `confidence level` | Annotator confidence (1–5) |
| `Justification` | Annotator justification for the label |

> **Note**: The columns `Title`, `Text`, and `Author` are **not distributed** due to Reddit's Terms of Service. See [Recovering post content](#recovering-post-content-from-reddit) for instructions on how to retrieve them.

### 🤖 LLM Outputs

The raw classification outputs produced by each model are available in [`llm_results_public/`](llm_results_public/) and can be reused in future studies without re-running the inference pipeline:

| File | Model |
|---|---|
| [`PANDEIA_RESULTS_Deepseek.csv`](llm_results_public/PANDEIA_RESULTS_Deepseek.csv) | DeepSeek-R1 7B |
| [`PANDEIA_RESULTS_Falcon3.csv`](llm_results_public/PANDEIA_RESULTS_Falcon3.csv) | Falcon3 3B |
| [`PANDEIA_RESULTS_Llama.csv`](llm_results_public/PANDEIA_RESULTS_Llama.csv) | LLaMA 3.1 8B |
| [`PANDEIA_RESULTS_Mistral.csv`](llm_results_public/PANDEIA_RESULTS_Mistral.csv) | Mistral 7B |
| [`PANDEIA_RESULTS_Phi3.csv`](llm_results_public/PANDEIA_RESULTS_Phi3.csv) | Phi3-Mini |
| [`PANDEIA_RESULTS_Qwen.csv`](llm_results_public/PANDEIA_RESULTS_Qwen.csv) | Qwen2.5-Coder 3B |

Each file contains the model's predicted polarization and subtheme labels for all prompting strategies (zero-shot, one-shot, few-shot), aligned with the post IDs in the PANDEIA dataset.

---

## 💻 Installation & Usage

### ✅ Requirements

- **Python 3.10+**
- [Ollama](https://ollama.com) (for running local LLMs)

### 1. Clone the repository

```bash
git clone <repository-url>
cd DEIA_analysis
```

### 2. Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows use: venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Recovering post content from Reddit

Due to Reddit's Terms of Service, the columns `Title`, `Text`, and `Author` are not included in the distributed dataset. The file `llm_results_public/PANDEIA_DATASET.csv` contains only the metadata columns (`ID`, `Date`, `Subreddit`, `Permalink`, `Score`, `num_comment`).

To run `Run_LLMs.ipynb`, you must enrich this file with the post content by re-collecting it from Reddit using [PRAW](https://praw.readthedocs.io/). Use the `ID` column (Reddit post ID) and the `Subreddit` column to fetch each post:

```python
import praw
import pandas as pd

reddit = praw.Reddit(
    client_id="YOUR_CLIENT_ID",
    client_secret="YOUR_CLIENT_SECRET",
    user_agent="YOUR_USER_AGENT"
)

df = pd.read_csv("llm_results_public/PANDEIA_DATASET.csv")

def fetch_post(row):
    try:
        submission = reddit.submission(id=row["ID"])
        return pd.Series({
            "Title": submission.title,
            "Text": submission.selftext,
            "Author": str(submission.author)
        })
    except Exception:
        return pd.Series({"Title": None, "Text": None, "Author": None})

df[["Title", "Text", "Author"]] = df.apply(fetch_post, axis=1)
df.to_csv("llm_results_public/PANDEIA_DATASET.csv", index=False)
```

> **Note**: Some posts may have been deleted or removed since data collection. In those cases, `Text` will be `None` or `[deleted]` — the LLM pipeline handles these gracefully.

### 5. Pull the models via Ollama

```bash
ollama pull deepseek-r1:7b
ollama pull mistral:7b
ollama pull llama3.1:8b
ollama pull gurubot/phi3-mini-abliterated:q4
ollama pull qwen2.5-coder:3b
ollama pull falcon3:3b
```

### 6. Run the notebooks

Open the notebooks in order using Jupyter:

1. **`Run_LLMs.ipynb`** — runs all models against the PANDEIA dataset for all prompting strategies
2. **`RQ1_polarization_analysis.ipynb`** — answers RQ1 (polarization distribution)
3. **`RQ2_1_subthemes_analysis.ipynb`** — exploratory data analysis and visualizations for RQ2
4. **`RQ2_2_subthemes_analysis.ipynb`** — answers RQ2 (subtheme analysis)
5. **`RQ3_temporal_analysis.ipynb`** — answers RQ3 (temporal discourse evolution)

---

## ⚙️ Hardware Setup Used for Experimentation

| Component | Specification |
|---|---|
| CPU | AMD Ryzen 9 9950X |
| GPU | NVIDIA RTX 5070 Ti 16 GB |
| RAM | 64 GB |

---

## 🧩 Model Recommendations for Resource-Constrained Environments

### 🖥️ Setups with ≤8 GB VRAM

These models are the lightest in the study and can run efficiently using [Ollama](https://ollama.com):

- [**Falcon3 (3B)**](https://ollama.com/library/falcon3) — 2.0 GB, Q4_K_M quantization
- [**Qwen2.5-Coder (3B)**](https://ollama.com/library/qwen2.5-coder) — 2.8 GB, Q6_K quantization
- [**Phi3-Mini (3.82B)**](https://ollama.com/library/phi3) — 2.4 GB, Q4_K_M quantization

### 🧠 CPU-only Setups

For environments without GPU access, the following CPU-optimized models are available via the **steamdj** collection on [Ollama](https://ollama.com):

- [**Mistral (CPU-only)**](https://ollama.com/steamdj/mistral-cpu-only)
- [**LLaMA 3.1 (CPU-only)**](https://ollama.com/steamdj/llama3.1-cpu-only)

> **Note**: Running larger models (Llama-3.1-8B BF16) on CPU may be very slow. For preliminary replication, prefer quantized variants (Q4/Q6) of the smaller models listed above.

---

## 📁 Repository Structure

```
DEIA_analysis/
├── llm_results_public/                             # 📂 PANDEIA dataset and raw LLM outputs
│   ├── PANDEIA_Manual_Labeling_Final.csv           # ⭐ Human-annotated ground truth (700 DEIA posts)
│   ├── PANDEIA_RESULTS_Deepseek.csv                # 🤖 LLM outputs — DeepSeek-R1 7B
│   ├── PANDEIA_RESULTS_Falcon3.csv                 # 🤖 LLM outputs — Falcon3 3B
│   ├── PANDEIA_RESULTS_Llama.csv                   # 🤖 LLM outputs — LLaMA 3.1 8B
│   ├── PANDEIA_RESULTS_Mistral.csv                 # 🤖 LLM outputs — Mistral 7B
│   ├── PANDEIA_RESULTS_Phi3.csv                    # 🤖 LLM outputs — Phi3-Mini
│   └── PANDEIA_RESULTS_Qwen.csv                    # 🤖 LLM outputs — Qwen2.5-Coder 3B
├── prompts/                                        # 📂 Prompts used for LLM inference
│   ├── polarization/                               # Prompts for polarization classification
│   │   ├── zeroshot.txt
│   │   ├── oneshot.txt
│   │   └── fewshot.txt
│   └── subthemes/                                  # Prompts for subtheme identification
│       ├── zeroshot.txt
│       ├── oneshot.txt
│       └── fewshot.txt
├── results/
│   ├── polarization/                               # Polarization evaluation results per model/strategy
│   │   ├── zeroshot/<Model>_evaluation/
│   │   ├── oneshot/<Model>_evaluation/
│   │   ├── fewshot/<Model>_evaluation/
│   │   ├── polarization_comparison.xlsx
│   │   └── results_table_polarization.xlsx
│   ├── subthemes/                                  # Subtheme evaluation results per model/strategy
│   │   ├── zeroshot/<Model>_evaluation/
│   │   ├── oneshot/<Model>_evaluation/
│   │   ├── fewshot/<Model>_evaluation/
│   │   ├── subtheme_comparison.xlsx
│   │   └── results_table_subthemes.xlsx
│   └── json_problems/                              # Posts with invalid/unparseable LLM outputs
├── figures/                                        # Generated plots and visualizations
├── rq_tables_gen/                                  # Scripts to generate results tables
├── Run_LLMs.ipynb                                  # LLM inference pipeline
├── RQ1_polarization_analysis.ipynb                 # RQ1: polarization distribution analysis
├── RQ2_1_subthemes_analysis.ipynb                  # EDA and visualizations for RQ2
├── RQ2_2_subthemes_analysis.ipynb                  # RQ2: DEIA subtheme analysis
├── RQ3_temporal_analysis.ipynb                     # RQ3: temporal discourse evolution
├── requirements.txt                                # Python dependencies
└── README.md
```

---

## 📜 License

This repository uses two licenses depending on the type of content:

- **Code** (notebooks, scripts): [MIT License](LICENCE-CODE.txt) — Copyright (c) 2026 JOANNE CARNEIRO
- **Dataset** (PANDEIA, `llm_results_public/`): [Creative Commons Attribution 4.0 International](LICENSE-DATASET.txt)

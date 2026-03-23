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

- **Source**: 5 technology-related subreddits collected via [PRAW](https://praw.readthedocs.io/) (Python Reddit API Wrapper)
- **Total posts annotated**: 1,017
- **PANDEIA dataset** (IS_DEIA = Yes): **700 posts**
- **Annotation**: Each post independently reviewed by 2 annotators; disagreements resolved by a 3rd annotator (15 volunteers total)
- **Labels**: Polarization (Pro-DEIA / Anti-DEIA / Neutral), Subthemes (multi-label: Gender, Race, LGBTQIA+, Disability, None), and metadata

| Subreddit | Description |
|---|---|
| womenintech | Inclusive space for women in tech (technical and non-technical roles) |
| cscareerquestions | Career development and industry challenges for CS professionals |
| girlsgonewired | Women in technology sharing professional experiences |
| technology | Technological developments and societal impact |
| ExperiencedDevs | Experienced software engineers discussing advanced career topics |

### 🤖 Models

All models were run locally via [Ollama](https://ollama.com):

| Model | Parameters | Model Size | Quantization |
|---|---|---|---|
| [deepseek-r1:7b](https://ollama.com/library/deepseek-r1) | 7.62B | 4.7 GB | Q4_K_M |
| [mistral:7b](https://ollama.com/library/mistral) | 7.25B | 4.4 GB | — |
| [Llama-3.1-8B-Instruct](https://ollama.com/library/llama3.1) | 8.03B | 16 GB | BF16 |
| [gurubot/phi3-mini-abliterated:q4](https://ollama.com/library/phi3) | 3.82B | 2.4 GB | Q4_K_M |
| [qwen2.5-coder-3b-instruct-q6_k](https://ollama.com/library/qwen2.5-coder) | 3.4B | 2.8 GB | Q6_K |
| [falcon3:3b](https://ollama.com/library/falcon3) | 3.23B | 2.0 GB | Q4_K_M |

### 💬 Prompting Strategies

| Strategy | Description |
|---|---|
| Zero-shot | Direct instruction with no examples |
| One-shot | Single example provided alongside the target post |
| Few-shot | Multiple examples to enable in-context learning |

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

### 4. Pull the models via Ollama

```bash
ollama pull deepseek-r1:7b
ollama pull mistral:7b
ollama pull llama3.1:8b
ollama pull phi3-mini
ollama pull qwen2.5-coder:3b
ollama pull falcon3:3b
```

### 5. Run the notebooks

Open the notebooks in order using Jupyter:

1. **`Run_LLMs.ipynb`** — runs all models against the PANDEIA dataset for all prompting strategies
2. **`EDA.ipynb`** — exploratory data analysis and visualizations
3. **`RQ1_polarization_analysis.ipynb`** — answers RQ1 (polarization distribution)
4. **`RQ2_subthemes_analysis.ipynb`** — answers RQ2 (subtheme analysis)
5. **`RQ3_temporal_analysis.ipynb`** — answers RQ3 (temporal discourse evolution)

---

## ⚙️ Hardware Setup Used for Experimentation

> **TODO**: Add your hardware specifications here (CPU, RAM, GPU).

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
├── llm_results/                        # PANDEIA dataset and raw LLM outputs (CSV/XLSX)
│   ├── PANDEIA_DATASET_ManualLabeling_Final.csv
│   └── PANDEIA_RESULTS_<Model>_<strategy>.csv
├── results/
│   ├── polarization/                   # Polarization evaluation results per model/strategy
│   │   ├── zeroshot/<Model>_evaluation/
│   │   ├── oneshot/<Model>_evaluation/
│   │   └── fewshot/<Model>_evaluation/
│   ├── subthemes/                      # Subtheme evaluation results per model/strategy
│   │   ├── zeroshot/<Model>_evaluation/
│   │   ├── oneshot/<Model>_evaluation/
│   │   └── fewshot/<Model>_evaluation/
│   └── json_problems/                  # Posts with invalid/unparseable LLM outputs
├── figures/                            # Generated plots and visualizations
├── Run_LLMs.ipynb                      # LLM inference pipeline (all models × all strategies)
├── EDA.ipynb                           # Exploratory data analysis
├── RQ1_polarization_analysis.ipynb     # RQ1: polarization distribution analysis
├── RQ2_subthemes_analysis.ipynb        # RQ2: DEIA subtheme analysis
├── RQ3_temporal_analysis.ipynb         # RQ3: temporal discourse evolution
├── requirements.txt                    # Python dependencies
└── README.md
```

---

## 📜 License

> **TODO**: Add license information here.

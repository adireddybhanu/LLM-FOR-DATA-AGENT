# LLM for Data Agent — Text-to-SQL with Automated Error Repair

MSc dissertation project, Cardiff University (School of Computer Science, MSc in
Artificial Intelligence, August 2025) — Bhanu Prakash Adireddy, supervised by
Dr. José Camacho-Collados.

This project introduces a Text-to-SQL framework that pairs an LLM-based SQL generator
with an automated **validation and repair mechanism**, evaluated on the Spider 1.0
benchmark.

## Abstract

Text-to-SQL systems translate natural language questions into database queries, but
struggle to generate reliably accurate SQL, especially when a question is phrased in
different ways. This work proposes a six-layer Text-to-SQL architecture that processes
user input, engineers a prompt, generates SQL with an LLM (GPT-4o), executes it against
a database, and — the key contribution — validates the result and iteratively repairs
the query when it fails or looks wrong, before returning the answer to the user.

The framework was evaluated on **Spider 1.0** (1,034 questions across 20 databases),
comparing GPT-4o, GPT-4o Mini, and Qwen-Coder, with and without the repair mechanism.

## Key results

- **83.8% exact-match accuracy** on the Spider dev set using GPT-4o with the repair
  mechanism enabled — a **+20.8 point** improvement over the no-repair baseline (63.0%),
  and above prior published state-of-the-art results such as STRUG (70.5%, Deng et al.,
  2021).
- The repair mechanism successfully corrected **49.4%** of the queries it attempted to
  fix, using question-intent analysis, schema verification, and up to three iterative
  refinement attempts per query.
- **Question-phrasing paradox**: rephrasing questions into an "improved" / more
  explicit form *decreased* accuracy by 1.9–2.7 points across all models tested,
  suggesting current systems are sensitive to surface phrasing rather than robust to
  it — a finding discussed at length in the dissertation.

## System architecture

1. **Input Processing** — parses and prepares the incoming natural language question.
2. **Prompt Engineering** — builds the LLM prompt from the question and relevant schema.
3. **SQL Generation** — the LLM (GPT-4o / GPT-4o Mini / Qwen-Coder / Llama / CodeLlama)
   produces a candidate SQL query.
4. **Query Execution** — runs the candidate query against the target database.
5. **Validation & Repair (key contribution)** — detects execution errors and result
   discrepancies, then loops back to the LLM with diagnostic feedback for up to three
   repair attempts.
6. **Result Delivery** — returns the validated result to the user.

## Repository contents

| File | Description |
|---|---|
| [`Text_to_sql (4).ipynb`](<Text_to_sql (4).ipynb>) | Full experimental notebook: data loading/checks on Spider, prompt-based SQL generation and accuracy evaluation for GPT-4o, GPT-4o Mini, Llama, CodeLlama, and Qwen2.5-Coder-1.5B, with and without the repair mechanism and with/without schema. |
| [`Dissertation (2).pdf`](<Dissertation (2).pdf>) | Full dissertation write-up (PDF). |
| [`Dissertation.txt`](Dissertation.txt) | Plain-text extraction of the dissertation, for quick viewing/search without a PDF reader. |
| [`requirement.txt`](requirement.txt) | Python dependencies used in the notebook. |

## Dataset

Experiments use the [Spider 1.0](https://yale-lily.github.io/spider) benchmark, a
cross-domain text-to-SQL dataset spanning 200 databases and 138 domains. This repo does
not redistribute Spider — download it from the official source and point the notebook's
data-loading cells at your local copy.

## Getting started

```bash
pip install -r requirement.txt
```

The notebook expects:
- A local copy of the Spider dataset (train/dev splits + database schemas).
- An OpenAI API key available to the `openai` client for the GPT-4o / GPT-4o Mini cells
  (the notebook reads this from a local key file rather than hardcoding it — supply your
  own key path or environment variable).
- A GPU runtime (e.g. Google Colab) for the Llama / CodeLlama / Qwen-Coder cells, which
  load models via `transformers`/`torch`.

Open `Text_to_sql (4).ipynb` and run the sections top to bottom; each model/condition
(with or without repair, with or without schema) is broken into its own labelled section.

## Citation

If you reference this work, please cite the dissertation:

> Adireddy, B. P. (2025). *LLM For Data Agent*. MSc Dissertation, School of Computer
> Science, Cardiff University.

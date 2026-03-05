# LiveNote — AI-Driven Meeting Transcription, Summarization & Action Item Extraction

LiveNote is an end-to-end meeting intelligence system that converts meeting audio into **incremental transcripts, summaries, decisions, and structured action items**.

The system is designed for **near real-time operation** using chunked processing with a human-verifiable output format.

---

# 📁 Repository Structure

This repository is organized to separate **data, experiments, documentation, and implementation code** so that the project is easy to understand and extend.

```
LiveNote/
│
├── Data/
│   └── (meeting datasets, processed audio/text files)
│
├── Docs/
│   └── (project documentation, references, research notes)
│
├── Notebooks/
│   └── DatasetsEDAandInitialModels.ipynb
│
├── Presentations/
│   ├── LiveNote_MidProjectReview.mp4
│   ├── LiveNote_MidProjectReviewSlides.pdf
│   └── Satwik_LiveNoteInitialProjectPresentation.pdf
│
├── Report/
│   └── (final report drafts and evaluation write-ups)
│
├── src/
│   └── (core implementation code for the pipeline)
│
├── LiveNote_ProjectProposalTemplate.pdf
│
├── README.md
│
```

### Folder Descriptions

**Data/**
Contains datasets used in the project including raw meeting audio, transcripts, and any processed versions required for modeling and evaluation.

**Docs/**
Contains documentation related to the project including research notes, references, and design documents.

**Notebooks/**
Contains exploratory notebooks used for:

* dataset analysis
* model prototyping
* baseline experimentation

Example:

```
DatasetsEDAandInitialModels.ipynb
```

This notebook performs:

* dataset inspection
* exploratory data analysis (EDA)
* baseline summarization experiments.

**Presentations/**
Contains presentation materials related to the project including:

* project proposal presentation
* mid-project review slides
* recorded presentation videos.

**Report/**
Contains formal project reports including:

* progress reports
* evaluation results
* final project documentation.

**src/**
Contains the **core system implementation**, including:

* audio processing pipeline
* ASR integration
* diarization
* summarization and action-item extraction modules.

**README.md**
Provides an overview of the project, repository structure, and instructions for navigating the codebase.

---

# 🚧 Project Status (Current Progress)

✅ **Datasets ingested and validated** (AMI + ICSI)
✅ **EDA completed for feasibility “stress test”** across text + audio datasets
✅ **Baseline vs advanced summarization benchmark** established using ROUGE
🔄 **Current Implementation phase**: Audio → Structured speech units → Incremental intelligence

---

# Problem

Meetings produce critical decisions and tasks, but outcomes are often lost due to:

* inconsistent or incomplete notes
* unclear ownership and deadlines
* manual post-meeting cleanup
* existing tools producing static summaries after the meeting (often requiring correction)

**Goal:** Generate **usable meeting notes during the meeting**, not after.

---

# System Overview

## Module 1 — Audio → Structured Speech Units (Stage 1)

Pipeline:

1. Audio ingestion (uploaded audio now; live mic later)
2. Chunking into 2–3 minute windows
3. ASR (Whisper)
4. Speaker diarization + alignment
5. Output timestamped structured utterances:

```json
{
  "speaker": "SPEAKER_02",
  "text": "I will send the draft by Friday",
  "start_time": 553.2,
  "end_time": 556.9,
  "meeting_id": "M001"
}
```

---

## Module 2 — Intelligence Layer (Stage 2)

Given structured utterances per chunk, an LLM-based system produces:

* rolling meeting summary (context memory)
* action items (task, owner, deadline, priority)
* decisions and risks
* evidence spans for traceability

---

# 📊 Datasets Used

## 1) AMI (Text Summarization)

Dataset: `knkarthick/AMI`
Fields: `dialogue`, `summary`

Use:

* summarization + action extraction development
* ROUGE-based summarization evaluation
* incremental chunk simulation from text

---

## 2) AMI (Audio + Utterance Ground Truth)

Dataset: `edinburghcstr/ami` (`ihm`)
Fields: `audio`, `text`, `begin_time`, `end_time`, `speaker_id`, `meeting_id`

Use:

* ASR evaluation (WER)
* speaker attribution validation
* timing + chunking feasibility analysis

EDA highlights:

* utterance median duration ~1–2 seconds
* meetings typically ~35 minutes
* high interaction intensity (speaker change rate ~0.65)

---

## 3) ICSI (Audio + Speaker Timelines)

Dataset: `argmaxinc/icsi-meetings`

Use:

* stress testing real-world meeting complexity
* meeting duration + chunk count estimation
* multi-speaker density analysis

EDA highlights:

* median meeting duration ~56 minutes
* up to ~100 minute meetings
* ~5 speakers per chunk
* ~84 segments per chunk

---

# EDA “Stress Test” Summary

### AMI Text

* transcripts avg ~5k words
* human summaries highly abstractive
* compression ratio typically <10%

### AMI Audio

* short utterances → must aggregate into windows
* speaker structure useful for evaluation

### ICSI

* longer meetings
* more speakers
* higher conversational density

---

# Model Benchmark

## Baseline — Lead-3 Extractive Summary

Dataset: `knkarthick/AMI` test split

Mean ROUGE-F1:

* ROUGE-1: **0.0503**
* ROUGE-2: **0.0126**
* ROUGE-L: **0.0371**

---

## Model — `philschmid/bart-large-cnn-samsum`

Mean ROUGE-F1:

* ROUGE-1: **0.1315**
* ROUGE-2: **0.0387**
* ROUGE-L: **0.0896**

Improvement over baseline:

* ROUGE-1: **+161%**
* ROUGE-2: **+207%**
* ROUGE-L: **+142%**

---

# Evaluation Plan

## Module 1 — Audio Pipeline

* ASR: Word Error Rate (WER)
* speaker attribution accuracy
* chunk processing latency
* stress testing on ICSI meetings

---

## Module 2 — Intelligence Layer

Metrics:

* ROUGE vs human summaries
* action item extraction precision / recall / F1
* owner attribution accuracy
* deadline extraction accuracy

---

# Roadmap

## Immediate

* implement Module 1 offline
* generate structured utterance JSON
* implement rolling summarization
* run full pipeline on AMI

## Later

* live microphone ingestion
* streaming UI updates
* persistent meeting storage

---

# 🛠 Tech Stack

* **ASR:** Whisper / faster-whisper
* **Diarization:** pyannote
* **LLM reasoning:** structured prompting + JSON outputs
* **Backend (planned):** FastAPI + WebSockets
* **Storage (planned):** PostgreSQL
* **Evaluation:** ROUGE, WER, custom action metrics

---

# Author

**Sai Satwik Yarapotini**
M.S. Applied Data Science
Herbert Wertheim College of Engineering — University of Florida

Email: [saisatwi.yarapot@ufl.edu](mailto:saisatwi.yarapot@ufl.edu)

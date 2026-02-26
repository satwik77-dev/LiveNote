# LiveNote — AI-Driven Meeting Transcription, Summarization & Action Item Extraction

LiveNote is an end-to-end meeting intelligence system that converts meeting audio into **Incremental Transcripts, Summaries, Decisions, and Structured action items**. The system is designed for **near real-time** operation using chunked processing with a human-verifiable output format.

---

## Project Status (Current Progress)

✅ **Datasets ingested and validated** (AMI + ICSI)  
✅ **EDA completed for feasibility “stress test”** across text + audio datasets  
✅ **Baseline vs advanced summarization benchmark** established using ROUGE  
🔄 **Implementation phase starting**: Audio → Structured speech units → Incremental intelligence

---

## Problem

Meetings produce critical decisions and tasks, but outcomes are often lost due to:
- inconsistent or incomplete notes
- unclear ownership and deadlines
- manual post-meeting cleanup
- existing tools producing static summaries after the meeting (often requiring correction)

**Goal:** To generate **usable meeting notes during the meeting**, not after.

---

## System Overview

### Module 1 — Audio → Structured Speech Units (Stage 1)
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
````

### Module 2 — Intelligence Layer (Stage 2)

Given structured utterances per chunk, an LLM-based core produces:

* Rolling meeting summary (context memory)
* Action items (task, owner, deadline, priority)
* Decisions and risks
* Evidence spans for traceability

---

## Datasets Used :

### 1) AMI (Text Summarization)

**Dataset:** `knkarthick/AMI`
**Fields:** `dialogue`, `summary`
**Use:**

* summarization + action extraction development
* ROUGE-based summarization evaluation
* incremental chunk simulation from text

---

### 2) AMI (Audio + Utterance Ground Truth)

**Dataset:** `edinburghcstr/ami` (`ihm`)
**Fields:** `audio`, `text`, `begin_time`, `end_time`, `speaker_id`, `meeting_id`
**Use:**

* ASR evaluation (WER)
* speaker attribution validation
* timing + chunking feasibility analysis for near real-time pipeline

Key EDA highlights:

* utterance median duration ~1–2 seconds
* meetings typically ~35 minutes
* high interaction intensity (speaker change rate ~0.65)

---

### 3) ICSI (Audio + Speaker Timelines)

**Dataset:** `argmaxinc/icsi-meetings`
**Fields:** `audio`, `timestamps_start`, `timestamps_end`, `speakers`
**Use:**

* stress testing real-world meeting complexity
* meeting duration + chunk count estimation
* multi-speaker complexity per chunk

Key EDA highlights:

* median meeting duration ~56 minutes (up to ~100 minutes)
* ~19–20 chunks per meeting with 3-minute windows
* median ~5 speakers per chunk
* median ~84 segments per chunk

---

## EDA “Stress Test” Summary (Feasibility Evidence)

### AMI Text (Summarization Feasibility)

* Meeting transcripts are long (avg ~5k words, up to ~10k)
* Human summaries are highly abstractive
* Compression ratios heavily skewed toward <10%
* Strong action-oriented language density across meetings

### AMI Audio (Streaming Feasibility)

* Utterances are very short → requires aggregation into windows
* Speaker structure is consistent and useful for validation

### ICSI (Real-World Robustness)

* Longer meetings, more speakers, higher conversational density
* Validates the need for rolling memory and speaker-aware reasoning

---

## Baseline vs Model Benchmark (Midpoint Modeling Evidence)

### Naive Baseline: Lead-3 Extractive Summary

**Method:** first 3 sentences of dialogue
**Dataset:** `knkarthick/AMI` test split (n=28)
**Mean ROUGE-F1:**

* ROUGE-1: **0.0503**
* ROUGE-2: **0.0126**
* ROUGE-L: **0.0371**

### Advanced Model: `philschmid/bart-large-cnn-samsum`

**Method:** pretrained transformer dialogue summarizer
**Note:** truncated input (~3000 chars) due to model context limit
**Mean ROUGE-F1:**

* ROUGE-1: **0.1315**
* ROUGE-2: **0.0387**
* ROUGE-L: **0.0896**

**Improvement over baseline:**

* ROUGE-1: **+161%**
* ROUGE-2: **+207%**
* ROUGE-L: **+142%**

---

## Evaluation Plan (Modules)

### Module 1 Validation (Audio Pipeline)

* **ASR:** Word Error Rate (WER) on AMI audio vs reference transcript
* **Speaker attribution:** alignment accuracy using AMI `speaker_id`
* **Latency:** chunk processing time + end-to-end delay per update
* **Stress test:** run timing + multi-speaker load analysis on ICSI

### Module 2 Validation (Intelligence Core)

* **Summarization:** ROUGE vs human summaries (AMI text)
* **Actions/Decisions:** manual gold subset (10–15 meetings) for:

  * Precision / Recall / F1
  * Owner attribution accuracy
  * Deadline extraction accuracy
* **Trust layer impact:** compare hallucination/correction rate before vs after validation rules

---

## Roadmap (Next Steps)

### Immediate (Midpoint → Final build)

* Implement Module 1 offline using uploaded audio (chunk simulation)
* Produce structured utterance JSON per chunk
* Implement Module 2 rolling memory summarization + action extraction
* Run end-to-end pipeline on AMI audio, then stress test on ICSI

### Later (Deployment)

* Add live microphone ingestion (WebRTC/browser)
* WebSocket streaming updates to frontend
* Persist outputs to a database (PostgreSQL) + exportable meeting notes

---

## Tech Stack (Planned)

* **ASR:** Whisper / faster-whisper
* **Diarization:** pyannote (or offline alternative), plus alignment layer
* **LLM Intelligence:** LLM prompting with JSON schema outputs
* **Backend (planned):** FastAPI + WebSockets
* **Storage (planned):** PostgreSQL + object storage for audio
* **Evaluation:** ROUGE, WER, action metrics on labeled subset

---

## Notes

This repo is currently focused on:

* dataset ingestion + EDA
* establishing baselines
* validating feasibility before full deployment implementation

---

## Author

Sai Satwik Yarapotini
M.S. in Applied Data Science — Herbert Wertheim College of Engineering

```
```

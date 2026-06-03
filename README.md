# 🎙️ Voice-to-Case Ticket Generator & Call QA Monitor

A HIPAA-safe healthcare operations pipeline that transcribes patient call audio, extracts structured clinical entities, auto-generates case tickets, and scores calls against compliance QA criteria — all without sending data to external APIs.

---

## 📌 Project Overview

Healthcare call centers handle thousands of calls daily involving sensitive patient information. This pipeline automates three critical workflows:

1. **Speech-to-Text Transcription** — Converts call audio to text using OpenAI Whisper (fully local, no data leaves the environment)
2. **NLP Entity Extraction** — Extracts structured fields (intent, payer, provider, CPT code, date of service, member ID, date of birth) from transcripts using regex-based NLP
3. **Case Ticket Generation** — Auto-generates structured case tickets with recommended next actions based on detected intent
4. **QA Scorecard** — Automatically scores each call against compliance criteria (recording disclosure, identity verification, next-step confirmation)

---

## 🏗️ Architecture

```
Audio File (MP3)
      │
      ▼
OpenAI Whisper (local transcription)
      │
      ▼
NLP Entity Extraction (regex + keyword matching)
      │
      ├──► Intent Classification (benefits / prior_auth / claim_status / billing / provider_network)
      ├──► Field Extraction (payer, provider, CPT, DOS, member ID, DOB)
      │
      ▼
Case Ticket Generator
      │
      ▼
QA Scorecard (recording disclosed / identity verified / next step requested)
      │
      ▼
Output: extracted_calls.jsonl + qa_report.csv + demo_output.json
```

---

## 🔧 Tech Stack

| Tool | Purpose |
|---|---|
| `OpenAI Whisper` | Local speech-to-text transcription |
| `gTTS` | Text-to-speech for synthetic audio generation |
| `Python (re, json, uuid)` | NLP entity extraction and ticket logic |
| `Pandas` | QA report aggregation and CSV export |
| `Google Colab` | Runtime environment |
| `JSONL / CSV` | Structured output formats |

---

## 📂 Project Structure

```
voice-to-case-pipeline/
│
├── Voice_to_case_pipeline.ipynb   # Main pipeline notebook
├── demo_call.mp3                  # Sample synthetic audio file
│
└── out/
    ├── extracted_calls.jsonl      # Full pipeline output per call
    ├── qa_report.csv              # QA scorecard summary
    └── demo_output.json           # Single call demo output
```

---

## 🚀 How to Run

### 1. Install Dependencies
```bash
pip install openai-whisper gTTS pandas
apt-get install -y ffmpeg
```

### 2. Generate Synthetic Call Data
The notebook generates 100 synthetic healthcare call transcripts covering 5 intent categories across major US payers (Aetna, BCBS, UnitedHealthcare, Cigna, Humana) and Boston-area providers (MGH, Brigham, Beth Israel, Tufts, Lahey).

### 3. Generate & Transcribe Audio
A sample call is synthesized using gTTS and transcribed locally using Whisper `base` model.

### 4. Run Full Pipeline
The pipeline processes all 100 calls end-to-end:
- Extracts structured fields from each transcript
- Generates a case ticket with recommended next action
- Scores each call on 3 QA criteria
- Exports results to JSONL and CSV

---

## 📊 Results

| Metric | Result |
|---|---|
| Intent Prediction Accuracy | **100%** (on synthetic dataset) |
| QA Pass Rate (score = 3/3) | **~75%** |
| Calls with missing fields | ~10-15% (simulated non-compliance) |
| Dataset size | 100 synthetic calls |
| Audio transcription | Local via Whisper (zero external API cost) |

---

## 🏥 Intent Categories

| Intent | Description |
|---|---|
| `benefits` | Coverage verification, deductible/copay, in-network benefits |
| `prior_auth` | Prior authorization status and documentation |
| `claim_status` | Claim denial, appeal steps, payment status |
| `billing` | Invoice clarification, itemized bills, balance disputes |
| `provider_network` | In-network confirmation, directory updates |

---

## 🔒 HIPAA Compliance Design

- All transcription is performed **locally** using Whisper — no audio or transcript data is sent to external APIs
- Member IDs are **masked** (e.g., `***1234`) throughout the pipeline
- QA scoring flags calls missing identity verification steps
- Designed for use in environments with PHI (Protected Health Information) sensitivity

---

## 📋 Sample Output

### Case Ticket
```json
{
  "call_id": "a3f2b1c4",
  "ticket_title": "prior_auth - Aetna",
  "summary": "PRIOR_AUTH request for payer Aetna. Provider: MGH, CPT: 90837, DOS: 2026-02-10.",
  "recommended_next_action": "Check prior auth status and required documentation.",
  "extracted_fields": {
    "intent_pred": "prior_auth",
    "payer": "Aetna",
    "provider": "MGH",
    "dob": "1998-04-10",
    "member_id_masked": "***1234",
    "cpt_code": "90837",
    "date_of_service": "2026-02-10"
  }
}
```

### QA Scorecard
```json
{
  "qa_recording_disclosed": 1,
  "qa_identity_verified": 1,
  "qa_next_step_requested": 1,
  "missing_fields": [],
  "qa_score": 3
}
```

---

## 🔮 Future Improvements

- Replace regex NLP with a fine-tuned NER model (spaCy or BERT) for more robust entity extraction
- Add real-time audio streaming support
- Integrate with EHR/ticketing systems (Epic, Salesforce Health Cloud)
- Add escalation detection for urgent calls
- Expand QA criteria (tone analysis, hold time compliance, script adherence)

---

## 👩‍💻 Author

**Nishthaben Vaghani**
- 🔗 [LinkedIn](https://linkedin.com/in/nishtha0104)
- 🐙 [GitHub](https://github.com/nish0104)
- 🌐 [Portfolio](https://nishthaben-vaghani.vercel.app)

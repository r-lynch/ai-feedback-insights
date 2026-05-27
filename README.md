# AI Customer Feedback Intelligence Pipeline

> **Operational intelligence infrastructure for converting qualitative customer conversations into structured product and revenue signals.**

Built as a demonstration of AI fluency, workflow automation, and operational thinking for the **Impact Strategy & Operations** function at ElevenLabs.

---

## What This Is

Customer interviews contain some of the highest-signal data in any SaaS business. A single conversation can surface a churn risk, reveal a product gap, or unlock a competitive insight. But this data typically lives in disorganised notes, unlistened recordings, and siloed Notion docs — inaccessible to the product, revenue, and operations teams who need it most.

This pipeline bridges that gap.

It takes a raw customer interview transcript, runs it through a structured AI extraction layer, and outputs:

- Classified pain points with severity scoring
- Prioritised feature requests linked to product areas
- Churn risk assessment with reasoning
- Competitor intelligence
- Actionable next steps with owner assignments
- An executive summary ready for leadership review
- A CRM-compatible record for Salesforce or HubSpot

The output isn't just organised notes. It's operational infrastructure.

---

## Live Demo

The app runs without an OpenAI API key using pre-loaded demo data. Three realistic sample transcripts are included covering a range of customer archetypes:

| Transcript | Segment | Churn Risk | Key Themes |
|---|---|---|---|
| Meridian Analytics | Enterprise | High (72%) | API reliability, enterprise support, renewal risk |
| Storyforge Media | SMB | Low (18%) | Team collaboration, integrations, pricing |
| Luminos Education | Enterprise | Critical (88%) | SLA, incident management, EdTech compliance |

---

## Architecture

```
Customer Transcript (text / PDF)
         │
         ▼
┌─────────────────────────┐
│   Streamlit Front-End   │  ← Upload, metadata capture, dashboard
└─────────────────────────┘
         │
         ▼
┌─────────────────────────┐
│   Extraction Engine     │  ← OpenAI GPT-4o with structured prompt
│   utils/extractor.py    │     Returns typed JSON against schema
└─────────────────────────┘
         │
         ▼
┌─────────────────────────┐
│   CRM Formatter         │  ← Flattens nested insight object
│   utils/crm_formatter.py│     Maps to Salesforce field conventions
└─────────────────────────┘
         │
         ▼
┌─────────────────────────┐
│   Theme Analyzer        │  ← Aggregates across multiple records
│   utils/theme_analyzer.py   Surfaces recurring patterns
└─────────────────────────┘
         │
         ▼
┌─────────────────────────┐
│   Dashboard + Export    │  ← Insights, CRM preview, JSON/MD export
│   Tabs 2–5 in main.py   │     Mock Salesforce sync
└─────────────────────────┘
```

**Stack:** Python · Streamlit · OpenAI API (GPT-4o) · pdfplumber

No database. No authentication. No cloud infrastructure required. Designed to be runnable locally in under five minutes.

---

## Extraction Schema

Every transcript is converted to a typed JSON record. Key fields:

```json
{
  "pain_points": [
    {
      "description": "API latency spikes during batch processing cause pipeline failures",
      "severity": "high",
      "category": "Performance",
      "quote": "When it happens, it breaks our pipeline..."
    }
  ],
  "churn_risk": {
    "score": 0.72,
    "level": "High",
    "reasoning": "Customer gave explicit renewal conditions with 5-month deadline..."
  },
  "sentiment": {
    "label": "Mixed",
    "score": 0.38,
    "summary": "Strong product affinity, frustrated with operational gaps..."
  },
  "action_items": [
    {
      "task": "Assign named Technical Account Manager by end of November",
      "owner": "CSM",
      "urgency": "high"
    }
  ],
  "executive_summary": "..."
}
```

Full schema: [`data/extraction_schema.json`](data/extraction_schema.json)

---

## Getting Started

### 1. Clone the repo

```bash
git clone https://github.com/YOUR_USERNAME/ai-feedback-insights.git
cd ai-feedback-insights
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the app

```bash
streamlit run app/main.py
```

The app opens at `http://localhost:8501`.

### 4. Try it

**Without an API key:** Load a sample transcript from the sidebar dropdown. Click "Run Intelligence Pipeline" — demo data loads automatically.

**With an OpenAI API key:** Enter your key in the sidebar (it's not stored). Upload a transcript or paste text. Click "Run Intelligence Pipeline" for live AI extraction.

---

## Project Structure

```
ai-feedback-insights/
│
├── app/
│   └── main.py                     # Streamlit application (all 5 tabs)
│
├── utils/
│   ├── extractor.py                # OpenAI extraction with structured prompt
│   ├── crm_formatter.py            # Insight → CRM record mapping
│   ├── theme_analyzer.py           # Cross-interview pattern aggregation
│   └── mock_crm.py                 # Simulated Salesforce sync response
│
├── data/
│   ├── sample_transcripts/         # 3 realistic interview transcripts
│   │   ├── meridian_analytics_q4_review.txt
│   │   ├── storyforge_media_product_feedback.txt
│   │   └── luminos_education_churn_risk.txt
│   ├── processed_records.json      # Pre-processed records for dashboard
│   ├── demo_output.json            # Demo extraction output (no API key needed)
│   └── extraction_schema.json      # Full JSON schema reference
│
├── .streamlit/
│   └── config.toml                 # Dark theme configuration
│
├── .env.example                    # API key template
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Design Decisions

**Why Streamlit?** Fastest path from Python logic to interactive UI without frontend complexity. The priority here is demonstrating the intelligence layer, not frontend engineering.

**Why GPT-4o?** Higher accuracy on nuanced extraction tasks — particularly churn risk reasoning and competitor sentiment, which require reading between the lines of polite corporate language. GPT-4o-mini available as a cost option.

**Why no database?** Keeping it locally runnable without infrastructure setup. In production, each CRM record would write to a Postgres table or directly to Salesforce via REST API. The architecture is designed to make that swap straightforward.

**Why mock Salesforce?** The integration pattern is demonstrated (field mapping, API response structure) without requiring credentials. A production version would use `simple-salesforce` or the Salesforce REST API with OAuth.

**Low temperature on extraction (0.2):** Structured data extraction benefits from consistency over creativity. The AI should classify, not interpret liberally.

---

## What This Demonstrates

This project was built to show, not tell, the following capabilities:

| Capability | Implementation |
|---|---|
| **Operational thinking** | End-to-end workflow from raw input to CRM record — mirrors how a real ops team would deploy this |
| **AI fluency** | Structured prompt engineering, JSON schema enforcement, temperature tuning, fallback handling |
| **Product intuition** | Extraction schema reflects real CSM/PM needs — churn reasoning, owner assignment, competitor context |
| **Systems thinking** | Modular utility layer, separation of extraction / formatting / aggregation concerns |
| **Clear communication** | Sample transcripts written to reflect realistic customer conversations, not toy examples |

---

## Extending This

**Add audio/video support:** Pipe audio through OpenAI Whisper for transcription before extraction. The pipeline is identical from that point.

**Live Salesforce sync:** Replace `mock_crm.py` with `simple-salesforce` and OAuth credentials. The field mapping in `crm_formatter.py` is already Salesforce-compatible.

**Persistent storage:** Add SQLite (via `sqlite3`) or Supabase to store every processed record. The theme analyzer already supports multi-record aggregation.

**Slack notifications:** Post high-churn-risk records to a CSM Slack channel on extraction. Adds ~15 lines of code.

**Batch processing:** Wrap `extract_insights()` in an async loop to process a folder of transcripts overnight.

---

## Notes on Sample Data

The sample transcripts are fictional but grounded in realistic enterprise SaaS dynamics. Names, companies, and scenarios are invented. Any resemblance to real customers, companies, or individuals is unintentional.

---

## Author

Built by [Your Name] · [LinkedIn](https://linkedin.com/in/yourprofile) · [your@email.com](mailto:your@email.com)

*Candidate project for Impact Strategy & Operations at ElevenLabs.*

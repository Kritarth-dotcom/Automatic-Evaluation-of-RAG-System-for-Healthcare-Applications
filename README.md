
# Automatic Evaluation of RAG System for Healthcare Applications

## Overview
This repository contains an automated evaluation framework for Retrieval-Augmented Generation (RAG) systems tailored to healthcare applications.  
The framework evaluates RAG outputs on four primary axes: **Correctness**, **Faithfulness (Grounding)**, **Hallucination Detection**, and **Clinical Safety**. It supports batch evaluation, per-sample diagnostics, visualization dashboards, and exportable reports.

> NOTE: This project is intended for research and academic use. It is **not** a substitute for clinical judgment. Always consult qualified healthcare professionals for medical decisions.

---

## Features
- Run end-to-end RAG evaluation on medical QA datasets
- Retrieval quality metrics (Precision@k, Recall@k, nDCG)
- Claim-level grounding / faithfulness checks
- Hallucination classification and explanations
- LLM-as-a-judge correctness scoring with rubric prompts
- Safety/risk detection (dosage flags, contraindications, harmful suggestions)
- Streamlit dashboard for visual analysis and comparisons
- Exportable PDF/CSV/JSON reports

---

## Quickstart

### Prerequisites
- Python 3.10+
- An OpenAI-compatible API key (or Azure OpenAI credentials) if using cloud LLMs
- Google service account JSON for scheduling or Sheets integration (optional)
- Optional GPU for faster embedding/model inference

### Install
```bash
# create and activate a virtual environment
python -m venv venv
# macOS / Linux
source venv/bin/activate
# Windows (PowerShell)
.\venv\Scripts\Activate.ps1

# install dependencies
pip install -r requirements.txt
```

### Configuration
Create a `.env` file in the project root with required environment variables:

```env
# OpenAI (or Azure OpenAI)
OPENAI_API_KEY=sk-...
# If using Azure OpenAI:
AZURE_OPENAI_KEY=...
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com/
AZURE_OPENAI_DEPLOYMENT=gpt-4o-mini

# Google APIs (optional, for scheduling/reporting integrations)
GOOGLE_APPLICATION_CREDENTIALS=/path/to/google-service-account.json

# Other options
FAISS_INDEX_PATH=./data/faiss_index
```

### Run the Streamlit Dashboard (recommended)
```bash
streamlit run app.py
```

### CLI Usage (examples)
Evaluate a single dataset and write results:
```bash
python run_evaluation.py --input data/medical_qa_test.jsonl --output results/report.json
```

Compare two RAG configurations:
```bash
python compare_runs.py --run1 results/runConfigA.json --run2 results/runConfigB.json --out results/compare.html
```

---

## Dataset Format
Accepted formats: CSV, JSON, JSONL. Minimal required fields for each sample:

```json
{
  "id": "sample_001",
  "question": "What is the first-line treatment for community acquired pneumonia?",
  "reference_answer": "Empiric antibiotics targeting common pathogens; refer to local guidelines.",
  "context": ["...optional pre-provided context passages..."]
}
```

If the dataset does not include `reference_answer`, the system can still evaluate grounding and hallucination but correctness scoring will be limited.

---

## Output / Report
The evaluation produces:
- `results/<timestamp>_metrics.csv` — per-sample metrics
- `results/<timestamp>_summary.json` — aggregated metrics and metadata
- `reports/<timestamp>_report.pdf` — PDF summary with charts (if PDF export enabled)

Key fields:
- `faithfulness` (0-1)
- `correctness` (0-1)
- `hallucination_score` (0-1)
- `safety_rating` (Low/Moderate/High)

---

## Configuration Options
- `--retriever` : faiss | bm25
- `--embedder` : openai | sentence-transformers
- `--k` : number of retrieved passages
- `--judge_model` : model used for LLM-as-a-judge scoring

---

## Troubleshooting
- **API errors / rate limits**: Check API key validity, usage quota, and network access.
- **FAISS index error**: Ensure the index path is correct and files are accessible.
- **Slow evaluation**: Use batching for embedding generation; enable GPU if available.
- **Strange scores**: Confirm dataset formatting and presence of `reference_answer`.

---

## Development Notes
- Keep evaluation prompts and rubrics versioned (`prompts/rubric_v1.json`).
- Use `data/cache/` for caching embeddings to avoid repeat computation.
- Add unit tests under `tests/` for retrieval, grounding, and scoring modules.

---

## Contributing
Contributions and improvements are welcome. Please open an issue or a pull request with a clear description and test cases.

---

## License
Specify an appropriate license (e.g., MIT) in `LICENSE`.

---

## Contact
Maintainer: Your Name — your.email@example.com

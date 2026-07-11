# AI-Powered-Bid-Response-Engine-CUST-HAckathon-2026

[![Python Version](https://img.shields.io/badge/python-3.10%20%7C%203.11%20%7C%203.12%20%7C%203.13-blue.svg)](https://www.python.org/)
[![Streamlit App](https://img.shields.io/badge/Streamlit-FF4B4B?style=flat&logo=Streamlit&logoColor=white)](https://streamlit.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![NLP Stack](https://img.shields.io/badge/spaCy-en__core__web__sm-09A3D5?style=flat&logo=spacy&logoColor=white)](https://spacy.io/)
[![ML Stack](https://img.shields.io/badge/scikit--learn-Scoring--Engine-F7931E?style=flat&logo=scikitlearn&logoColor=white)](https://scikit-learn.org/)

An advanced, automated procurement, sourcing, and contract management engine designed to streamline and automate responding to **Requests for Proposal (RFPs)**. By leveraging **Natural Language Processing (NLP)**, **Retrieval-Augmented Generation (RAG)**, and **Machine Learning (ML)**, this system automates requirement extraction, capability alignment, compliance grading, win-probability scoring, and tailored proposal drafting.

---

##  System Architecture

The engine is built on a clean, decoupled modular architecture separating the frontend visualization layer from the core processing pipeline:

```
                  ┌─────────────────────────────────────────┐
                  │          Streamlit Web Dashboard        │
                  │   (Interactive Widgets & Visualizations)│
                  └────────────────────┬────────────────────┘
                                       │
                                       ▼
                  ┌─────────────────────────────────────────┐
                  │               Backend API               │
                  │   (Coordinates extraction & matching)  │
                  └────────┬──────────────────────┬─────────┘
                            │                      │
                            ▼                      ▼
             ┌────────────────────────┐  ┌────────────────────────┐
             │  NLP Extractor Engine  │  │  RAG Semantic Matcher  │
             │   (spacy / Regexes)    │  │ (Sentence Transformers)│
             └────────────────────────┘  └──────────┬─────────────┘
                                                    │
                                                    ▼
             ┌────────────────────────┐  ┌────────────────────────┐
             │   Win Scorer (ML)      │  │  Proposal Gen (LLM)    │
             │(RandomForestClassifier)│  │ (Google Gemini API)    │
             └────────────────────────┘  └────────────────────────┘
```

---

##  Key Modules

The core implementation is structured under the [src/](file:///D:/Hackathon/src) directory:

1.  **Document Parser ([parser.py](file:///D:/Hackathon/src/parser.py)):** Automatically extracts clean plaintext from PDF, DOCX, and TXT files, with built-in table parsing and layout handling.
2.  **Requirement Extractor ([extractor.py](file:///D:/Hackathon/src/extractor.py)):** Employs spaCy dependency parsing and contextual regex rules to identify, classify, and isolate mandatory requirements, project deadlines, budgets, and evaluation weightings.
3.  **RAG Capability Matcher ([rag.py](file:///D:/Hackathon/src/rag.py)):** Projects requirement clauses and the capability library into a shared 384-dimensional vector space using `sentence-transformers/all-MiniLM-L6-v2` to compute semantic similarities.
4.  **Compliance Checker ([compliance.py](file:///D:/Hackathon/src/compliance.py)):** Cross-references capabilities against mandatory requirements to generate a Compliance Matrix, highlight gaps, and suggest mitigation strategies.
5.  **Win Scorer ([scorer.py](file:///D:/Hackathon/src/scorer.py)):** Employs a trained Random Forest model predicting the historical likelihood of winning a bid based on 8 key feature variables.
6.  **Proposal Generator ([generator.py](file:///D:/Hackathon/src/generator.py)):** Integrates with Google's Gemini API to synthesize and generate draft text for 7 key sections of the proposal, using context injection.

---

##  Project Structure

```
.
├── data/                            # Raw and processed dataset files
│   ├── capability_library.csv       # Past projects and corporate capabilities
│   ├── evaluation_criteria.csv      # Mapped criteria per industry sector
│   ├── historical_bids.csv          # Bid outcomes used for ML training
│   └── workspaces/                  # Streamlit application saved workspaces
│       └── Default.json             # Pre-configured workspace demo state
├── models/                          # Trained ML models and caches
│   └── win_probability_model.pkl    # Serialized RandomForest classifier
├── src/                             # Backend engine modules
│   ├── compliance.py                # Compliance matrix and gap analysis
│   ├── extractor.py                 # NLP parser and requirements classifier
│   ├── generator.py                 # LLM proposal synthesis and fallbacks
│   ├── parser.py                    # File parsing helper (PDF/DOCX/TXT)
│   ├── rag.py                       # Vector database & similarity search
│   ├── scorer.py                    # Win probability inference
│   └── utils.py                     # Scoring utilities & helpers
├── tests/                           # Integrated test suite
│   ├── test_engine.py               # Unit tests for core functions
│   └── test_extra.py                # Mocks and validation tests
├── utils/                           # Admin and setup utilities
│   ├── generate_data.py             # Synthetic data generation engine
│   ├── process_user_data.py         # Org excel-to-csv processing parser
│   ├── setup_project.py             # Automated workspace initialization script
│   └── validate_data.py             # CSV integrity constraints check
├── app.py                           # Main Streamlit web application
├── train.py                         # ML Model training workflow script
├── requirements.txt                 # Application python dependencies
├── .env.example                     # Environment configuration template
├── .gitignore                       # Standard git ignore definitions
├── LICENSE                          # MIT License details
└── README.md                        # Project documentation overview
```

---

##  Offline Resiliency & Fallbacks

Designed for high reliability, the engine includes automatic fallback protocols to handle offline environments, API limits, and missing models:
- **Offline RAG Vectorization:** If the Sentence-Transformers model cannot download or load, the engine instantly falls back to a localized **TF-IDF Vectorizer** with Cosine Similarity matching.
- **Missing ML Classifier:** In the absence of a compiled Random Forest model, the scorer falls back to a **weighted heuristic model** mimicking the Random Forest parameters to continue scoring bids.
- **LLM Rate-Limitation / Offline:** If the Google Gemini API key is missing or fails (due to safety filters or rate limits), the proposal generator falls back to a **localized Markdown Template Engine** that populates placeholders with matched capabilities and extracted requirements.

---

##  Getting Started

### 1. Prerequisites
- Python 3.10 to 3.13
- Git

### 2. Installation & Virtual Environment Setup
Clone the repository and set up a virtual environment:

```bash
# Clone the repository
git clone https://github.com/your-username/bid-response-engine.git
cd bid-response-engine

# Create and activate virtual environment
python -m venv .venv
.venv\Scripts\activate      # Windows
source .venv/bin/activate    # macOS/Linux

# Install required dependencies
pip install -r requirements.txt
```

### 3. Automated Project Setup
Execute the automated setup script to download required NLP models, parse the source Excel dataset (or generate high-quality synthetic data if missing), run data validation, and train the Random Forest model:

```bash
python utils/setup_project.py
```

### 4. Launch the Dashboard
Run the Streamlit web dashboard:

```bash
python -m streamlit run app.py
```
Your default browser will launch and load the application interface at `http://localhost:8501`.

---

##  Running Tests

Validate codebase integrity and test coverage by running pytest:

```bash
python -m pytest tests/
```

---

## 📄 License
This project is licensed under the MIT License - see the [LICENSE](file:///D:/Hackathon/LICENSE) file for details.

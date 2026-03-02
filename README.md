# 🛂 Amar Passport — AI Agent

A **multi-agent AI system** built with [CrewAI](https://crewai.com/) that automates Bangladesh passport eligibility checking, fee calculation, and document checklist generation. Given a user's profile (age, profession, urgency, location), the system produces a bilingual **Passport Readiness Report** in English (Markdown table) and Bangla.

---

## 📌 Overview

| Item | Details |
|---|---|
| **Framework** | CrewAI 1.10.0 (multi-agent, sequential process) |
| **LLM** | Google Gemini `gemini-2.5-flash-lite` (free tier) |
| **Language** | Python 3.12 |
| **Notebook** | `amar_passport_agent.ipynb` |
| **Output** | Bilingual Passport Readiness Report (English + বাংলা) |

---

## 🤖 Agents

The system uses three specialized agents working in sequence:

### 1. 🧑‍⚖️ Policy Guardian (`policy_guardian`)
- Fetches current Bangladesh passport policy (via DuckDuckGo search with local DB fallback)
- Determines permitted validity based on applicant age:
  - Under 18 → max **5 years**, requires **Birth Registration**
  - 18–65 → max **10 years**, requires **NID Card**
  - Over 65 → max **5 years**, requires **NID Card**
- Flags **policy violations** (e.g., minor requesting 10-year passport)
- Recommends delivery type based on urgency (Regular / Express / Super Express)

### 2. 💰 Fee Calculator (`fee_calculator`)
- Uses the eligibility decision from Task 1
- Looks up the exact 2026 fee from the local database
- Reports total fee in BDT with 15% VAT included

### 3. 📋 Document Architect (`doc_architect`)
- Fetches the customized document checklist based on age, profession, and name-change status
- Compiles all findings into the final bilingual report

---

## 🛠️ Tools

| Tool | Description |
|---|---|
| `fetch_passport_policy` | DuckDuckGo search + local DB fallback for Bangladesh passport rules |
| `calculate_fee` | Looks up 2026 official fee by page count, validity, and delivery type |
| `get_required_documents` | Returns customized document list by age/profession/name-change |

---

## 📊 Fee Structure (2026, incl. 15% VAT)

| Pages | Validity | Regular | Express | Super Express |
|---|---|---|---|---|
| 48 | 5 yr | 4,025 BDT | 6,325 BDT | 8,625 BDT |
| 48 | 10 yr | 5,750 BDT | 8,050 BDT | 10,350 BDT |
| 64 | 5 yr | 6,325 BDT | 8,625 BDT | 10,925 BDT |
| 64 | 10 yr | 8,050 BDT | 10,350 BDT | 12,650 BDT |

---

## 🧪 Test Scenarios

Three scenarios are included to validate the agent pipeline:

| # | Profile | Expected Result |
|---|---|---|
| **1** | Age 24, private sector, 64-page, urgent (Express) | 10-year, Express, **10,350 BDT**, NID |
| **2** | Age 15, student, requested 10-year, 48-page, Regular | ⚠️ **POLICY VIOLATION** → capped at 5-year, **4,025 BDT**, Birth Registration |
| **3** | Age 35, BCS govt officer, name change, 48-page, Regular | 10-year, Regular, **5,750 BDT**, NOC + NID + Marriage Certificate |

---

## 📁 Project Structure

```
Assignment_Building_Amar Passport_AI Agent/
├── amar_passport_agent.ipynb   # Main notebook (agents, tasks, scenarios)
├── .env                         # API keys (not committed to git)
├── .venv/                       # Python virtual environment
└── README.md                    # This file
```

---

## ⚙️ Setup

### 1. Clone / open the project
```bash
cd "Assignment_Building_Amar Passport_AI Agent"
```

### 2. Create and activate virtual environment
```bash
python -m venv .venv
source .venv/bin/activate        # Linux / macOS
# .venv\Scripts\activate         # Windows
```

### 3. Install dependencies
```bash
pip install crewai[google-genai] duckduckgo-search python-dotenv
```

### 4. Configure API key
Edit `.env` with your Gemini API key:
```env
GEMINI_API_KEY=your_api_key_here
CREWAI_TELEMETRY_OPT_OUT=true
```
Get a free key at [aistudio.google.com](https://aistudio.google.com/app/apikey).

### 5. Run the notebook
Open `amar_passport_agent.ipynb` in VS Code or Jupyter and run all cells top-to-bottom.

---

## 📤 Sample Output

```markdown
| Field            | Details                                      |
|------------------|----------------------------------------------|
| Applicant Age    | 24                                           |
| Permitted Validity | 10 years                                   |
| Delivery Type    | Express                                      |
| Total Fee (BDT)  | 10350                                        |
| Required ID      | NID                                          |
| Documents Needed | NID Card, Application Summary, Payment Slip  |
| Policy Notes     | None                                         |

**প্রয়োজনীয় তথ্যের সারাংশ:**
- বৈধতা: ১০ বছর | ফি: ১০,৩৫০ টাকা | নথি: এনআইডি কার্ড, আবেদন সারাংশ, পেমেন্ট স্লিপ
```

---

## 📦 Key Dependencies

| Package | Version | Purpose |
|---|---|---|
| `crewai[google-genai]` | 1.10.0 | Multi-agent orchestration + Gemini integration |
| `google-genai` | 1.49.0 | Gemini API SDK |
| `duckduckgo-search` | 8.1.1 | Web search tool (with local DB fallback) |
| `python-dotenv` | 1.1.1 | Load `.env` API key |

---

## ⚠️ Notes

- **Free tier limits**: `gemini-2.5-flash-lite` allows ~5 RPM. The notebook includes a 90-second sleep between scenarios to avoid rate-limit errors.
- **DuckDuckGo fallback**: If the web search returns no results, a local policy database is used automatically.
- **API key security**: Never commit `.env` to a public repository. Add it to `.gitignore`.

---

## 📝 Assignment

*Building Amar Passport AI Agent — demonstrating multi-agent AI systems with CrewAI and Google Gemini for a real-world Bangladesh e-passport workflow.*

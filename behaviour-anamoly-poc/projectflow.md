# Core Anomaly Analytics Hub

A real-time banking fraud detection and behavioral telemetry platform built with FastAPI, SQLAlchemy, and LLM-powered risk analysis. This backend engine continuously monitors user sessions, evaluates user behavior through heuristic rules and biometrics, scores security risks using AI, and automates incident escalations.

---

## 🚀 Key Features

* **Real-Time Telemetry Ingestion:** Processes high-frequency frontend event batches (navigation, clicks, keystrokes).
* **Behavioral Anomaly Detection:** Flags suspicious patterns like bulk data exfiltration, rapid sensitive actions, direct route access, and credential manipulation.
* **Keystroke Dynamics Analysis:** Detects automated bot script injections, unrealistic flight times, and social engineering coercion/hesitation.
* **Hybrid AI Risk Evaluation:** Integrates OpenRouter LLMs (GPT-4o-mini) alongside an intelligent rule-based fallback analyzer using evidence-based signal weights.
* **Automated Incident Response:** Triggers email verification callbacks (Yes/No security confirmations) and logs security incidents for high-risk sessions.

---

## 📂 Project Directory Structure

```text
app/
├── __init__.py
├── config.py             # Environment configurations & settings
├── database.py           # SQLAlchemy database connection & session setup
├── models.py             # ORM database models (Incident, TelemetryEventLog, SessionTelemetry)
├── schemas.py            # Pydantic request/response validation models
├── fraud_pattern.py      # Catalogue of fraud typologies, signal weights, and baselines
├── routers/
│   ├── incident.py       # Operations portal endpoints for viewing incidents
│   ├── telemetry.py      # Core ingestion, anomaly detection, and risk analysis router
│   └── verification.py   # User email callback verification handler (Yes/No response)
└── services/
    ├── ai_service.py     # OpenRouter AI integration with rule-based fallback
    └── email_service.py  # SMTP email dispatch for security alerts
main.py                   # FastAPI app initialization and route registration


## 🔄 End-to-End Code Flow (Telemetry Lifecycle)

When the frontend flushes a batch of actions to `POST /api/v1/fraud/telemetry/events`, the data flows through **5 key processing steps**:

1. **Persistence (TelemetryEventLog):** Every individual event (e.g., `TRANSFER`, `VIEW_DASHBOARD`, `KEYSTROKE_DYNAMICS`) is saved sequentially into the database with its metadata, sequence numbers (`seq`), and timestamps.

2. **Heuristic Anomaly Detection (detect_anomalies):** The action stream is evaluated against multiple fraud indicators and behavioral patterns (e.g., bulk downloads over limits, rapid sensitive action sequences, navigation anomalies, and bot keystroke speeds).

3. **Historical Baseline Lookup (get_user_database_baseline):** Queries past sessions and events to understand historical transaction baselines while isolating the current active session.

4. **AI & Risk Analysis (analyze_fraud_risk & ai_service.py):** Packages session context, detected anomalies, keystroke summaries, and user history status into an evaluation prompt sent to OpenRouter. If unavailable, it falls back to a deterministic, weight-based rule engine.

5. **Aggregation & Background Escalation:**
   - Updates or creates an aggregated session profile in `SessionTelemetry`.
   - If the final risk score is `> 60 (HIGH risk)`, background tasks dispatch an HTML security alert email to the user with verification links and create a `PENDING` incident record.

---

## 🧠 AI & Risk Scoring Logic

### Risk Bands:

- **LOW (0–39):** Normal activity; allowed automatically.
- **MEDIUM (40–69):** Suspicious activity; flagged for monitoring or step-up authentication.
- **HIGH (70–100):** Severe threat (Account Takeover, Bot attack, Fraudulent transfer); triggers email verification and account escalation.

### Established History Protection:

The evaluation protocol ensures that users with established historical sessions are not unfairly penalized against cold-start baseline rules during standard transactions.

---

## 🔗 Secondary Endpoints & Verification Workflow

### Verification Callback (`GET /api/verification/verify`):

- **"YES, That Was Me":** Resolves the incident status as `VERIFIED_BY_USER`.
- **"NO, Secure My Account":** Escalates the incident, triggers account freezing, and alerts bank forensics.

### Operations Portal (`GET /api/incidents`):

Provides endpoints for compliance and fraud operations teams to review active security incidents and audit session logs.
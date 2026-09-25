# SAS821S_Mhuriyengwe_Tjiurutue_FinalZipfile-
# T13 · SME Managed Security Monitoring and Incident Prioritisation
SAS821S Capstone — Milestone 2 (Project Implementation Plan and Working Prototype)
Group: Mhuriyengwe (223027138) & Tjiurutue (220097615)

## What this is
A working prototype answering one decision: *given today's alerts, tickets and
findings across all monitored SME clients, which should the analyst
investigate first, and why?* Everything below feeds that single dashboard.

## How to run it

```bash
pip install -r requirements.txt --break-system-packages   # if needed

# 1. Generate synthetic multi-client data (reproducible, seed=821)
python 03_notebooks_or_scripts/01_generate_data.py

# 2. Ingest, clean, and build baselines
python 03_notebooks_or_scripts/02_ingest_and_baseline.py

# 3. Train supervised + unsupervised models
python 03_notebooks_or_scripts/03_ml_models.py

# 4. Text mining on ticket data
python 03_notebooks_or_scripts/04_text_mining.py

# 5. Investigation timeline + intelligence outputs
python 03_notebooks_or_scripts/05_investigation_intelligence.py

# 6. Simulation (triage strategy comparison)
python 03_notebooks_or_scripts/06_simulation.py

# 7. Predictive risk forecast + adversarial testing
python 03_notebooks_or_scripts/07_predictive_adversarial.py

# 8. Launch the dashboard
streamlit run 07_dashboard_or_prototype/app.py
```

## Live capture demo (simulated real-time event feed)
Shows the trained models scoring new, never-before-seen events one at a time,
as if arriving live — good for a Milestone 4 walkthrough.

Terminal version (great to run live during the presentation):
```bash
cd 03_notebooks_or_scripts
python 08_live_capture_simulator.py --n 25 --interval 1.5
```
In-browser version: the dashboard's "🔴 Live Capture" tab does the same thing
with a visible, auto-updating table. Both are simulated arrivals (not a real
network feed) but use the actual trained classifier and text-mining models —
see 03_notebooks_or_scripts/live_capture_lib.py for the shared scoring logic.

## Running the tests
```bash
pip install pytest --break-system-packages   # if needed
pytest tests/ -v
```
39 automated checks covering data integrity, model sanity, and dashboard
dependencies. See `09_documentation/known_defects_and_backlog.md` for known
limitations and the remaining backlog before Milestone 3.

Scripts must be run in order once (1→7) since each stage writes files the
next stage reads from `02_data/processed/` and `08_outputs/`. Re-running is
safe and idempotent (same seed = same results).

## Folder structure
```
02_data/raw/          synthetic source data (alerts, vuln scans, auth logs, tickets)
02_data/processed/    cleaned + scored data used by later stages
03_notebooks_or_scripts/  pipeline scripts, run in numeric order
04_models/            saved trained models (joblib)
05_simulation/         (simulation outputs are written to 08_outputs/)
06_text_mining/         (text mining outputs are written to 08_outputs/)
07_dashboard_or_prototype/app.py   Streamlit analyst dashboard (C10)
08_outputs/            all metrics, charts, CSVs, JSON produced by the pipeline
09_documentation/      final write-up (Milestone 3) + known_defects_and_backlog.md
tests/                  automated test suite (pytest)
```

## Coverage of Section 5 requirements (C1–C10)
| Code | Where |
|---|---|
| C1 | Problem framing throughout; this README + dashboard tabs mirror the lifecycle |
| C2 | 4 sources in `02_data/raw/`: alerts (network/endpoint), vuln scans, auth logs (identity/access), tickets (text) |
| C3 | `02_ingest_and_baseline.py` → data dictionary, cleaning log, baselines in `08_outputs/` |
| C4 | `03_ml_models.py` → RandomForest (supervised) + IsolationForest (unsupervised) |
| C5 | `05_investigation_intelligence.py` → `incident_timeline.csv` |
| C6 | `05_investigation_intelligence.py` → `operational_priority_queue.csv`, `executive_risk_digest.csv` |
| C7 | `06_simulation.py` → 1,500-iteration Monte Carlo, 3 triage strategies |
| C8 | `04_text_mining.py` → category/urgency classification + entity extraction |
| C9 | `07_predictive_adversarial.py` → forecast + 3 adversarial tests |
| C10 | `07_dashboard_or_prototype/app.py` → Streamlit dashboard |

## Known limitations (to expand in Milestone 3 documentation)
- All data is synthetic; realism is bounded by the generation logic in `01_generate_data.py`.
- Text classification metrics (near-perfect) are an artefact of templated
  ticket generation, not a genuine real-world performance claim.
- Supervised alert classifier accuracy (~64%) reflects genuine label noise
  built into the data generation (each rule has an inherent false-positive
  rate) - this is closer to a realistic ceiling than a modelling weakness.
- Adversarial Test 2 shows detection genuinely degrades under low-and-slow
  attack spreading (100% → ~10-27% detection) - a real limitation of
  volume-based per-client anomaly detection worth discussing in governance
  recommendations.
- Simulation assumes constant per-alert review time; does not model analyst
  fatigue or interruptions.

## AI assistance disclosure
Generative AI (Claude, Anthropic) was used to help design the architecture,
scaffold and debug the pipeline code, and structure this documentation. All
logic was reviewed, tested, and the results verified by the group.

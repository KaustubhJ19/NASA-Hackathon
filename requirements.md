# Requirements: VITALS-M

Decision IDs (D1–D9) and assumption IDs (A1–A5) refer to `context.md`. Evidence references `[n]` refer to `research.md`.

## 1. Functional requirements

| ID | Requirement | MoSCoW |
|---|---|---|
| F1 | Import or ingest daily wearable summaries: sleep duration, sleep efficiency, sleep midpoint, resting HR, ln(RMSSD), skin-temperature deviation, steps | Must |
| F2 | Daily check-in: mood, stress and fatigue sliders (1–5); symptom checklist; red-flag symptoms; free-text note | Must |
| F3 | 3-minute reaction-time test (based on PVT-B [12]): mean reaction time and lapses (> 500 ms) | Must |
| F4 | Exercise log (minutes per modality) compared against a personal weekly plan | Must |
| F5 | Manual entry of body mass (weekly), dosimeter cumulative dose, BP, SpO2 | Must (mass, dose) / Should (BP, SpO2) |
| F6 | Personal baseline per metric, with a "Calibrating" state until 7 valid days | Must |
| F7 | Deviation engine: Watch / Act levels from robust z-score, EWMA and CUSUM; compound patterns | Must |
| F8 | Urgent level from red-flag symptoms or flight-surgeon-set limits | Must |
| F9 | Dashboard: overall status, per-domain tiles, sparklines with personal band | Must |
| F10 | Metric detail view: 30/90/all-day trend, baseline band, flagged days, source badge, data quality | Must |
| F11 | Evidence card per alert: which rule fired, values, plain-language template summary, next steps | Must |
| F12 | Next-step guidance using a fixed ladder (self-care → CMO → flight surgeon), with placeholder procedure references | Must |
| F13 | CMO view: crew status grid; details shown only with consent (always at Urgent) | Must |
| F14 | Simulated Earth link (Online / Delayed / Blackout); outbox queue; encrypted export packet (file download) | Must |
| F15 | Demo scenario loader (4 synthetic crew, 180 mission days) and a time-scrubber to step through days | Must |
| F16 | Weekly summary report (exportable as Markdown/JSON) | Should |
| F17 | Radiation tile: cumulative dose vs a mission budget configured by the flight surgeon | Should |
| F18 | Trend projection with uncertainty band, labelled "not a forecast" | Could |
| F19 | Import a real wearable export (e.g. a CSV from a team member's watch) | Could |
| F20 | On-device LLM summary | Could (post-MVP) |
| F21 | Real onboard server, multi-device sync, real wearable SDKs, real Earth comms | Won't (MVP) |
| F22 | Diagnosis, treatment instructions, clinical risk scores | Won't, ever |

## 2. Non-functional requirements

| ID | Requirement |
|---|---|
| N1 Offline | 100% of features work with no network after first load (service worker + IndexedDB) |
| N2 Performance | Dashboard renders in < 1 s for 4 crew × 365 days on a mid-range tablet; engine recompute for one astronaut takes < 100 ms |
| N3 Usability | Overall status understandable in < 5 s; check-in finished in ≤ 3 min plus the reaction-time test; touch targets ≥ 44 px |
| N4 Accessibility | WCAG 2.1 AA contrast; severity never shown by colour alone (icon + word + colour); works in dark mode; font scaling to 200% |
| N5 Reliability | Engine is deterministic and pure: same inputs give same outputs; covered by unit tests |
| N6 Explainability | Every flag traceable to a rule, metric values and the baseline window |
| N7 Safety wording | No diagnostic vocabulary; a lint test scans UI strings for banned words |
| N8 Footprint | Static bundle < 2 MB gzipped; no runtime cloud dependency |

## 3. Technology stack (selected)

| Layer | Choice | Justification |
|---|---|---|
| App | **React 18 + TypeScript + Vite** | Team knows it; fast setup; one language for UI and engine |
| Offline | **vite-plugin-pwa** (Workbox service worker) | Installable, works offline |
| Storage | **Dexie.js** over IndexedDB | Simple typed local database in the browser |
| Charts | **Recharts** | Quick to build with; supports reference bands (baseline band) |
| Styling | **Tailwind CSS** | Fast consistent UI; dark mode |
| Engine | Pure TypeScript module `src/engine` | Testable without the UI; could later be ported to an onboard server |
| Crypto | **Web Crypto API** (AES-GCM) | Built into browsers; encrypts export packets |
| Data generator | TypeScript script (`scripts/generate.ts`, run with `tsx`) | Same language; reproducible with a seed |
| Tests | **Vitest** (unit), **Playwright** (end-to-end smoke) | Native to Vite; Playwright already available |
| Hosting | Static host (GitHub Pages / Netlify). *Exact choice: TBD* | Judges get a URL; nothing to run on a server |

**Rejected for the MVP:** a FastAPI onboard server (integration risk within 1 day; kept as the target architecture), a cloud backend (breaks N1), ML frameworks (no training data).

## 4. Data requirements

- Daily record per astronaut per metric: `{crewId, day, metric, value, source, quality}`. Full schema in `design.md` §9.
- Synthetic crew: 4 astronauts × 180 days. Baselines drawn from plausible healthy ranges; ISS-reported sleep of about 6–6.5 h [22]; exercise about 600 min/week [25]. Realistic noise, missing days (~5%) and weekly rhythm.
- Injected scenarios (ground truth labelled): S1 sleep restriction → slower reactions; S2 illness-like pattern (resting HR ↑, temperature ↑, HRV ↓, symptoms) → red-flag symptom; S3 activity and exercise decline plus mood decline (pattern inspired by Mars-500 [14]); S4 control crew member with no injected change.
- Open datasets are used only for sanity checks (PhysioNet sleep for night-to-night variability [17]; WESAD for HRV shape [7]). Non-commercial terms apply.

## 5. Analytics / AI requirements (Phase 5 evaluation)

| Feature | Problem | Approach chosen | Why not ML | False-positive / false-negative risk | MVP? |
|---|---|---|---|---|---|
| Personal baseline | What is "normal for me" | Rolling 21-day median ± 1.4826·MAD (robust z) | Small number of days per person; median/MAD is robust and explainable | Too-short baseline → noisy; mitigated by Calibrating state | Must |
| Anomaly detection | Real, sustained change vs noise | EWMA (λ = 0.2) on z-scores for drift; CUSUM (k = 0.5, h = 4) for persistent shifts [28][33] | Deterioration events are too rare for supervised ML [34] | False positives cause alert fatigue → two tiers + persistence rule; false negatives → red-flag symptoms always override | Must |
| Compound patterns | Multi-system signals | Rules: e.g. resting HR↑ + HRV↓ + temperature↑ for ≥ 2 days | Rules are transparent | Pattern ≠ cause; wording stays neutral | Must |
| Sleep quality | Chronic restriction, drift | Duration, efficiency, midpoint regularity versus baseline | Plain metrics are enough | Actigraphy overestimates sleep (unverified) → shown as "estimate" | Must |
| Cardiovascular trend | Deconditioning / illness hints | Resting HR, ln(RMSSD) 7-day rolling vs baseline [20] | Same | Device-specific HRV → relative comparison only | Must |
| Behavioural trend | Mood, cognition decline | Sliders + reaction-time test vs baseline; first 3 tests excluded (practice effect [13]) | Same | Under-reporting; consent model | Must |
| Natural-language summary | Readability | Templates filled with values | LLM may hallucinate; needs compute | Low | Must (templates) |
| Trend projection | Where is this heading | Linear fit + prediction interval, last 14 days | — | Over-trust → "not a forecast" label | Could |
| Learned anomaly model (isolation forest etc.) | Multivariate oddities | — | Not explainable; no validation data | High | Won't |

**Validation:** run the engine over the synthetic crew. Report, per scenario, the detection delay (days from injection to Act) and the false-alarm rate (Watch/Act days for S4 and outside injected windows). These are *engineering checks on synthetic data*, never claims of clinical accuracy.

## 6. Hardware integration assumptions

- Wearable gives nightly summaries (A1). Real wearable SDKs are out of scope; we define an import JSON/CSV contract instead.
- Dosimeter, body-mass device, BP cuff, pulse oximeter: manual entry (A2).
- Tablet-class device with a modern Chromium/WebKit browser.

## 7. Security and privacy requirements

| ID | Requirement |
|---|---|
| S1 | Per-astronaut profile with a PIN lock (demo-grade); role = astronaut or CMO |
| S2 | Consent flags per astronaut: `shareDetailsWithCMO`, `shareSummaryWithEarth`; Urgent overrides detail-sharing and is logged |
| S3 | Export packets encrypted with AES-GCM; in the demo the key is a pre-shared passphrase |
| S4 | Audit log of every access to another person's details and every export |
| S5 | Data minimisation: Earth packets carry daily aggregates and flags, not raw streams |
| S6 | No third-party network calls at runtime; no analytics or tracking scripts |
| S7 | Clear "Not a medical device" notice on first run and in the footer |

Known demo limitation: IndexedDB is not encrypted at rest in the MVP (documented; production would need OS-level device encryption plus a key held by the user).

## 8. Testing and validation

- Unit tests (Vitest) for `baseline`, `ewma`, `cusum`, `rules`, `status` (fixed fixtures; edge cases: missing days, constant series where MAD = 0, < 7 days of data).
- Scenario tests: S1–S3 reach Act within the expected days; S4 stays at Normal or Watch for ≥ 95% of days.
- Wording lint: UI strings contain no banned diagnostic terms.
- Playwright smoke test: load demo → check-in → alert evidence card → toggle blackout → export.
- Offline test: load, switch off the network in Playwright, reload, app still works.

## 9. MVP scope (1-day build)

**In:** F1–F15, N1–N8, S1–S7, the four synthetic scenarios, demo script.
**Stretch, in order:** F16 weekly report → F17 radiation budget → F18 projection → F19 real import.

## 10. Acceptance criteria (demo-ready)

1. Fresh load → "Load demo mission" → dashboard for 4 crew in < 2 s.
2. Scrubbing to S2's injection day + 3 shows Act on the cardio/immune tile, with an evidence card naming the metrics, their values and the rule.
3. Completing a check-in with a red-flag symptom shows the Urgent card and flags the CMO grid.
4. With Blackout on and the network off, check-in, dashboard and alerts all work; the outbox shows queued packets; switching to Online "delivers" them after the simulated delay.
5. The control crew member (S4) shows no Act alerts.
6. No banned words in the UI (lint passes); all unit and scenario tests are green.

## 11. Implementation roadmap (5 people, about 1 day)

| Phase | Hours | Deliverable | Owner | Acceptance |
|---|---|---|---|---|
| 1–3 Research, solution, architecture | done | These six docs | all | Reviewed by team |
| 4 UI prototype | H0–H1 | Paper/Figma sketch of dashboard + evidence card | P3 | Team agrees layout |
| Contracts | H0–H1 | `src/types.ts` (schema from `design.md` §9), repo scaffold, CI test script | P1 | Builds; everyone pulls |
| 5 Data | H1–H4 | `scripts/generate.ts`, `public/demo/mission.json` with ground-truth labels | P2 | 4 × 180 days; scenarios visible in a quick plot |
| 6/8 Engine | H1–H5 | `src/engine/*` + unit tests | P1 | Tests green; scenario metrics computed |
| 7 Frontend | H1–H6 | Dashboard, detail, evidence card (P3); check-in, reaction test, symptoms (P4) | P3, P4 | Works on fixture data |
| 7 Ops features | H1–H6 | CMO grid, consent, comms simulator, outbox, encrypted export, PWA | P5 | Works offline |
| 9 Integration | H6–H8 | Real engine + generated data wired in | all | Acceptance 1–5 pass |
| 10 Testing | H8–H9 | Scenario tests, wording lint, Playwright smoke | P1, P2 | Green |
| 11 Demo | H9–H12 | Deployed URL, 2–3 min video, slides from these docs | P5 + P3 | Submitted before deadline with buffer |

Skills/tools per phase: `writing-plans` (turn this into a task plan), `tdd` (engine), `frontend-design` + `dataviz` (UI), Playwright (end-to-end), `verification-before-completion` (before claiming done), Artifact tool (shareable demo page).

## 12. Critical review

- **Solves the challenge?** Yes: it gathers indicators, evaluates them against the person's own baseline, and turns that into action through the next-step ladder, for astronauts acting alone.
- **Realistic measurements?** Mostly. Bone, immune and radiation are proxies or logs, and the UI says so. HRV is device-dependent, so only relative comparisons are made.
- **Stack too complex?** No. One app, one language, no server.
- **Meaningful demo with available data?** Yes, through injected scenarios with ground truth. The weakness is that the data is synthetic, and we say so openly.
- **Scientifically defensible?** The methods (robust baselines, EWMA/CUSUM) are standard and have terrestrial evidence. Spaceflight validation is an acknowledged gap.
- **Comms outages?** By design: offline-first, no dependency on Earth.
- **Safety and privacy?** No diagnosis, red flags always escalate, consent model, encrypted export. Encryption at rest is a known gap.
- **Achievable?** Yes if the stretch goals stay stretch goals.

### Top 5 risks

| # | Risk | Mitigation |
|---|---|---|
| R1 | Integration slips; the demo is broken at the deadline | Types contract at H0; fixture data so UI and engine work in parallel; integrate by H8; feature freeze H9 |
| R2 | Too many false alarms make the demo look noisy | Tune parameters on S4 (control); two tiers; persistence rule; report false-alarm rate honestly |
| R3 | Judges see an unvalidated medical claim | Neutral wording, lint, "Not a medical device", evidence labels, research.md caveats |
| R4 | Cited numbers turn out wrong (excerpt-only research) | Verify the ~6 numbers used in the pitch by opening the sources; cut anything we can't verify |
| R5 | Offline/PWA caching bugs during the live demo | Test offline in Playwright; record a backup demo video |

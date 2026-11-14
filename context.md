# Project Context: VITALS-M (working name)

> **Vital Indicators Tracking for Autonomous Long-duration Spaceflight – Mars.** An offline-first personal-baseline health monitor for astronauts.
> This file is the handoff for any developer or AI agent joining the project. Read it first, then `requirements.md` → `architecture.md` → `design.md`. Evidence lives in `research.md`.

## 1. Problem background

Long missions expose crews to radiation, altered gravity, isolation and confinement, and a closed environment. These bring documented changes in bone, cardiovascular function, the immune system, sleep and behavioural health (`research.md` §2). On a Mars transit, Earth is minutes away by signal and sometimes unreachable, so **astronauts must notice and act on changes in themselves.**

**NASA Space Apps challenge:** build health monitoring software that gathers health indicators and lets astronauts evaluate and act on their health status.

## 2. Objectives

1. Collect physiological, psychological and environmental indicators. Each one is labelled as Measured, Self-reported, Derived or Lab/Equipment-only.
2. Learn each astronaut's **personal baseline** and detect sustained deviations from it.
3. Show status, trends and alerts that a stressed or tired person can read in under 5 seconds.
4. Suggest **next steps** (self-care → crew medical officer → flight surgeon). Never diagnose or prescribe.
5. Work fully offline. Earth sync is a delayed, optional extra.
6. Keep each astronaut in control of their own data.

## 3. Personas

| Persona | Need | Key decision |
|---|---|---|
| **Astronaut (primary)**: mission specialist, day 140 of transit, tired | "Am I OK? Is this change real or noise?" | Rest, adjust, keep watching, or tell the crew medical officer |
| **Crew Medical Officer (CMO)**: a crewmate with medical training, not a doctor | "Who needs attention today, and how urgently?" | Check in, run onboard exams, consult Earth |
| **Flight surgeon (Earth, secondary)**: receives delayed packets | "What changed, since when, how confident are we?" | Change countermeasures or the plan, by delayed message |
| **Hackathon judge** | "Is this real, safe and new?" | Scores the project |

## 4. Brainstorming outcomes and decisions

Three concepts were compared in a weighted matrix (relevance, scientific validity, feasibility, data, demo, innovation, complexity):

- **A. Personal Baseline Sentinel: 86%, selected**
- C. Behavioural Health Companion: 72%; its check-in and reaction-time test are folded into A
- B. Physiological Digital Twin: 55%; rejected because there is no flight data to validate forecasts. It survives only as a "trend projection" stretch goal, labelled *not a forecast*.

| # | Decision | Why |
|---|---|---|
| D1 | Scenario: **Mars transit** | Signal delay and blackouts make offline autonomy essential, not optional |
| D2 | Users: astronaut + CMO on board; flight surgeon by delayed export | Matches the challenge's "astronauts act for themselves" framing |
| D3 | Analytics: **transparent statistics first** (robust baseline, EWMA, CUSUM, rules). No black-box risk score | Rare events and no flight training data. Explainability is a safety requirement (`research.md` §7) |
| D4 | Summaries come from **templates**, not an LLM | Offline, predictable, can't hallucinate. An on-device LLM is a stretch goal |
| D5 | Demo data: **synthetic crew** seeded from published ranges and patterns, with injected scenarios. Open datasets (WESAD, PhysioNet sleep) used only to sanity-check algorithms | No public astronaut vitals stream exists (`research.md` §5) |
| D6 | **Single offline PWA** (React + TypeScript) holding the engine; IndexedDB storage; Earth link simulated in the app | Deadline is 1 day. Fewest moving parts. Onboard server documented as target architecture |
| D7 | Check-in uses **our own reaction-time test (based on PVT-B) and our own unvalidated 1–5 sliders** | KSS needs written permission; PHQ/GAD/PANAS/POMS licensing unclear (`research.md` §4) |
| D8 | Urgent level comes only from **red-flag symptoms the astronaut reports** or **limits set by the flight surgeon** (demo values clearly labelled) | We have no verified clinical cut-offs and no medical team member |
| D9 | Astronaut owns the data. The CMO always sees status level; details need consent, except at Urgent | Under-reporting risk if crew fear grounding (`research.md` §8.8) |

## 5. Assumptions (flag if they turn out false)

- A1. A wrist or chest wearable provides nightly HR, HRV (RMSSD), skin temperature and actigraphy-based sleep. In the demo this is simulated.
- A2. Dosimeter readings and body mass are entered by hand or imported. The app cannot measure them.
- A3. The crew shares one or more tablets on the spacecraft network. Each astronaut has a login.
- A4. One-way signal delay on a Mars transit ranges from several minutes to about 20+ minutes, with possible blackouts. *This is commonly cited and not yet sourced in research.md.*
- A5. Statistical parameters (window 21 days, EWMA λ = 0.2, CUSUM k = 0.5, h = 4) are **design hypotheses** to be tuned on synthetic data.

## 6. Main user journeys

1. **Morning check-in (about 3 min):** wake → wearable has synced overnight data → mood/stress/fatigue sliders → symptom checklist → 3-minute reaction-time test → status summary.
2. **"Why is this amber?":** tap the alert → evidence card (chart against the personal band, rule that fired, data quality) → suggested next steps → acknowledge / note / share with CMO.
3. **CMO morning review:** crew grid of status levels → open the astronaut who consented → decide on an exam or an Earth consult.
4. **Comms blackout:** Earth link goes dark → app shows "Autonomous mode", all functions keep working, packets queue → link returns → queue sends after the delay.
5. **Urgent:** red-flag symptom reported → full-screen Urgent card → "Contact CMO now" → CMO notified automatically.

## 7. Expected behaviour (non-negotiables)

- Never use diagnostic words ("depression", "infection", "bone loss"). Use "outside your usual range" wording.
- Never say "all clear" or "normal" for anything the app doesn't measure (e.g. vision, bone density).
- Show "Calibrating" until a metric has 7 valid days.
- Every number carries a source badge (Measured / Self-reported / Derived / Lab) and a data-quality indicator.
- Everything works with no network.

## 8. Important limitations

- Not a medical device. Nothing in it is clinically validated. Thresholds and alert text need a physician's review.
- All evidence in `research.md` was seen through search excerpts only; it must be re-verified before quoting.
- Ground (terrestrial) data and synthetic data cannot validate performance on astronauts.
- Bone, immune and radiation effects can only be tracked through proxies or logs.

## 9. Open questions

1. Real CMO / flight-surgeon policy on who sees what data, and when.
2. Permission for KSS, or a licensed mood instrument, for a post-hackathon version.
3. Exact Mars-transit communications profile (source for A4).
4. Physician review of the red-flag symptom list.

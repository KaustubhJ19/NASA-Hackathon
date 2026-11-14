# Research: Offline-First Astronaut Health Self-Monitoring (Mars Transit)

## Evidence supporting major product decisions

| Decision (`context.md`) | Evidence | Strength |
|---|---|---|
| D1 Mars transit, offline autonomy | Long cruise (253 days for MSL) [2]; behavioural risk grows with mission length [8]; signal delay figure A4 not yet sourced | Medium |
| D3 Personal baselines, not population thresholds | Large individual differences in Mars-500 [14][35]; no spaceflight thresholds found (§3, §8.1); terrestrial resting-HR baseline detection [27]; athlete HRV rolling baseline [20] | Medium (terrestrial) |
| D3 EWMA/CUSUM, no supervised ML | CUSUM and EWMA used on wearable data [28][33]; events too rare for supervised ML [34] | Medium (terrestrial) |
| Sleep as a lead domain | ~6 h actual vs 8.5 h scheduled; hypnotic use common [22]; circadian desynchrony [9] | Strong (secondary summaries) |
| Activity/exercise compliance as bone and muscle proxy | ISS ~600 min/week exercise prescription [25]; bone loss 1–1.5%/month, no fracture-prediction validity [3] | Medium |
| Radiation as log only | App cannot measure; dose figures [2][24]; career limit status unverified [23] | Strong for the "log only" choice |
| D7 Own reaction-time test + unvalidated sliders | PVT-B validation [12]; KSS needs permission [18]; other licences unclear [19] | Strong for the licensing caution |
| Vision-change symptom item | SANS common on long missions [21] | Medium |
| D9 Consent-based sharing | Under-reporting risk (ASSUMPTION §8.8) | Assumption |

Items still to verify before the pitch: the radiation rates, bone %/month, sleep 6 h vs 8.5 h, exercise ~600 min/week, the 63% early-detection figure, and the SANS prevalence. Open each source URL and confirm.

Status: working notes for a hackathon team without medical background. NOT medical advice. The app is a personal-baseline deviation tracker, not a diagnostic device.

## 0. How to read this file (IMPORTANT sourcing caveat)

- In this session `WebFetch` was blocked (hosts such as ntrs.nasa.gov, nasa.gov, swri.org, archive.ics.uci.edu failed DNS resolution under the sandbox network policy). All sources below were therefore seen ONLY through `WebSearch` result summaries (title, URL and excerpt), NOT opened in full text. Treat every number as "seen in a search excerpt of the cited page". Before quoting any figure in a pitch, open the cited URL and confirm it. [0]
- Labels used: **VERIFIED** = stated in a cited search excerpt; **ASSUMPTION** = needed for design, not sourced; **DESIGN HYPOTHESIS** = our proposal, untested; **UNVERIFIED** = plausible, not confirmed here (do not quote).
- Several figures were reported by secondary sources (press releases, news, Wikipedia-like pages). These are flagged "(secondary)".
- Reference numbers [n] are listed in Section 9. [0] is this caveat itself.

---

## 1. Research questions

1. **RQ1.** Which of the NASA-tracked spaceflight risks can plausibly be tracked with low-burden, offline, self-collected data (wearable + short self-report), and which need specialised equipment or labs (so the app can only log them)?
2. **RQ2.** What are the published in-flight magnitudes of change (bone, sleep, radiation, immune, cognition) that tell us how large a "meaningful deviation" might be?
3. **RQ3.** Which self-report instruments are validated, short, usable daily/weekly, and legally usable in a free app (licensing)?
4. **RQ4.** Which open datasets exist so we can prototype baseline/deviation algorithms, and how far are terrestrial/analogue data valid substitutes?
5. **RQ5.** Which statistical methods suit short personal baselines (n of roughly 14-60 days per person) and what is known about their use in spaceflight?
6. **RQ6.** What already exists (CSA Astroskin, NASA Cognition, HERA) and what gap is left for an offline, crew-owned, delayed-export tool?
7. **RQ7.** How should deviations be worded so the app never diagnoses or advises treatment (safe interpretation)?
8. **RQ8.** What evidence gaps (Mars-duration, comms delay, sex differences) should be disclosed honestly to judges?

---

## 2. The seven risks

### 2.1 Space radiation

- Mars transit measurement: MSL's RAD instrument measured radiation inside the spacecraft during the 253-day cruise (launched 26 Nov 2011); RAD operated 220 of 253 days. [2] VERIFIED (secondary: press release and search excerpts)
- Average galactic cosmic ray (GCR) dose equivalent rate in cruise: 1.8 mSv/day (press release figure). [2] VERIFIED (secondary). A separate excerpt cites about 1.75 mSv/day mean. [24] Use "roughly 1.8 mSv/day" and cite both.
- Absorbed dose rates quoted by a follow-up paper citing Zeitlin 2013: about 332 +/- 23 uGy/day (silicon detector) and 461 +/- 92 uGy/day (plastic scintillator). [2] VERIFIED (secondary; a follow-up paper, not the original table)
- Solar particles: about 5% of the dose was from solar particles during a quiet solar period; five significant SEP events seen in cruise. [2] VERIFIED (secondary). Do NOT extrapolate this to solar-maximum conditions (ASSUMPTION flag).
- Shortest round trip with current propulsion and comparable shielding: 0.66 +/- 0.12 Sv dose equivalent. [2] VERIFIED (secondary)
- ISS comparison: effective dose about 0.2-0.5 mSv/day; a NASA-programme table gives a mean of 0.4 mSv/day across 13 ISS crews. Free-space GCR dose rates are described as two to three times higher than on ISS (GCR only, excludes SEP). [24] VERIFIED (secondary/excerpt)
- NASA career limit (current standard): 3% risk of exposure-induced death (REID) from cancer at 95% confidence (NASA-STD-3001). NASA proposed a single approximately 600 mSv career limit (+/-10%) for all astronauts; the National Academies review (2021) supported a single dose-based limit but recommended re-examining the 3% metric. Whether the final standard was adopted: UNVERIFIED. [23]
- CNS/cardiovascular: the combined effect of altered gravity and deep-space radiation on heart and vessels is "not yet well understood"; HRP states very little is documented on combined CNS effects of gravity, radiation and psychological stressors. [1][8]
- Countermeasures in use: shielding, storm shelters, operational planning and career dose limits (general). [23] ASSUMPTION for specifics; nothing pharmacological is validated in what we saw.
- **What an app can monitor:** the app cannot measure radiation. It can LOG readings from an onboard personal dosimeter/area monitor (SPECIALIZED-EQUIPMENT, manual entry or file import), keep cumulative dose vs a configurable limit (the limit value must be set by the flight surgeon, not hard-coded), and flag SEP-event periods for later correlation with symptoms. DESIGN HYPOTHESIS.
- Safe interpretation: "Logged cumulative dose is X; the mission-defined threshold is Y (set by medical team)". No cancer-risk numbers computed by the app.

### 2.2 Bone and muscle loss

- NASA HRP evidence report (2017): average pre- to post-flight areal BMD (aBMD) decline of 1-1.5% per month on 4-6 month missions. [3] VERIFIED (excerpt)
- Site-specific rates (older 2015 HRP slides, dated): about 1.5%/month hip, 1%/month lumbar spine, 0.3%/month whole body, versus 0.5-1.0%/year in ageing adults. [3-related NTRS slides; excerpt] VERIFIED (dated; low confidence on exact numbers)
- No data validate a percentage BMD loss as a predictor of fracture in astronaut-age adults; the report judges the loss not unacceptable for microgravity ISS/asteroid missions but warns fractional gravity or missions over 6 months could raise risk. [3] VERIFIED (excerpt)
- Strength loss may exceed density loss: a NASA-funded hip CT/finite-element study reported estimated strength drops of 0.6-5%/month versus 0.4-1.8%/month for density (secondary reporting). [4] VERIFIED (secondary). ISS CT study: vertebral bone loss 0.8-0.9%/month. [3-related excerpt] VERIFIED (secondary)
- Bone biomarkers: bed-rest studies show urinary N-telopeptide (resorption marker) rising in controls and blunted by resistance exercise; formation markers unchanged. ISS studies reported higher NTX and urinary calcium in flight. [32] VERIFIED (excerpt; bed rest is an analogue)
- Muscle: no percent muscle-loss figure found in our searches. Muscle-loss magnitude: UNVERIFIED (omit). One finding: up to 17% of astronauts would experience performance-limiting deconditioning if current exercise countermeasures were used on future missions (excerpt, source not opened). [25] VERIFIED (excerpt only)
- Countermeasures: ISS uses ARED (resistance, installed 2008), T2 treadmill, CEVIS cycle ergometer. A 2023 npj Microgravity study of 46 astronauts reports about 600 min/week total exercise per crew member (range about 450-720), median 84 completed T2 sessions and 65 CEVIS sessions. [25] VERIFIED. Bisphosphonates "could help" (NASA news page, secondary). [3-related]
- A 2020 npj Microgravity comparison found a higher-intensity, lower-volume protocol (SPRINT) not appreciably different overall from standard ISS exercise. [25-related excerpt] VERIFIED (excerpt)
- **What an app can monitor:** exercise compliance (minutes per modality/week; compare to the crew member's own prescribed plan), body mass (mass-measurement device), muscle soreness/pain symptom check, optional step/activity from wearables. Bone density (DXA/QCT) and bone markers (blood/urine) are SPECIALIZED/lab: app only logs results.
- Safe interpretation: "Exercise minutes this week are X% below your own usual", not "you are losing bone".

### 2.3 Cardiovascular (including orthostatic intolerance, fluid shift and SANS briefly)

- NASA HRP frames the risk as cardiovascular deconditioning, dysfunction and remodelling; orthostatic intolerance (OI) at ascent/landing is the leading in-mission/landing concern for LEO and lunar surface; for longer missions cardiovascular disease is a long-term health issue. Venous thromboembolism has been folded into the concern (page updated June 2025). [1] VERIFIED
- Reduced left ventricular mass reported in some returning ISS crew (HRP 2024 workshop slides). [1-related NTRS excerpt] VERIFIED (excerpt)
- OI numbers vary by era/protocol: after short shuttle flights, 9 of 14 astronauts could not complete a 10-minute stand test within 4 hours of landing; 5 of 6 US astronauts could not complete a 10-minute tilt after 4-5 months on Mir; later ISS crews are described as mostly able to stand. Figures are not directly comparable. [26] VERIFIED (excerpt)
- NASA report: end-of-mission fluid loading failed to protect about 30% of Shuttle astronauts when compression garments were not worn; countermeasures include exercise, fluid loading and compression garments. [26] VERIFIED (excerpt)
- SANS (spaceflight-associated neuro-ocular syndrome): optic disc edema, chorioretinal folds, globe flattening, hyperopic shift; NASA briefing says about 70% of long-duration crew show signs (mild to severe); a consensus paper reports 52 of 64 (81%) NASA astronauts with at least one ocular finding; cause unclear. Optical coherence tomography (onboard ISS since 2013) is NASA's key detection tool; countermeasure is "space anticipation glasses". [21] VERIFIED (excerpt; percentages differ by definition/population)
- Fluid shift (headward) is the commonly proposed contributor to SANS; mechanism "thought to involve multiple factors". [21] VERIFIED (only that it is multifactorial)
- **What an app can monitor:** resting HR and HRV trends (DIRECT wearable), blood pressure (cuff, SELF-REPORT/manual entry), pre-landing orthostatic symptoms checklist (dizziness on standing), leg swelling/headache, and a "vision changes" question (blurred near/far) that prompts reporting to the surgeon. Fundoscopy/OCT are SPECIALIZED.
- Safe interpretation: never say "no SANS". Say "You reported a vision change; flag for flight surgeon review".

### 2.4 Immune changes

- Immune dysregulation and latent herpesvirus reactivation persist across 6-month ISS missions; adaptive immunity aspects dysregulated while some innate aspects heightened; some crews show persistent hypersensitivity reactions; viral reactivation proposed as a biomarker of compromised adaptive immunity (review). [10] VERIFIED
- Integrated Immune studies: 28 crew sampled before, during and after missions; cytokine changes persisted through a 6-month mission (Journal of Interferon & Cytokine Research, secondary reporting). [31] VERIFIED (secondary)
- Earlier study (13 Shuttle + 8 ISS crew): marked reduction in T-cell function after ISS flight. [31] VERIFIED (excerpt)
- Clinical consequence on longer missions: "further investigations are required" per the lead investigator. [31] VERIFIED
- Countermeasures: review title refers to "potential countermeasures"; specifics not verified here. UNVERIFIED (omit)
- **What an app can monitor:** symptom checklist (cold-like symptoms, rash, oral lesions, fatigue, fever), body temperature trend (DIRECT), skin-reaction log, sleep disruption. Immune markers (cytokines, viral shedding PCR, lymphocyte subsets) are lab/SPECIALIZED: log only.
- Relevant analogue evidence: wearable resting-heart-rate deviation detected 63% of 32 COVID-19 cases before symptom onset in a cohort of about 5,300 (terrestrial, not spaceflight). [27] VERIFIED (excerpt). This is a DESIGN HYPOTHESIS for in-flight illness early warning; unvalidated in space.
- Safe interpretation: "Your resting heart rate and temperature are above your personal range for N days; consider reporting symptoms". No infection claims.

### 2.5 Sleep and circadian disruption

- Barger et al. 2014 (Lancet Neurology, 85 astronauts, more than 4000 nights on Earth and more than 4200 in space): 8.5 h scheduled sleep; average actual sleep just under 6 h on shuttle and just over 6 h on ISS; about 6.5 h or less in pre-flight training. [22] VERIFIED (secondary summary)
- Hypnotic use: 78% (61/78) of shuttle crew took a sleep-promoting drug on 52% (500/963) of nights; 75% (12/16) of ISS crew reported use. [22] VERIFIED (secondary summary)
- NASA risk page: astronauts average less sleep in flight; cause unclear (environment vs work overload); circadian desynchrony observed; ISS crew see 16 sunrises/sunsets per day; schedule creep reported; sleep inertia not evaluated in flight; sex differences not systematically studied. [9] VERIFIED
- Mars-500 (520-day isolation): crew activity fell and sleep/rest time rose over the mission ("behavioural torpor"); light exposure decreased; one crew member drifted to a 25 h day; wide inter-individual differences. [14][35] VERIFIED (excerpt)
- Countermeasures: work-hour guidelines, scheduled sleep period, lighting (details UNVERIFIED), hypnotics (documented above). [9][22]
- **What an app can monitor:** sleep duration/efficiency/regularity from wrist actigraphy (DERIVED from DIRECT), bedtime/wake time consistency, daily Karolinska Sleepiness Scale (KSS), light-exposure log, caffeine/medication log (including hypnotic use, important given fatigue risk), PVT-B vigilance.
- Safe interpretation: "Sleep below your own usual range for 5 nights"; do not label insomnia. Show sleep consistency trend as a first-class metric (Mars-500 shows drift matters).

### 2.6 Behavioural and psychological health

- NASA Behavioral Medicine evidence report (2025, NTRS 20250001454): average incidence of adverse behavioural health events is relatively low on current missions (up to 6 months and lunar), the knowledge base has grown, and the exploration environment is a significant, still unknown risk. 2016 TM states likelihood greatly increases with mission length. [8] VERIFIED (excerpt, report not opened)
- HRP studies the interaction of behavioural stressors with radiation and altered gravity; very little documented. [8] VERIFIED
- Twins Study: some post-flight cognitive decline observed (spatial/emotional/decision tasks developed by the Basner group); most changes returned to baseline within the study window; body mass fell during flight (one report: 7%, secondary). [11] VERIFIED (excerpt; check numbers in paper)
- Cognition battery: 10 tests, about 20 min, 15 unique versions for repeat use, used routinely by ISS astronauts and in HERA; practice effects and test-retest variability differ substantially between tests. [13] VERIFIED
- Countermeasures: HRP behavioural health and performance programme (details not verified here), crew-ground communication, psychological support; specifics UNVERIFIED.
- **What an app can monitor:** weekly mood/stress/anxiety/well-being screens (see Section 4), daily brief mood and stress, cognitive tests (PVT-B, optionally the Cognition-style tasks; the actual Cognition battery is a NASA-controlled tool, so we would build our own simple tasks), social/communication frequency log (DESIGN HYPOTHESIS).
- Safe interpretation: "Your mood scores trended lower than your own baseline"; show crisis/support pathway text approved by the flight surgeon; no diagnosis labels (e.g. "depression") on screens.

### 2.7 Isolation and confinement

- Mars-500 design: three isolation studies (14, 105, 520 days), six crew; 520-day study used continuous wrist actigraphy and light data (4.396 million minutes) and weekly computer-based neurobehavioural tests (n=888). [14][35] VERIFIED (excerpt)
- Findings: increasing sedentariness (hypokinesis), more sleep/rest time, reduced light exposure, circadian drift in one subject, one participant with increasing PVT errors; "differential vulnerability" to isolation and confinement emphasised. [14][35] VERIFIED (excerpt)
- Limitations flagged: no microgravity or radiation, no real risk. [14] VERIFIED
- HERA (NASA analog, 4 crew, 7-45 day missions): behavioural health/performance, communication and autonomy studies, with deliberate stressors in some missions; instruments and data-access terms not found. [30] VERIFIED for the description; data access UNVERIFIED
- Countermeasures: crew selection, scheduled communications, recreation, autonomy training (general; specifics UNVERIFIED).
- **What an app can monitor:** movement/activity trend (Mars-500 signal), light exposure, sleep timing drift, daily mood, conflict/social-support single items, "private journal" optional. DESIGN HYPOTHESIS: combine activity drop + sleep drift + low mood as a compound early flag.
- Safe interpretation: privacy matters; make crew-medical-officer visibility explicit and opt-in/consent-governed (ASSUMPTION; needs policy input from a flight surgeon).

---

## 3. Indicator table

Class key: DIRECT = wearable measured; SELF-REPORT; DERIVED = computed from other data; SPECIALIZED = needs onboard medical equipment or lab. "Baseline/threshold availability" states whether a published spaceflight/population threshold exists; if not, the app uses the individual's own baseline.

| Indicator | Why it matters | Measurement | Class | Feasible frequency | Limitations | Baseline/threshold availability | Safe interpretation |
|---|---|---|---|---|---|---|---|
| Resting HR | Illness, fatigue, deconditioning, stress; used in terrestrial early-illness detection [27] | Wrist PPG or chest strap, lowest/quiet-period value | DIRECT | Continuous; daily summary | Motion artefact; wrist PPG accuracy varies; no spaceflight-validated thresholds found | Personal baseline only (UNVERIFIED for spaceflight norms); terrestrial method uses own baseline [27] | "Above your usual range for N days"; not a diagnosis |
| HRV (RMSSD) | Autonomic balance, recovery; athletes use 7-day rolling mean with smallest worthwhile change [20] | Beat-to-beat intervals during sleep/rest; ln(RMSSD) | DERIVED | Daily (nocturnal or morning 1-5 min) | Needs IBI-quality sensor; consumer HRV is device-specific; strong inter-individual variance | No universal thresholds; compare to self. 3+ days/week averaging gave similar results to 7 days in athletes [20] | "Recovery metric below your range"; avoid health claims |
| SpO2 | Hypoxia/illness; relevant to cabin atmosphere | Wrist/finger pulse oximeter | DIRECT | Spot or overnight | Wrist SpO2 harder (low perfusion/SNR); skin-tone bias and motion/sleep issues; pooled accuracy values 3.96-4.71% in a 2024 meta-analysis [29] | Clinical cut-offs exist terrestrially (not sourced here: UNVERIFIED); ISS-specific none found | "Reading lower than your usual; re-measure at rest"; never reassure on normal |
| Skin / core temperature | Fever, illness, circadian phase | Skin patch/wearable; oral/ear/forehead thermometer for spot core estimate | DIRECT (skin) / SELF-REPORT (manual spot) | Continuous skin; daily spot | Skin temp is not core; environment/exercise/clothing effects; astronaut cabin thermal conditions | Own baseline; shift from personal range | "Temperature trend above personal range"; prompt fever check with proper thermometer |
| Blood pressure | Cardiovascular adaptation, OI risk [1][26] | Automatic cuff | SPECIALIZED/SELF-REPORT entry | Daily or weekly; orthostatic checks pre-landing | Cuffless wearables not validated here; posture/timing matter | Personal baseline; population cut-offs UNVERIFIED | "Differs from your baseline"; surgeon decides |
| Sleep duration/efficiency | Sleep loss is a documented in-flight issue [9][22] | Wrist actigraphy (+ HR) | DERIVED | Nightly | Actigraphy overestimates sleep vs polysomnography (general knowledge, UNVERIFIED here); stage accuracy poor | ISS mean about 6-6.5 h vs 8.5 h scheduled [22]; individual baseline preferred | "Shorter than your usual"; no clinical labels |
| Activity and exercise compliance | Countermeasure adherence (bone, muscle, CV) [25] | Log ARED/T2/CEVIS minutes; accelerometer steps | SELF-REPORT + DIRECT | Per session; weekly summary | Wearable does not capture resistive load; loads logged by ISS hardware (access UNVERIFIED) | ISS crew average about 600 min/week (range 450-720) [25]; use the person's own prescription | "X min vs your plan"; no performance judgements |
| Body mass | Fluid, nutrition, muscle; Twins mass fell [11] | Space-rated mass measurement device | SPECIALIZED | Weekly (ASSUMPTION) | Requires special scale in microgravity; hydration confounds | Personal baseline | "Mass trend down X% vs baseline; report to surgeon" |
| Radiation dose | Cancer risk and career limits [23] | Personal dosimeter / ISS-style monitors | SPECIALIZED | Per dosimeter readout | App cannot measure; reading cadence set by mission | Career limit under NASA standard (3% REID; 600 mSv proposal) [23] | Log cumulative values only; mission medical team interprets |
| Mood/stress scales | Behavioural risk [8] | Short validated forms (Section 4) | SELF-REPORT | Daily 1-item; weekly full scale | Social desirability (crew fear of grounding); licensing | Population norms exist per instrument (not sourced here); use personal baseline | "Lower than your own range"; show support options |
| Cognitive tests (PVT-B; Cognition-style) | Fatigue/performance; sensitive to sleep loss [12][13] | 3-min PVT-B; NASA battery of 10 tests, about 20 min [13] | SELF-REPORT (performance-based) | PVT-B daily/few times/week; battery weekly | Practice effects differ by test [13]; device latency; the NASA battery itself is NASA tool | PVT-B validated against 10-min PVT in 74 adults [12]; no mission thresholds | "Slower response than your baseline"; avoid fitness-for-duty claims |
| Symptoms checklist | Catch illness, SANS, OI, GI issues | Structured checkboxes | SELF-REPORT | Daily | Under-reporting; symptom list design needs surgeon input | None; counts vs personal usual | "Reported symptoms list for surgeon"; no cause attribution |
| Hydration / urine | Fluid shift, kidney stone risk (stone risk not sourced here) | Urine colour chart/volume log or specific gravity | SELF-REPORT / SPECIALIZED | Daily (ASSUMPTION) | Water reclamation context; colour charts are crude | UNVERIFIED | "Log only"; avoid dehydration diagnosis |
| Bone markers (lab) | Resorption/formation balance [32] | Blood/urine assays (e.g. NTX, urinary Ca) [32] | SPECIALIZED | Periodic, per mission protocol | Needs lab or point-of-care equipment; delayed | Pre-flight own baseline; no thresholds | Store results; no interpretation in app |
| Immune markers (lab) | Dysregulation, viral reactivation [10][31] | Blood/saliva assays, viral shedding | SPECIALIZED | Periodic, per protocol | Needs sample handling; not real-time | Pre-flight own baseline | Store results only |
| Visual acuity / SANS checks | SANS prevalence high on long missions [21] | Near/far chart, fundus/OCT (onboard ISS since 2013) [21] | SELF-REPORT (acuity chart) / SPECIALIZED (OCT) | Weekly acuity chart (DESIGN HYPOTHESIS) | Chart tests crude; OCT needs equipment | NASA own procedures; thresholds UNVERIFIED | "Report any change"; never "all clear" |

Table-level notes: (a) all "feasible frequency" entries are ASSUMPTION/DESIGN HYPOTHESIS unless a cite is given. (b) Population reference ranges (HR, BP, SpO2) are standard medicine but not sourced in this session; do not hard-code them without a clinician and a citation.

---

## 4. Validated self-report instruments for daily/weekly check-ins

| Instrument | What it measures | Verified existence/evidence | Cadence idea | Licensing |
|---|---|---|---|---|
| PVT-B (3-min brief Psychomotor Vigilance Test) | Sustained attention/lapses; sleep loss sensitivity | Basner et al. 2011: 74 healthy adults, total and partial sleep deprivation; 3 min vs 10 min (about 70% shorter), ISI 1-4 s; about 22.7% mean effect-size loss vs standard PVT [12]. Smartphone/tablet 3-min PVT validated against 10-min laptop PVT in 16 participants (2016); stable across repeated administration (2018) [12] | Daily or 3x/week | Test paradigm is published; app implementation is our own. Check any specific software (e.g. PC-PVT) terms: UNVERIFIED |
| NASA Cognition test battery | 10 tests, about 20 min: spatial orientation, emotion recognition, risk decision-making etc. | Basner et al. 2015 Aerosp Med Hum Perform 86(11):942-952; 15 unique versions; used by ISS crew and in HERA [13] | Weekly (not reproducible by us without access) | NASA/University of Pennsylvania resource; reuse terms UNVERIFIED. Treat as "inspiration" only |
| Karolinska Sleepiness Scale (KSS) | 9-point momentary sleepiness (1 extremely alert, 9 very sleepy) | Original Akerstedt and Gillberg 1990 Int J Neurosci 52:29-37; validated against performance/EEG by Kaida et al. 2006 [18] | Several times per day or daily | Copyright Akerstedt 1990; reproduction/adaptation requires written consent from the author per the chapter notice [18]. ACTION: get written permission before shipping |
| PANAS | Positive/negative affect (20 items) | Watson, Clark, Tellegen 1988, J Pers Soc Psychol 54:1063-1070 (citation from model memory; search could not confirm) | Weekly | Terms UNVERIFIED; check publisher/authors before shipping |
| Profile of Mood States (POMS) | Mood dimensions | Existence per common knowledge; no source retrieved. UNVERIFIED | Weekly | Reported (not verified here) as commercially published: assume licence needed. ASSUMPTION; avoid in MVP |
| PHQ-9 / GAD-7 | Depression / anxiety symptom screeners | Existence from a source about PHQ family; licensing sources conflict: PHQ family described as public domain by one secondary source; psytools marks GAD-7 free-to-use; UK NCCR lists GAD-7 as copyrighted measure under a licensing service [19] | Weekly/biweekly | UNVERIFIED/CONFLICTING: confirm with developers/Pfizer statement. Also clinical risk: PHQ-9 item 9 (self-harm) needs an escalation protocol; DESIGN HYPOTHESIS |
| WHO-5 Well-Being Index | Brief wellbeing | No source retrieved in this session. UNVERIFIED | Weekly | UNVERIFIED |
| Single-item mood/stress sliders (our own) | Daily subjective state | DESIGN HYPOTHESIS; no validation | Daily | Our own wording; not a "validated instrument" - say so on screen |

Guidance:
- Do not describe app scores as clinical screening results. Present them as personal trend values. DESIGN HYPOTHESIS.
- Prefer instruments with clear public or permissive terms, or write our own plain items and label them "non-validated".
- Practice effects: the Cognition battery paper reports practice effects and test-retest variability differ substantially across tests, so baseline period (several sessions) should be discarded/learning phase. [13] VERIFIED; the number of learning sessions is ASSUMPTION.

---

## 5. Open datasets for prototyping

IMPORTANT: none of these are spaceflight-crew-grade physiological streams at daily resolution. Terrestrial and analogue data are SUBSTITUTES to test code paths (ingest, baseline fit, deviation flag, alert wording). They cannot validate thresholds for astronauts. Suggested use: (1) healthy terrestrial wearables for rolling-baseline algorithms; (2) injected synthetic deviations (step changes, drifts, missing days) for sensitivity testing; (3) analogue/isolation data only for behavioural-trend sanity checks. DESIGN HYPOTHESIS.

| Dataset | URL | Access | Size/signals | Notes and suitability |
|---|---|---|---|---|
| NASA OSDR (GeneLab) | https://science.nasa.gov/biological-physical/data/osdr | Mixed: OSDR describes itself as open access and FAIR; human-derived data under controlled access via IRB, request form, Data Access Committee and Data Use Agreement; access time-limited [16] | Mostly omics; not daily vitals | Open (non-human) data: no restriction stated on AWS listing (unclear if it covers restricted data) [16]. Not useful for HR/HRV prototyping. VERIFIED for process |
| Inspiration4 / SOMA | Portal: https://soma.weill.cornell.edu/apps/SOMA_Browser | Processed I4 data public on OSDR; raw sequence-level data require application and board approval [6]; Mason Lab portal for browsing [5][6] | Multi-omics, clinical labs, cognition etc. for a 4-person civilian crew | Package of Nature-family papers (conflicting paper counts across NASA pages: 31, 32 or 44) [5][6]. Useful context, not for wearable baselines. VERIFIED existence; exact vitals availability UNVERIFIED |
| NASA LSDA (Lifetime Surveillance of Astronaut Health) | - | Not confirmed by any source in this session; we found only OSDR controlled-access process [16] | - | Assume restricted/application-only (ASSUMPTION). UNVERIFIED |
| WESAD | https://archive.ics.uci.edu/dataset/465/wesad+wearable+stress+and+affect+detection (DOI 10.24432/C57K5T) | Open download (UCI; authors allow scientific, non-commercial use with credit) [7] | 15 subjects; chest RespiBAN (ECG, EDA, EMG, respiration, temperature, 3-axis acceleration) and wrist Empatica E4 (BVP, EDA, temperature, acceleration); neutral/stress/amusement; about 2.5 GB on authors' site [7] | Lab stress; short sessions, not multi-day baselines. Non-commercial restriction: fine for hackathon, check before commercialising. VERIFIED |
| PhysioNet: Multi-night Apple Watch HR + accelerometry with EEG sleep stages | https://www.physionet.org/content/bidsleep-dataset/ (DOI 10.13026/rees-1092 for v1.0.1) | "Open Access: accessible by all users, minimal restrictions" per PhysioNet listing [17]; licence name not confirmed | 47 healthy adults, up to 7 nights, 253 nights; HR about 0.2 Hz from HealthKit; Dreem 2 EEG sleep stages (30 s epochs) [17] | Good for multi-night sleep/HR baseline tests; HR too sparse for HRV [17]. Subject-level splits recommended by authors [17]. VERIFIED |
| PhysioNet: Wearable Exam Stress dataset | PhysioNet open-database list [17] | Open (listing) | EDA, HR, BVP, inter-beat intervals across exams [17] | Multi-day-ish repeated measures; possible HRV use. Details UNVERIFIED beyond listing |
| PhysioNet: BIDMC PPG and Respiration; Brno smartphone PPG | PhysioNet list [17] | Open (listing) | PPG/respiration; PPG quality | Algorithm sanity checks; short recordings. Details UNVERIFIED |
| NSRR (National Sleep Research Resource) | Not retrievable in this session | Our search returned no NSRR page; the general pattern for NIH repositories is credentialed/DUA [from other repos only] | - | UNVERIFIED. Check nsrr.org yourself; expect application for some cohorts |
| Kaggle / UCI wearable datasets (other than WESAD) | - | Not verified | - | UNVERIFIED; do not cite without opening |
| Mars-500 (published data) | Papers: Basner et al., PNAS 2013 (supporting appendix at med.upenn.edu) [14][35] | Aggregate figures in paper/appendix; no downloadable raw dataset found | Actigraphy and weekly tests (n=888) [14] | Use published effect patterns to design synthetic scenarios (activity drop, sleep drift). Raw data availability UNVERIFIED |
| HERA | NASA analog studies portal (analogstudies.jsc.nasa.gov, not verified) | Not found [30] | - | Data access UNVERIFIED |

Truly open without application in our evidence: WESAD (non-commercial, credit), PhysioNet open-access listings (Apple Watch/EEG sleep dataset; others per listing), and OSDR's non-human, public data. Controlled/restricted: OSDR human data, I4 raw data, probably LSDA/HERA.

---

## 6. Existing comparable technologies

- **CSA Astroskin Bio-Monitor (Carre Technologies/Hexoskin):** smart garment measuring temperature, heart rate, respiration and movement; data sent to a tablet app; launched on SpaceX CRS-16 (Dec 2018); commissioned by David Saint-Jacques Jan 2019; tested during sleep and exercise; used by CSA/NASA/ESA; CSA contract about CAD 2.4 million; Xiphos processor card for data transmission; CSA said it would make the system available to ISS partners for five years. [15] VERIFIED (largely company/CSA sources; current status UNVERIFIED)
- **NASA Cognition battery:** see Section 4; ground-controlled, per-astronaut testing with baseline and normative data. [13] VERIFIED
- **PVT-B on ISS:** one page states the PVT-B has been tested by astronauts on ISS (no detail). [12] VERIFIED (weak)
- **HERA:** analog habitat with behavioural health and human-system studies. [30] VERIFIED
- **ESA/DLR projects:** none verified in this session. UNVERIFIED (do not claim).
- **NASA crew medical systems (e.g. ISS medical kits, flight surgeon console):** not researched. UNVERIFIED.
- **Terrestrial analogue:** personal-baseline deviation from wearables (resting HR deviation for early illness detection, 63% of 32 cases pre-symptom) [27]; shows the concept works on Earth with consumer wearables.
- **Gap statement (DESIGN HYPOTHESIS):** existing tools are mission- or research-protocol-driven, controlled by agencies. A crew-owned, offline-first, multi-domain personal baseline view with explainable deviation flags and a delayed, structured export for the flight surgeon is not documented in what we found. We cannot claim "no such tool exists" without a wider search (UNVERIFIED).

---

## 7. Statistical methods for personal baselines with small n

Core requirement: baseline must be estimated from the individual's own days, be robust to outliers and missing data, and run offline with simple code. All method choices are DESIGN HYPOTHESIS unless cited.

| Method | Use | Evidence found | Notes |
|---|---|---|---|
| Rolling mean/median with MAD | Robust personal range, e.g. median +/- k x MAD over 14-28 days | Not sourced in this session (standard robust statistics; UNVERIFIED citation) | Median/MAD resists outliers; need min days (ASSUMPTION: at least 7-14) |
| 7-day rolling mean and smallest worthwhile change (SWC) | Athlete HRV monitoring | Plews et al. case study: 7-day rolling mean of ln RMSSD vs individual SWC; 3 or more days/week gave equivalent results to 7-day mean; AIS guideline recommends 7-day rolling averages with SWC; SWC concept from Batterham and Hopkins [20] VERIFIED (excerpt; exact SWC formula not retrieved) | One case-study figure had an implausible r-squared in the abstract (reporting error), so use only the design idea |
| CUSUM | Detect small persistent shifts | Mishra et al. Nat Med 2021 states CuSum and RHRAD algorithms detect abnormal deviations in resting HR relative to baseline [28]; BMC Med Res Methodol 2016 compared tabular CUSUM, standardized CUSUM and EWMA on simulated wearable-sensor data for older adults [33] VERIFIED (excerpt) | Tune slack/threshold k, h on baseline data; false alarm rate must be reported |
| EWMA control chart | Detect gradual drifts | Same comparison study [33] VERIFIED; parameter choices not found | Choose lambda about 0.1-0.3 (ASSUMPTION; standard SPC practice, UNVERIFIED here) |
| Resting HR baseline from sliding window | Illness detection | Mishra 2020 NBE: 63% of 32 COVID-19 cases detected before symptoms by extreme resting-HR elevation relative to individual baseline; earlier algorithm used a 28-day sliding window (community post, secondary) [27] VERIFIED (excerpt) | Terrestrial; large cohort of about 5,300 |
| Personalised anomaly detection | Rare-event setting | University of Twente post-operative home monitoring: personalised AD proposed because deterioration events are too rare for supervised ML [34] VERIFIED (excerpt) | Supports unsupervised approach for rare events |
| Bayesian updating (e.g. normal-normal with prior from population, updated daily) | Small-n shrinkage; prior can encode pre-flight baseline | No source retrieved in this session. UNVERIFIED citation | DESIGN HYPOTHESIS: use pre-flight (ground) baseline as prior mean, widen variance for in-flight shift |
| Learning phase | Practice effects in cognitive tasks | Cognition battery paper notes practice effects differ by test [13] | Exclude first N sessions from baseline (N ASSUMPTION) |

Practical rules (DESIGN HYPOTHESIS):
1. Minimum baseline length per metric before flagging (e.g. 7 valid days; show "calibrating").
2. Two-tier alerts: "watch" (CUSUM/EWMA mild) and "report to surgeon" (persistent plus multi-signal agreement), reducing false alarms.
3. Multi-signal agreement (e.g. resting HR up + HRV down + sleep short + temperature up) before any strong wording.
4. Use pre-flight ground baseline as the starting reference but allow microgravity re-baselining; show both. Spaceflight shifts physiology (OI, fluid shift [1][26][21]) so ground baselines may not hold: ASSUMPTION flagged.
5. Always display data quality and missing-days count.
6. Report false-alarm rate on injected anomalies in prototype tests; do not claim detection accuracy on astronauts.

Evidence gaps (spaceflight): no source found validating CUSUM/EWMA/MAD personal-baseline alerts on in-flight astronaut data, nor reference changes for HRV in flight; all supportive evidence is terrestrial (athletes, COVID cohort, older adults on simulated data). [20][27][28][33]

---

## 8. Research gaps and open questions

1. **Spaceflight validity:** no published validation of wearable-derived resting HR/HRV/SpO2 thresholds on Mars-duration missions found. Open question for flight surgeons. UNVERIFIED/GAP
2. **Mission length:** ISS data cover about 6-12 months; Mars round trip is much longer (the 253-day one-way cruise [2]); bone report warns longer than 6 months and fractional gravity raise risk [3]; behavioural risk grows with duration [8].
3. **Sex differences:** sleep and circadian sex differences not systematically studied in flight due to small number of women [9]. Baseline models need to avoid sex-specific assumptions.
4. **Combined stressors:** HRP states combined radiation + altered gravity + psychosocial effects are poorly documented [1][8].
5. **Radiation:** final NASA career limit status (3% REID vs approximately 600 mSv) not confirmed here [23]; SEP contribution on future missions uncertain (cruise measured during a quiet period) [2].
6. **Sensor accuracy in microgravity/cabin:** SpO2 wrist accuracy limited and skin-tone/motion issues in terrestrial meta-analysis [29]; no spaceflight validation found.
7. **Muscle loss quantification:** no percent found in our searches. UNVERIFIED/GAP.
8. **Privacy and consent:** crew may under-report if data leads to flight restrictions (ASSUMPTION); who sees what, when, requires NASA policy input.
9. **Licensing:** KSS needs written permission [18]; PANAS, POMS, WHO-5, PHQ/GAD terms unresolved [19].
10. **Alert thresholds and false-alarm burden:** no data on acceptable alert rates for crew (DESIGN HYPOTHESIS: low, tunable).
11. **Medical sign-off:** all wording/thresholds should be reviewed by a physician or flight-surgeon mentor before any claim of use.
12. **Not researched here:** ESA/DLR programmes, NSRR terms, Kaggle datasets, HRV-in-flight literature, muscle metrics, nutrition/vitamin D, GI/renal (stones), CO2 effects.

Suggested next steps: (a) open every [n] URL and confirm quoted numbers; (b) email KSS author for permission; (c) obtain a clinician review of the symptom list and alert text; (d) prototype with WESAD + PhysioNet sleep data + synthetic perturbations.

---

## 9. References

All were seen only via WebSearch result excerpts (see [0]); URLs as returned.

[0] Caveat: WebFetch blocked in the session (DNS failure for nasa.gov, ntrs.nasa.gov, swri.org, archive.ics.uci.edu); no full-text verification. Re-verify before citing.
[1] NASA Human Research Program, "Risk of cardiovascular adaptations contributing to adverse mission performance and health outcomes", NASA (page updated June 2025). https://www.nasa.gov/directorates/esdmd/hhp/risk-of-cardiovascular-adaptations-contributing-to-adverse-mission-performance-and-health-outcomes/ (related: Tahimic, HRP IWS 2024 slides, https://ntrs.nasa.gov/api/citations/20230014752/downloads/Tahimic%20HRP%20IWS%202024.pdf)
[2] Zeitlin C. et al., "Measurements of Energetic Particle Radiation in Transit to Mars on the Mars Science Laboratory", Science 340(6136):1080-1084, 2013; figures via SwRI press release https://www.swri.org/newsroom/press-releases/swri-led-team-calculates-radiation-exposure-associated-trip-mars and follow-up papers (https://pdf.arxiv.org/pdf/1503.06631; EGU abstracts). Secondary.
[3] NASA HRP, Evidence report on bone fracture risk due to spaceflight-induced bone changes, 2017. https://ntrs.nasa.gov/api/citations/20170004597/downloads/20170004597.pdf ; Sibonga, HRP slides 2015, https://ntrs.nasa.gov/api/citations/20150001885/downloads/20150001885.pdf
[4] Keyak et al. hip strength study as reported by Space.com, "Space Station Astronauts Lose Bone Strength Fast". https://www.space.com/6354-space-station-astronauts-lose-bone-strength-fast.html (secondary)
[5] Overbey E. et al. and Jones C. et al. (SOMA package), Nature 2024; Space Omics and Medical Atlas. https://soma.weill.cornell.edu/apps/SOMA_Browser
[6] NASA OSDR, Inspiration4 mission datasets news. https://www.nasa.gov/osdr-latest-news-i4-mission-datasets/ ; SOMA publications news https://www.nasa.gov/osdr-latest-news-soma-publications
[7] Schmidt P., Reiss A., Duerichen R., Marberger C., Van Laerhoven K., "Introducing WESAD, a Multimodal Dataset for Wearable Stress and Affect Detection", ICMI 2018; UCI repository dataset 465. https://archive.ics.uci.edu/dataset/465/wesad+wearable+stress+and+affect+detection
[8] NASA HRP Behavioral Medicine evidence report, "Risk of Adverse Cognitive or Behavioral Conditions and Psychiatric Disorders...", Dev S., Whiting S. et al., 2025. https://ntrs.nasa.gov/citations/20250001454 ; 2015 report https://ntrs.nasa.gov/api/citations/20150016966/downloads/20150016966.pdf
[9] NASA HRP, "Risk of performance decrements and adverse health outcomes resulting from sleep loss, circadian desynchronization, and work overload". https://www.nasa.gov/directorates/esdmd/hhp/risk-of-performance-decrements-and-adverse-health-outcomes-resulting-from-sleep-loss-circadian-desynchronization-and-work-overload ; evidence report https://ntrs.nasa.gov/citations/20160003864
[10] Crucian B. et al., "Immune System Dysregulation During Spaceflight: Potential Countermeasures for Deep Space Exploration Missions", Frontiers in Immunology, 2018, doi 10.3389/fimmu.2018.01437. https://frontiersin.org/journals/immunology/articles/10.3389/fimmu.2018.01437/full
[11] Garrett-Bakelman F. et al., "The NASA Twins Study: A multidimensional analysis of a year-long human spaceflight", Science 2019, doi 10.1126/science.aau8650. https://pubmed.ncbi.nlm.nih.gov/30975860/
[12] Basner M., Mollicone D., Dinges D., "Validity and sensitivity of a brief psychomotor vigilance test (PVT-B) to total and partial sleep deprivation", Acta Astronautica, 2011. https://pmc.ncbi.nlm.nih.gov/articles/PMC3190077 ; PsyToolkit note https://www.psytoolkit.org/experiment-library/pvtb.html ; Sleep 2018 https://pubmed.ncbi.nlm.nih.gov/29126328/
[13] Basner M. et al., "Development and Validation of the Cognition Test Battery for Spaceflight", Aerosp Med Hum Perform 86(11):942-952, 2015, doi 10.3357/AMHP.4343.2015. https://asma.kglmeridian.com/view/journals/amhp/86/11/article-p942.xml ; PMC4691281; NASA 2018 comparison https://ntrs.nasa.gov/api/citations/20180007947/downloads/20180007947.pdf
[14] Basner M. et al., "Mars 520-d mission simulation reveals protracted crew hypokinesis and alterations of sleep duration and timing", PNAS 2013. https://www.med.upenn.edu/uep/assets/user-content/documents/PNAS2013Basner1212646110withSupportingAppendix.pdf (title from model memory; contents per search excerpt)
[15] Canadian Space Agency, "Bio-Monitor: Keeping an eye on astronauts' vital signs". https://www.csa-asc.gc.ca/eng/sciences/astroskin.asp ; Space.com https://www.space.com/43228-astroskin-smart-space-jersey-for-astronauts.html ; Hexoskin news https://hexoskin.com/blogs/news/bio-monitor-astroskin-to-launch-to-the-international-space-station
[16] NASA OSDR, "Requesting controlled-access datasets" tutorial https://www.nasa.gov/reference/osdr-tutorials-requesting-controlled-access-datasets/ ; OSDR FAQ https://science.nasa.gov/reference/osdr-faq ; AWS listing https://aws.amazon.com/marketplace/pp/prodview-unapchxb7ozfg ; NASA HRP IWS 2023 slides https://ntrs.nasa.gov/api/citations/20230001765/downloads/osdr_HRP_IWS_20230205.pdf
[17] PhysioNet, Song T.-A. et al., "A Multi-Night Instantaneous Heart Rate and Accelerometry Dataset with EEG Sleep Stage Labels", v1.0.1. https://www.physionet.org/content/bidsleep-dataset/ ; PhysioNet open database list https://physionet.org/physiobank/
[18] Akerstedt T., Gillberg M., "Subjective and objective sleepiness in the active individual", Int J Neurosci 1990;52:29-37; Kaida K. et al., Clin Neurophysiol 2006; KSS chapter with copyright notice. https://www.med.upenn.edu/cbti/assets/user-content/documents/Karolinska%20Sleepiness%20Scale%20(KSS)%20Chapter.pdf
[19] PHQ/GAD licensing: https://wikiwand.com/EN/Patient_Health_Questionnaire ; https://cdn.jsdelivr.net/npm/psytools@0.6.3/SOURCES.md ; https://nhsd-proxy.openprescribing.net/services/national-clinical-content-repository-copyright-licensing-service/nccr-tools-and-measures-library (conflicting, secondary)
[20] Plews D. et al., HRV monitoring in elite triathlete/athletes (AUT repository https://openrepository.aut.ac.nz/items/d7b41d29-e92e-4acf-90ea-ed68d84d6c73); Australian Institute of Sport, HRV best-practice guidelines https://www.ais.gov.au/__data/assets/pdf_file/0013/1130260/HRV-BPG_FINAL.pdf
[21] Brunstetter T., "Crewmember SANS Overview", NASA, 2023. https://ntrs.nasa.gov/api/citations/20230006218/downloads/Brunstetter%20-%20Crewmember%20SANS%20Overview%20-%20Export%20Control%20-FINAL%20-%2001May23.pdf ; consensus statistics via Springer https://link.springer.com/10.1038/s41433-026-04651-6
[22] Barger L. et al., "Prevalence of sleep deficiency and use of hypnotic drugs in astronauts before, during, and after spaceflight", Lancet Neurology 2014 (title from memory). Summary: https://sciencedaily.com/releases/2014/08/140807215803.htm ; https://www.eurekalert.org/news-releases/595450 (secondary)
[23] National Academies, "Space Radiation and Astronaut Health: Managing and Communicating Cancer Risks", 2021. https://nap.nationalacademies.org/read/26155/chapter/7 ; NASA OCHMO radiation protection brief https://www.nasa.gov/sites/default/files/atoms/files/radiation_protection_technical_brief_ochmo.pdf ; summary https://universetoday.com/151822/ (secondary)
[24] ISS and Mars transit effective dose: https://arxiv.org/pdf/2409.02001 ; https://ntrs.nasa.gov/api/citations/20250006168/downloads/ASCEND%20Simonsen%20draft%201.pdf ; https://pmc.ncbi.nlm.nih.gov/articles/PMC8038356/table/ijms-22-03739-t002
[25] "Exercise countermeasures on ISS" (npj Microgravity 2023, 46 astronauts), doi 10.1038/s41526-023-00256-5. https://www.nature.com/articles/s41526-023-00256-5.pdf ; 2020 SPRINT study https://nature.com/articles/s41526-020-00111-x.pdf ; HRP exercise page https://www.nasa.gov/general/astronaut-exercise/
[26] Orthostatic intolerance in spaceflight: NASA NTRS https://ntrs.nasa.gov/citations/20150007319 ; npj Microgravity review https://www.nature.com/articles/s41526-019-0082-3.pdf
[27] Mishra T. et al., "Pre-symptomatic detection of COVID-19 from smartwatch data", Nature Biomedical Engineering 2020, doi 10.1038/s41551-020-00640-6. https://med.stanford.edu/news/all-news/2020/12/smartwatch-can-detect-early-signs-of-illness.html ; https://communities.springernature.com/posts/early-detection-of-covid-19-at-scale-using-wearables
[28] Alavi A. et al. / Mishra T. et al., Nature Medicine 2021 (RHRAD and CuSum algorithms). https://www.nature.com/articles/s41591-021-01593-2
[29] Singh et al., "Impact of Skin Pigmentation on Pulse Oximetry Blood Oxygenation and Wearable Pulse Rate Accuracy: Systematic Review and Meta-Analysis", J Med Internet Res, 2024. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11502980/
[30] NASA HERA overview. https://www.nasa.gov/wp-content/uploads/2023/07/jsc-hhp-hera.pdf ; mission news https://www.nasa.gov/humans-in-space/nasa-selects-crew-for-45-day-simulated-mars-mission-in-houston
[31] Crucian B. et al., Integrated Immune study reports (ScienceDaily summary https://www.sciencedaily.com/releases/2014/08/140818135124.htm ; NASA immunology page https://www.nasa.gov/directorates/esdmd/hhp/immunology-and-virology/ ; NTRS https://ntrs.nasa.gov/citations/20080009760)
[32] Smith S.M. et al., "Calcium kinetics during bed rest with artificial gravity and exercise countermeasures", Osteoporos Int 2014. https://pmc.ncbi.nlm.nih.gov/articles/pmid/24861908
[33] Comparison of tabular CUSUM, standardized CUSUM and EWMA charts for trend detection with wearable-sensor transfer times, BMC Med Res Methodol 2016. https://bmcmedresmethodol.biomedcentral.com/track/pdf/10.1186/s12874-016-0124-4
[34] University of Twente, "Detecting early signs of patient deterioration at home using wearable...". https://research.utwente.nl/en/publications/detecting-early-signs-of-patient-deterioration-at-home-using-wear/
[35] Mars-500 behavioural coverage: https://zmescience.com/space/mars500-sleep-deprivation-boredom-08012013 ; https://doi.org//10.1371/journal.pone.0093298

# Patient Journey: Jordan Smith

This page follows Jordan Smith — a fictional but clinically representative patient — through a five-day general medicine admission. Jordan is not a real person, but every detail of his presentation, investigation results, and management plan reflects the kind of case that general medicine teams manage every week in Australian hospitals. The clinical content is grounded in real values: real creatinine levels, real CT findings, real drug names and doses.

The purpose is to show what Issued and IssuedOS actually do at each stage of an admission — and, just as importantly, what they do not do. Each scene describes what happens clinically, what the system does in response, and what safety boundaries keep the AI in its lane.

The complete clinical record is available in [`docs-patient-journeys/01-jordan-smith.md`](https://github.com/ClinTech-dev/issued-docs/blob/main/docs-patient-journeys/01-jordan-smith.md).

---

## Day 1 AM — Admission

Jordan Smith is a 50-year-old carpenter who arrives in the emergency department after five days of worsening fever, shortness of breath, and a productive cough with thick yellow sputum. He is an ex-smoker with a ten-pack-year history, quit at 30, and runs a small renovation business where he is regularly exposed to fine wood dust, timber particles, and adhesives — usually without respiratory protection. He has no regular medications and no significant past medical history beyond a shoulder operation eight years ago.

On arrival, his oxygen saturation is 88% on room air, improving to 93% on 3 litres via nasal prongs. He is febrile at 38.7 °C, tachycardic at 110, and tachypnoeic with a respiratory rate of 26. His chest X-ray shows right mid- and lower-zone consolidation. Bloods reveal a white cell count of 14, CRP of 210, and a creatinine of 138 µmol/L with elevated urea at 11.2 — suggestive of pre-renal acute kidney injury in the context of dehydration. COVID and influenza PCR are both negative. The ED team starts IV ceftriaxone and azithromycin, gives two litres of IV fluids, and refers Jordan to Dr Bowyer's general medicine team for admission.

Dr Jones, the medical registrar, sits down to write the admission note. In Issued, the admission note lives in a scratchpad — a freeform text area where the clinician writes however they normally would. Dr Jones types quickly, using the shorthand that every registrar uses: "50M carpenter, ex-smoker, pw 5/7 cough, fever, progressive SOB. SpO2 88% RA. CXR RLL consol. Cr 138. COVID neg." The scratchpad does not enforce structure. It captures what the clinician writes, in the way they write it.

When Dr Jones finishes and taps **Analyse**, the **Extraction Agent** reads the scratchpad text and proposes three issues with associated plans:

1. **Community-acquired pneumonia with acute hypoxic respiratory failure** — continue IV ceftriaxone and azithromycin, supplemental oxygen targeting SpO2 above 92%, nebulised salbutamol and ipratropium, chest physiotherapy, sputum for MCS, and a plan to step down to oral antibiotics once afebrile for 24 hours.
2. **Possible underlying COPD or occupational interstitial lung disease** — arrange outpatient pulmonary function tests once recovered, explore occupational history further, and consider respiratory follow-up post-discharge.
3. **Acute kidney injury, pre-renal (KDIGO Stage 1)** — continue IV hydration, monitor urine output and creatinine daily, arrange renal tract ultrasound to exclude obstruction, avoid nephrotoxins, and repeat UEC the following morning.

Each issue appears in Issued as a suggestion card — visually distinct from confirmed clinical content — with three actions available: accept, edit, or reject. Dr Jones reviews them, adjusts the wording on the pneumonia issue to add a note about prednisolone 37.5 mg commenced in ED for presumed infective exacerbation of COPD, accepts the other two, and the issues list is live.

!!! info "Safety"
    The Extraction Agent proposes only what the clinician wrote. It does not add diagnoses the clinician did not mention, suggest medications that were not in the note, or offer its own clinical interpretation. If Dr Jones had not mentioned the creatinine, there would be no AKI issue. Each proposed issue is a suggestion requiring explicit clinician action before it enters the active record.

---

## Day 1 PM — Consultant Post-take

That afternoon, Dr Bowyer — the consultant — reviews Jordan at the bedside. He notes pleuritic chest pain as an additional feature, reviews the examination findings in detail, and makes two key decisions: he ceases the prednisolone that was started in ED (steroids are not indicated for community-acquired pneumonia without confirmed COPD exacerbation) and refines the management plans for each issue. He adjusts the oxygen target to 92–96%, adds consideration of high-flow nasal prongs if work of breathing increases, and specifies the criteria for IV-to-oral antibiotic step-down.

Dr Bowyer makes all of these changes directly in the issues list. He is editing the clinical record, not generating text for an AI to interpret. No agent fires. No suggestions appear. The consultant's edits are the medical record.

Behind the scenes, the **Issue Index Agent** silently re-indexes the updated content — updating the internal representation of each issue's current state, its linked investigations, and its plan. This is a background housekeeping operation. It produces no visible output and requires no clinician interaction. It simply ensures that when agents do run later, they are working from the latest version of the record.

!!! info "Safety"
    The system serves the clinician, not the other way around. When a consultant edits the issues list directly, the system records those edits as the authoritative clinical record. Every version is saved. Every change is attributed to the clinician who made it, with a timestamp. There is no moment where the AI's view of the record supersedes the clinician's.

---

## Day 2 — Blood Results

Overnight, Jordan remains on 3 litres of oxygen with saturations in the low 90s. He continues to spike fevers and cough thick sputum. The next morning, the repeat bloods come back: creatinine has improved to 125 µmol/L (down from 138), white cell count is 13.6, and CRP is 225 — still elevated but the renal function is trending in the right direction. Blood cultures show no growth at 24 hours. His renal tract ultrasound shows no obstruction.

The intern, Dr Lay, enters these results into the Investigations section of Jordan's record. As the new data lands, the **Linkage Agent** matches each result against the active issues list and proposes structured linkages:

- The creatinine of 125 and the ultrasound result are linked to **Issue #3 (AKI)** — the result is directly relevant to monitoring renal recovery. The Linkage Agent also flags that Issue #3's documented plan may warrant a fresh look, since the trajectory has shifted.
- The CRP of 225 and the white cell count of 13.6 are linked to **Issue #1 (CAP)** — these are inflammatory markers relevant to tracking the pneumonia's trajectory.

These linkages appear as proposed connections that the clinician can accept or dismiss. Once accepted, the results sit underneath the relevant issue, so that on the next ward round, Dr Bowyer can see at a glance which results relate to which problem without having to mentally reconstruct the connections from a flat list of pathology values.

!!! info "Safety"
    The Linkage Agent says "this result is relevant to this issue" and, when a result lands on an active problem, "the documented plan may want a fresh look." It does not say "the AKI is improving," "you can stop fluids," or "the antibiotics are working." Clinical interpretation remains entirely with the treating team. The agent connects data to context and flags that the context may have moved; the clinician decides what the data means and whether the plan needs to change.

---

## Day 3 — CT Results and an Incidental Finding

By day three, Jordan reports feeling a little stronger but remains oxygen-dependent on 2 litres. His temperature overnight peaked at 37.8 °C — still low-grade febrile. The CT chest that was ordered the previous day comes back with four findings:

1. Dense consolidation in the right lower lobe with air bronchograms, consistent with bacterial pneumonia. No evidence of abscess or empyema.
2. Mild bronchial wall thickening throughout, supporting a background of COPD.
3. Patchy subpleural reticulation and early ground-glass changes in both lower lobes — possible early interstitial lung changes related to occupational dust exposure.
4. An incidental 5 mm non-calcified nodule in the left upper lobe, likely benign, with a recommendation for follow-up CT in three months.

The ward round notes are entered into the scratchpad. The **Linkage Agent** reads the CT report against the active issues and proposes a series of structured linkages: the consolidation and absence of abscess to **Issue #1 (CAP)**, the bronchial thickening and subpleural reticulation to **Issue #2 (COPD/ILD)**. The repeat bloods — creatinine now 112, WCC 12.4, CRP 210 — are linked to their respective issues alongside.

The fourth CT finding is different. The Linkage Agent finds no existing issue that the nodule belongs to. Rather than forcing a linkage, it proposes a separate kind of mutation — a *suggested new issue* — and surfaces a flag:

> **Incidental 5 mm LUL nodule mentioned in CT report — no matching issue on the active list. Suggested as a new issue for clinician review.**

This is not a recommendation to investigate the nodule and it is not a clinical judgement about what the finding means. It is a notification that something the clinician documented in the imaging has no corresponding entry on the issues list, paired with a draft new-issue card the clinician can accept, edit, or reject.

Dr Bowyer sees the flag, discusses the finding with the respiratory team by phone, and accepts the suggested new issue — editing it into **Issue #4: Incidental 5 mm LUL pulmonary nodule** with a plan for repeat CT chest at three months, results to be reviewed in Gen Med outpatient clinic, with consideration of referral to respiratory if there is interval change.

!!! info "Safety"
    The Linkage Agent says "this finding has no matching issue — here is a draft new-issue card if you want it." It does not say "this nodule needs urgent workup," "this could be malignant," or "you should order a PET scan." It identifies an omission in documentation coverage, not a clinical judgement. The clinician decides whether the finding warrants a new issue, and if so, what the plan should be.

---

## Day 4 — Clinical Improvement

Day four is the turning point. Jordan has been afebrile for over 24 hours. His oxygen saturations are 94–96% on room air — the nasal prongs are off. He is walking around the ward without assistance, his cough is improving, and his appetite has returned. Blood cultures remain negative at 72 hours. Sputum cultures grew only mixed respiratory flora. The creatinine is down to 105 µmol/L — effectively normal. The team switches him from IV ceftriaxone to oral cefuroxime 500 mg twice daily, commences a short course of prednisolone 25 mg daily for five days to address airway inflammation now that the acute infection has settled, and supplies a salbutamol inhaler with education on technique.

Dr Jones writes the daily review note, working in the scratchpad as usual: improvement on all fronts, antibiotic step-down, prednisolone commenced, inhaler issued, plan for discharge tomorrow if the trajectory holds. She marks the encounter type as a daily review and taps **Analyse**.

This time the request reaches the **Note Synthesis Agent**. Unlike the Linkage Agent — which reads single fragments — Note Synthesis reads the full draft note against the entire admission so far: every issue, every linked result, every plan, every outstanding task. It returns two things together.

The first is a clean rewrite of Dr Jones's note in the standard daily-review structure — the same content, organised the way the team formats progress notes. Dr Jones can edit or overwrite this before submitting; nothing is committed automatically.

The second is a unified set of proposed changes to the record:

- **Update Issue #3 (AKI)** — the documented plan still reads *"continue IV hydration, monitor UO and Cr daily"*, but the note and the recent results no longer support it. The agent proposes resolving the issue with a closing plan to maintain oral hydration and avoid nephrotoxins.
- **Update Issue #1 (CAP)** — reflect the step-down from IV ceftriaxone to oral cefuroxime and the commencement of prednisolone for airway inflammation.
- **Create three follow-up tasks under Issue #2 (COPD/ILD)** — pulmonary function tests in 6–8 weeks, respiratory outpatient follow-up, and workplace PPE counselling. These actions were already written in Issue #2's plan text but had never been captured as discrete tasks that would carry through to discharge.

Alongside these proposals, the agent surfaces one **completeness flag**: the note does not document a formal review of the airway, breathing, and circulation observations for today, which the structured daily-review template asks for. The flag is a prompt to consider, not a correction — Dr Jones decides whether to address it before submitting.

Dr Jones reviews each proposal, accepts all of them, takes the completeness prompt on board, and adds the missing observation summary into the rewritten note before submitting.

!!! info "Safety"
    Note Synthesis proposes; the clinician decides. Each mutation — every plan update, every new task, every issue closure — appears as an individual card that Dr Jones accepts, edits, or rejects. The rewritten note is a draft she can edit or discard. The completeness flag points to a documentation gap against a structured template; it does not say what the gap means clinically. Every proposal must be traceable to what the clinician has written; the agent invents no clinical content.

    Stale-plan detection that does not depend on a clinician writing a note — for instance, a long-running monitoring issue whose plan has gone untouched for several days — will be handled by a separate deterministic monitor on the roadmap, on the same pattern as the Patient-of-Concern Monitor. That guardrail is not yet live.

---

## Day 5 — Discharge

By his fifth morning in hospital, Jordan feels "back to normal." He is afebrile, breathing comfortably on room air with saturations of 96%, mobilising independently, eating and sleeping well. His residual cough has largely settled. The medical team reviews him and confirms he is clinically stable and safe for discharge home with his wife.

The clinician triggers the **Handover Agent**, which generates a structured discharge summary drawn entirely from the active issues list. The summary follows an ISBAR format and covers all four issues — their current status, what was done, and what remains outstanding:

**Issue #1 — Community-acquired pneumonia (RLL):** Treated with three days of IV ceftriaxone and azithromycin, stepped down to oral cefuroxime. Oxygen weaned off by day four. Blood cultures negative, sputum mixed flora only. For completion of oral antibiotics (stop date 25–27 October). Repeat chest X-ray at six weeks to confirm resolution.

**Issue #2 — Probable COPD / possible occupational ILD:** CT showed bronchial wall thickening and mild subpleural reticulation. Short course of prednisolone commenced for airway inflammation. Salbutamol inhaler issued with technique education. Pulmonary function tests within 6–8 weeks. Gen Med outpatient review at three months to assess PFT results and CT findings, with consideration of respiratory referral.

**Issue #3 — AKI, pre-renal (resolved):** Creatinine improved from 138 to 105 with IV hydration. No residual impairment. Maintain oral hydration, avoid NSAIDs and nephrotoxins.

**Issue #4 — Incidental 5 mm LUL pulmonary nodule:** Identified on CT (day 2). Likely benign. Repeat CT chest at three months. Results to be reviewed at Gen Med outpatient clinic alongside PFTs.

The follow-up plan is explicit:

- GP review in 1–2 weeks for symptom check, antibiotic and steroid completion, and occupational physician referral
- Repeat chest X-ray at 6 weeks
- Pulmonary function tests within 6–8 weeks
- CT chest at 3 months (nodule surveillance and ILD assessment)
- Gen Med outpatient clinic at 3 months (20 January 2026) to review all results

!!! info "Safety"
    The Handover Agent generates its summary exclusively from the issues list. It cannot include information that was not documented there, and it cannot silently omit an issue that is still active. The incidental nodule — Issue #4 — appears in the discharge summary because it is a tracked issue. It cannot be dropped the way a line in a Word document can be accidentally deleted during a late-night handover rewrite.

---

## The Payoff

Over five days, Jordan's issues list grew from three to four. Plans evolved as results came in. A medication was ceased, antibiotics were stepped down, and a new issue was identified from an incidental CT finding. At discharge, every issue had a current status, a clear plan, and explicit follow-up actions.

The incidental 5 mm pulmonary nodule — exactly the kind of finding that gets lost in a paper handover sheet, exactly the kind of finding described in [The Problem](../the-problem.md) — was tracked from the moment it appeared in a CT report to the moment it was documented in the discharge plan with a three-month follow-up CT arranged.

No AI agent made a clinical decision. The Extraction Agent proposed issues from what the clinician wrote in the admission scratchpad. The Linkage Agent matched incoming results and imaging to the issues they belonged to, and flagged a finding that needed its own issue card. The Note Synthesis Agent rewrote the daily review note and proposed the unified set of plan updates, issue closures, and follow-up tasks that the changing clinical picture warranted. The Handover Agent assembled the discharge summary from the issues list. At every step, a clinician reviewed, judged, and decided.

The system helped. The clinician decided. Nothing was dropped.

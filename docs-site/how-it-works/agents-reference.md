# Agents Reference

IssuedOS runs seven AI agents and one deterministic monitoring engine. Each one has a narrow, defined job. None of them modify a patient's record directly — they return proposals for clinician review.

---

## Extraction Agent

**What it does:** Reads clinical notes — admission summaries, progress notes, scratchpads — and pulls out structured issues. Each proposed issue has a title, a short description, and a plan.

**When it runs:** Triggered when a clinician saves a note or requests a manual extraction.

!!! note
    The Extraction Agent handles initial note entry. For re-analysing a complete note in the context of the full admission, see the **Review Synthesis Agent** below.

**Example:**

> A messy admission note reads: *"50M carpenter, CAP CURB-65=2, AKI on CKD3, started IV amox/clav, CXR infiltrate L base, creatinine 185."*
>
> The agent proposes three issues: **Community-acquired pneumonia**, **Acute kidney injury on chronic kidney disease**, and **Possible COPD** (based on occupational history and spirometry mention).

!!! warning "Boundary"
    Does not add clinical interpretations, suggest diagnoses not present in the source text, or comment on whether the documented plans are appropriate.

---

## Handover Generation Agent

**What it does:** Generates a structured handover document in ISBAR format from the current issues list, tuned for the specific recipient: the oncoming junior medical officer, a MET call team, a receiving ICU, or the weekend team. Language and emphasis differ by audience. Issues are ordered by documented acuity; outstanding tasks are highlighted.

**When it runs:** Triggered on demand — typically before a shift change or ward round.

**Example:**

> A night-cover JMO is about to receive handover for a ward with eight patients. The agent produces a single document covering all active issues — in JMO format, prioritised by acuity, with all outstanding overnight tasks surfaced at the top.

!!! warning "Boundary"
    Does not generate new clinical content, infer urgency, or add clinical judgement beyond what is already recorded in the issues list. The output is a structured rendition of what the team has already documented — nothing more.

---

## Issue Index Agent

**What it does:** Keeps structured metadata in sync when clinicians edit issue text directly. Runs silently in the background — no user interface, no interruption to the clinical workflow.

**When it runs:** Triggered automatically whenever an issue is edited and saved.

**Example:**

> A clinician edits the plan section of an issue and adds a new antibiotic. The agent re-indexes the medications and results mentioned in the updated text, keeping the structured metadata current for the other agents to use.

!!! warning "Boundary"
    Does not change what the clinician wrote. Only updates the metadata that powers search and the other agents. The clinical text is always the source of truth.

---

## Review Synthesis Agent

**What it does:** Reads a complete SOAP note — together with the patient's full admission history, active issues, and outstanding tasks — and proposes a unified set of changes to the record: new issues, updates to existing issues, archived issues, and tasks derived from the note. It also produces a clean rewrite of the note for the clinician to edit before submitting.

**When it runs:** Triggered when a clinician presses the "Analyse" button after writing a note.

**Example:**

> A consultant's post-take note covers three problems in an 80-year-old with sepsis, CKD, and delirium. The agent proposes: add a new issue for hospital-acquired delirium, update the sepsis plan to reflect the antibiotic switch overnight, archive a resolved urinary retention issue, and generate a task to repeat the creatinine at 0600.

!!! warning "Boundary"
    Does not invent clinical content. Every proposed change is derived from what the clinician has written. Any interpretation in the output must be traceable to text in the source note.

---

## Knowledge Retrieval Agent

**What it does:** Searches the IssuedOS clinical knowledge catalogue — illness scripts and presenting-complaint schemas covering common acute presentations — and returns the entries whose structure best matches what the clinician has written. Informational only: it surfaces patterns, not conclusions.

**When it runs:** Triggered as part of the Analyse workflow when a clinical formulation or note text is available.

**Example:**

> A clinician writes a note for a patient with dyspnoea, bilateral crackles, and an elevated BNP. The agent matches the illness script for acute heart failure and surfaces its key discriminating features — what supports that diagnosis and what argues against it — for the clinician to consider.

!!! warning "Boundary"
    Does not propose diagnoses. Returns matched knowledge structures with confidence rankings. The clinician interprets the match — the agent does the retrieval.

---

## Dump Zone Summariser Agent

**What it does:** Takes a long external document — a specialist referral letter, a prior discharge summary from another hospital, an imaging report — and reformats it into a compact, typed summary that the other agents and the clinician can read efficiently. Produces one of four structured output types depending on the document: a history brief, an investigation brief, a specialist opinion brief, or a general correspondence brief.

**When it runs:** Triggered when an external document is submitted to IssuedOS for processing.

**Example:**

> A twelve-page discharge summary from a referring hospital arrives. The agent extracts the relevant history, key investigations, active problems at discharge, and outstanding follow-up — reformatted into a concise brief reviewable in under a minute.

!!! warning "Boundary"
    Does not add clinical opinion to the source document. The output is a structural reformatting of existing content, not an interpretation or synthesis.

---

## Patient-of-Concern Monitor

**What it does:** Watches each patient's observation stream for three clinically significant patterns using deterministic rules — not AI. When a rule fires, it raises a Patient-of-Concern (POC) flag. An optional AI layer can attach brief anticipatory notes to the flag, but this text is CDSS-validated and cached; it is never a clinical recommendation.

The three rules are:

- **Rising creatinine** — flags when creatinine rises above the KDIGO Stage 1 threshold relative to the patient's documented baseline.
- **Falling haemoglobin** — flags when Hb drops by a defined threshold within a monitoring window.
- **Elevated NEWS** — flags when the National Early Warning Score breaches a configured threshold.

**When it runs:** Each time new observations are added for a patient.

!!! warning "Boundary"
    The flag reports that a pattern has been detected in the data. It does not say what the pattern means, what caused it, or what to do. Clinical interpretation remains entirely with the clinician.

---

## Coming Soon

The following agents are on the build list but not yet implemented. They are documented here so stakeholders can see where the roadmap is heading.

---

## Relevance Agent

**What it does:** When new results arrive — blood tests, imaging reports, nursing observations — links them to the issues they are relevant to.

**When it runs:** Triggered when a new result is ingested or a clinician requests a manual linkage pass.

**Example:**

> Creatinine result of 125 µmol/L arrives. The agent links it to **Issue #2: AKI on CKD3** and surfaces: *"This result is relevant to your AKI issue."*

!!! warning "Boundary"
    Does not interpret trends, say "the AKI is improving," or recommend changes to the treatment plan. Flags relevance only — clinical interpretation stays with the clinician.

---

## Gap Detection Agent

**What it does:** Reviews the full patient picture for documentation gaps — issues with no plan update in several days, clinical concerns mentioned in notes that have not been added to the issues list, and issues where new information may warrant a plan review.

**When it runs:** Runs on a schedule and can be triggered manually before handover.

**Example:**

> - Flags: *"Issue #3 (Delirium) has not been updated in 3 days."*
> - Flags: *"An incidental pulmonary nodule is mentioned in the CT report but does not appear on the issues list."*

!!! warning "Boundary"
    Does not assess clinical significance or urgency beyond what is documented. Says *"this hasn't been updated"* — never says *"this is clinically dangerous."*

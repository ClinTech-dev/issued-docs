# Agents Reference

IssuedOS runs five agents. Each one has a narrow, defined job. None of them modify a patient's record directly — they return proposals for clinician review.

---

## Extraction Agent

**What it does:** Reads clinical notes — admission summaries, progress notes, scratchpads — and pulls out structured issues. Each proposed issue has a title, a short description, and a plan.

**When it runs:** Triggered when a clinician saves a note or requests a manual extraction.

**Example:**

> A messy admission note reads: *"50M carpenter, CAP CURB-65=2, AKI on CKD3, started IV amox/clav, CXR infiltrate L base, creatinine 185."*
>
> The agent proposes three issues: **Community-acquired pneumonia**, **Acute kidney injury on chronic kidney disease**, and **Possible COPD** (based on occupational history and spirometry mention).

!!! warning "Boundary"
    Does not add clinical interpretations, suggest diagnoses not present in the source text, or comment on whether the documented plans are appropriate.

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

---

## Handover Agent

**What it does:** Generates a structured handover document in ISBAR format from the current issues list. Issues are ordered by documented acuity; outstanding tasks are highlighted.

**When it runs:** Triggered on demand — typically before a shift change or ward round.

**Example:**

> Produces a clean handover summary for the night team covering all patients on the ward: every active issue, every outstanding task, and every result added since the last handover.

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

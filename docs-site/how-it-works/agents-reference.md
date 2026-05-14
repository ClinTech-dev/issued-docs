# Agents Reference

IssuedOS runs eight AI agents and one deterministic monitoring engine. Each one has a narrow, defined job. None of them modify a patient's record directly — they return proposals for clinician review.

---

## Extraction Agent

**What it does:** Reads clinical notes — admission summaries, progress notes, scratchpads — and pulls out structured issues. Each proposed issue has a title, a short description, and a plan.

**When it runs:** Triggered when a clinician saves a note or requests a manual extraction.

!!! note
    The Extraction Agent handles initial note entry. For re-analysing a complete note in the context of the full admission, see the **Note Synthesis Agent** below.

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

## Note Synthesis Agent

**What it does:** Reads a clinician's draft note — together with the patient's full admission history, active issues, and outstanding tasks — and proposes a unified set of changes to the record: new issues, updates to existing issues, archived issues, and tasks derived from the note. It also produces a clean rewrite of the note in the appropriate template for the clinician to edit before submitting. The agent handles the full range of note types written on the ward: medical admission, post-take round, daily review, consultant round, and ad-hoc review — with the rewrite style tuned to each.

**When it runs:** Triggered when a clinician finishes a draft note and asks for it to be analysed. The encounter type (e.g. consultant round vs daily review) is supplied alongside the draft, so the rewrite arrives in the style appropriate to that note.

**Example:**

> A consultant's post-take note covers three problems in an 80-year-old with sepsis, CKD, and delirium. The agent proposes: add a new issue for hospital-acquired delirium, update the sepsis plan to reflect the antibiotic switch overnight, archive a resolved urinary retention issue, and generate a task to repeat the creatinine at 0600. The rewritten note arrives in post-take round format.

!!! warning "Boundary"
    Does not invent clinical content. Every proposed change is derived from what the clinician has written. Any interpretation in the output must be traceable to text in the source note.

---

## Linkage Agent

**What it does:** Handles inbound clinical fragments that are not full notes — a result pasted into a scratchpad, a snippet forwarded from Teams, an observation arriving from another system. The agent reads the fragment against the patient's current issues and proposes structured linkages: which issue this result belongs to, whether the fragment hints at a concern not yet on the issues list, and whether a result on an existing issue warrants a plan review by the clinician.

**When it runs:** Triggered when an observation, result, or fragment is ingested through any channel, and again when a clinician modifies a previously proposed linkage with their own rationale — so the agent can re-reason with the new context.

**Example:**

> A clinician pastes *"Cr 195 (was 156)"* into a scratchpad. The Linkage Agent matches the rising creatinine to the active AKI issue and proposes a linkage with a plan-review flag, so the clinician sees that the result has landed on a problem that may need attention.

!!! warning "Boundary"
    Does not interpret what the result means or suggest what to do about it. It says *"this fragment relates to this issue, and the plan may want a fresh look"* — never *"the AKI is worsening"* or *"escalate fluids"*. Clinical interpretation stays entirely with the clinician.

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

## Retired roadmap items

Two agents that previously appeared on the roadmap have been folded into the agents above. Their responsibilities did not go away — they moved.

- **Relevance Agent** — its job (linking incoming results to existing issues) is now done by the **Linkage Agent**, which handles a broader set of inbound fragments through a single pipeline.
- **Gap Detection Agent** — split into three jobs handled elsewhere: completeness review against documentation templates is now done by the **Note Synthesis Agent** (using a structured completeness skill); flagging issues whose plans have gone stale will be handled by a small deterministic monitor on the same pattern as the Patient-of-Concern Monitor; and surfacing concerns mentioned in notes that aren't yet on the issues list is now done by the **Linkage Agent**.

The original architectural decision — that each agent has one narrow job and no agent performs clinical interpretation — is unchanged. The map of which agent does which job has been redrawn to remove overlap.

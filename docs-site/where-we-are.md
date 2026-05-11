# Where We Are

An honest summary of what exists, what's coming next, and where we still need to make decisions together. As of May 2026.

---

## What's Built

### IssuedOS (the AI backend)

Seven AI agents are working end-to-end, plus one deterministic monitoring engine:

- The **Orchestrator** receives requests from the Issued app and coordinates which other agents run and in what order.
- The **Extraction Agent** reads clinical notes written in free text and pulls out structured issues — problems, plans, tasks — ready for clinician review.
- The **Issue Index Agent** keeps the structured issue list in sync when a clinician edits a note directly, so the underlying data stays consistent with what's on screen.
- The **Review Synthesis Agent** powers the "Analyse" button. When a clinician finishes writing a SOAP note, this agent reads the full admission context — prior notes, active issues, outstanding tasks — and proposes a unified set of changes: new issues to add, existing issues to update, tasks to create, and a clean rewrite of the note for the clinician to review before submitting.
- The **Knowledge Retrieval Agent** searches an in-repo catalogue of presenting-complaint schemas and illness scripts — covering common acute medical presentations — and surfaces the entries whose structure best matches what the clinician has written. It matches; it does not diagnose.
- The **Dump Zone Summariser Agent** reformats long external documents — specialist letters, prior discharge summaries, imaging reports — into a compact, typed brief that the other agents and the clinician can read efficiently.
- The **Handover Generation Agent** produces structured handover documents from the current issues list, tuned for the specific recipient: the oncoming junior medical officer, a MET call team, a receiving ICU, or the weekend team.
- The **Patient-of-Concern Monitor** is not an AI agent — it is a deterministic rule engine that watches observation streams for three clinically significant patterns: rising creatinine (KDIGO criteria), falling haemoglobin, and an elevated National Early Warning Score (NEWS). When a rule fires, it raises a flag. An optional AI layer adds brief anticipatory notes to the flag, but this text is CDSS-validated and never constitutes a recommendation.

Supporting infrastructure that's also in place:

- A **safety validation layer** that checks every AI output before it reaches the clinician. Nothing goes to the app without passing these checks.
- A **REST API** that connects IssuedOS to the Issued app. This is the single channel all communication flows through.
- An **event system** that tracks what agents are doing in real time, step by step.
- A **clinical knowledge catalogue** of 41 illness scripts and 19 presenting-complaint schemas, covering common acute medical presentations across internal medicine, emergency, and surgery.
- A **developer dashboard (Observer)** for visualising agent operations during testing. This is a development tool — clinicians would never see it.
- A **command-line test harness** for running agents against sample clinical notes without needing the full app running.
- All the **data models** that define how information is structured and passed between the two systems.

### Issued (the web app)

The clinical interface clinicians interact with:

- A **clinical text editor** built for ward use, with purpose-built support for structured issues, note sections, tasks, and inline AI suggestion previews.
- An **AI suggestion panel** where clinicians review what the agents have proposed, and accept, edit, or reject each suggestion before anything is committed.
- A **scratchpad editor** for quick notes that can be sent to the AI for analysis without touching the main progress note.
- A **version history system** that records every change to a patient's progress note, so nothing is lost.
- **Autosave infrastructure** running in the background to prevent data loss during ward round interruptions.
- A **typed API client** that handles all communication with IssuedOS, keeping the two systems in sync on data contracts.
- **Live agent streaming** — the typed API client now receives agent responses via server-sent events (SSE), so the interface updates in real time as agents work rather than waiting for a complete response.

---

## What's Next

The next agents and infrastructure on the build list:

- **Relevance Agent** — will link new results (blood tests, imaging, observations) to the issues they affect, so clinicians can see at a glance what's changed.
- **Gap Detection Agent** — will review the full patient picture for stale plans, untracked concerns, and documentation gaps.
- **Knowledge catalogue review** — 60 illness script and schema bundles are awaiting cofounder clinical review before they are used in live agent responses.
- **End-to-end integration testing** — a full workflow test from admission note through to handover, covering the complete pipeline.
- **Production database** — replacing the in-memory test data currently used in development with persistent storage.

---

## Open Decisions

These need cofounder input before we can move forward.

### Database technology

We need to choose which database system to use for production. Options are still being evaluated. This decision affects the production database build above.

### Documentation completeness checking

This would be a feature that checks whether history sections in a note are complete against clinical documentation templates — flagging things like a missing family history or an incomplete medication list.

It's currently parked. The concern is that depending on how it's implemented, it could cross into diagnostic or clinical decision support territory, which has regulatory implications in Australia. We need to think carefully about where the line is before building this.

### Section Review Agent — one or many?

When we get to reviewing individual sections of a progress note (history, examination, assessment), do we build one configurable agent that handles all sections, or separate specialist agents for each?

This is a future (P2) decision — we don't need to resolve it now. But it's worth keeping in mind as the architecture evolves.

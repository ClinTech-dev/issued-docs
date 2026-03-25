# Where We Are

An honest summary of what exists, what's coming next, and where we still need to make decisions together. As of March 2026.

---

## What's Built

### IssuedOS (the AI backend)

Three AI agents are working end-to-end:

- The **Orchestrator** receives requests from the Issued app and coordinates which other agents run and in what order.
- The **Extraction Agent** reads clinical notes written in free text and pulls out structured issues — problems, plans, tasks — ready for clinician review.
- The **Issue Index Agent** keeps the structured issue list in sync when a clinician edits a note directly, so the underlying data stays consistent with what's on screen.

Supporting infrastructure that's also in place:

- A **safety validation layer** that checks every AI output before it reaches the clinician. Nothing goes to the app without passing these checks.
- A **REST API** that connects IssuedOS to the Issued app. This is the single channel all communication flows through.
- An **event system** that tracks what agents are doing in real time, step by step.
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

---

## What's Next

The next agents and infrastructure on the build list:

- **Relevance Agent** — will link new results (blood tests, imaging, observations) to the issues they affect, so clinicians can see at a glance what's changed.
- **Gap Detection Agent** — will review the full patient picture for stale plans, untracked concerns, and documentation gaps.
- **Handover Agent** — will generate structured ISBAR handover documents from the active issue list.
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

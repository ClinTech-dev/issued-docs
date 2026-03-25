# Glossary

Plain English definitions for terms used throughout the IssuedOS documentation. Alphabetical order.

---

## Acuity

How urgent a clinical issue is. IssuedOS uses four levels: **urgent**, **active**, **monitoring**, and **archived**. Acuity drives the order of handover documents and flags issues that need attention — without making any clinical judgement about what to do about them.

---

## Agent

A specialist AI that does one specific, narrow job — reading notes, linking results, generating handover documents. Think of it like a specialist registrar you can consult: you give them information, they give you a structured opinion. They never act on their own; a clinician always has the final say.

---

## Audit Trail

The complete, immutable log of every AI suggestion and every clinician decision. It records who proposed what, who accepted or rejected it, and when — the regulatory evidence that a human was always in control of any change to the patient record.

---

## CDSS

Clinical Decision Support System. A regulatory category under Australia's Therapeutic Goods Administration (TGA) that defines what an AI system can and cannot do in a clinical setting. IssuedOS is designed to qualify for CDSS exemption by never making diagnoses or treatment recommendations — only surfacing information for clinicians to act on.

---

## Ghost Preview

A visual preview in the Issued editor showing where an AI suggestion would appear if accepted. Displayed with a dashed purple border and a sparkle icon to distinguish it clearly from confirmed clinical content — so the clinician always knows what is real and what is proposed.

---

## ISBAR

A standard clinical handover format: **Introduction**, **Situation**, **Background**, **Assessment**, **Recommendation**. The Handover Agent generates documents in this structure so the output is immediately familiar to any clinician who has ever done a ward handover.

---

## Issue Index Agent

A background AI agent that keeps structured metadata — medications, results, title and plan text — in sync whenever a clinician edits issue content directly. It runs silently, with no interface and no interruption to the clinical workflow. The clinician's text is always the source of truth; the agent only updates the metadata that the other agents rely on.

---

## Issues List

The structured list of a patient's active clinical problems, each with a title, description, plan, and acuity level. This is the core data structure that the entire Issued system revolves around — the thing that is built, maintained, and handed over.

---

## Mutation

A proposed change to patient data that an AI agent suggests. Could be "add this issue," "link this result," or "update this plan." Every mutation is reviewable — the clinician accepts it, edits it, or rejects it. Nothing in the patient record changes until a clinician acts on a mutation.

---

## Orchestrator

The coordinator agent that receives incoming requests and decides which specialist agent should handle them. It does not perform any clinical reasoning itself — it routes work to the right specialist and coordinates the response.

---

## Progress Note

The living document for each patient in Issued. There is one per patient, and it evolves continuously — it is not a new document each day. It contains structured sections for History, Examination, Investigations, Issues, and Tasks, providing a single place where the team's clinical thinking accumulates over the admission.

---

## Scratchpad

A quick, temporary note in Issued — the equivalent of tearing off a notepad page at the bedside. Scratchpads can be analysed by AI agents to extract issues or flag relevant information, but they are ephemeral inputs, not part of the patient's clinical record.

---

## StateProvider

The system's way of accessing patient data. Designed as a pluggable interface so the same agents can work with different underlying data sources — a test fixture, a database, or another system's API — without the agents themselves needing to know where the data comes from.

---

## Trigger

The event that activates an agent. For example, "a clinical note was entered" or "new blood results arrived." Each trigger type routes to the appropriate specialist agent, so the right job is done at the right moment without requiring clinician intervention to initiate it.

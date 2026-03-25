# The Problem

## The daily reality

Every morning, somewhere in every Australian teaching hospital, a registrar sits down before the ward round and reconstructs the night. They scroll through the overnight nursing notes, chase up pathology results, check the observation charts for the patient who spiked a temperature at 3 am, and try to piece together what happened to each of the fifteen or twenty patients they are responsible for. This takes between thirty minutes and an hour — every single day — before they have even seen a patient.

The tool they use to do this is almost always a Word document. Or a printed spreadsheet. Or, on a busy weekend, a piece of paper handed over in a corridor. The "handover sheet" is the beating heart of inpatient care, and in most hospitals it lives outside every clinical system. It is manually typed, manually updated, and manually carried from shift to shift.

Issues get dropped. Not because clinicians are careless — they are not — but because the system makes it nearly impossible to keep track of everything that is evolving in a complex patient at the same time.

## What goes wrong

Consider a patient admitted with sepsis. On day two, a chest CT finds an incidental 8 mm pulmonary nodule. The radiologist flags it in the report. The registrar reads the report, notes it, and moves on — there are fourteen other patients to see. The nodule never makes it onto the active issues list. The patient is discharged five days later with a letter that does not mention it. Nobody follows it up. That nodule may matter enormously or not at all, but no one will ever know, because it fell through the gap between a radiology report and a handover document.

Or consider a simpler failure: a plan that goes stale. The team decides on Monday to repeat a potassium level in 24 hours. By Tuesday, the team has changed, the handover sheet has been retyped from memory, and that instruction is gone. The potassium is never rechecked. A medication decision is made without it.

These are not edge cases. Handover failures are consistently identified as a leading cause of preventable adverse events in hospital settings. The information existed. The clinician who generated it was competent. The system just had nowhere to hold it.

## Why existing tools don't solve this

Electronic medical records were not built to manage problems — they were built to store records. The problem list in most EMRs is a static field that clinicians rarely touch after admission, because updating it competes with every other task in a twelve-hour shift. A 2025 Australian study confirmed what most junior doctors already know: they are still manually constructing handover documents despite working in hospitals with fully deployed EMRs. The EMR does not replace the Word doc; it just runs alongside it.

AI scribes are a genuine step forward for documentation, but they solve a different problem. They capture what was said in a consultation and turn it into a note. They do not track whether the plan in that note was carried out, whether the result it was waiting on ever came back, or whether the issue it described is still active three days later. The note is written and filed, and the issue it describes floats free.

Task management tools — checklists, to-do apps adapted for clinical use — track actions but not reasoning. They cannot tell you why a task exists, what clinical question it was meant to answer, or whether that question has since been resolved by other information. Research shows that 50% of nurses find EMR-generated printouts cumbersome, and 69% report being burdened with irrelevant information. The problem is not a shortage of data. It is the absence of any system that understands which data matters, for which patient, right now.

No dedicated intelligent issues list management tool currently exists anywhere in the world.

## What we're building instead

Issued is a living issues list — one that evolves with the patient rather than being recreated from scratch each morning. It is built around the way clinicians actually think: in terms of active problems, open questions, and pending decisions, not in terms of documents and timestamps.

The AI in Issued reads clinical information — notes, results, observations — and surfaces things that might be relevant to the active issues. It might notice that a result has come back and connects to an open question on the list, or that a new concern has appeared in overnight notes that does not yet appear anywhere as a tracked issue. It surfaces these observations for the clinician to review. It never acts on them autonomously. Every change to the issues list requires a clinician to read it, judge it, and accept or reject it.

The goal is not to replace clinical judgment. It is to make sure that when a clinician makes a judgment, they have the full picture in front of them — and that the incidental nodule, the stale plan, and the unanswered question from two nights ago are not waiting silently to become someone's adverse event.

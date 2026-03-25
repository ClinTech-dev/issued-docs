# Safety

Issued is built on a simple commitment: the AI does the reading, clinicians do the deciding. Every architectural choice described here follows from that commitment. These are not aspirational policies — they are enforced in code.

---

## AI proposes, clinicians decide

Nothing changes in a patient's record without a clinician actively choosing to make it happen. Every suggestion produced by IssuedOS is returned to the clinician as a proposal: they can accept it, edit it, or reject it. There is no auto-apply, no background update, no "accept all." AI content does not enter the active issues list through any path that bypasses a human decision.

This design holds whether the suggestion is small (linking a blood result to an existing problem) or significant (flagging a new issue that has emerged in an overnight note). The size of the proposal does not change the requirement for explicit approval. Every decision — acceptance, edit, or rejection — is logged with the clinician's identity and a timestamp.

---

## The system never crosses into diagnosis or treatment

IssuedOS agents surface information and flag gaps. They do not interpret. An agent might observe that a result has come back and appears relevant to an open question on the issues list; it will not tell you what that result means or what to do about it. The system surfaces what is there — it does not tell clinicians what to conclude.

This boundary is not just a design choice.

!!! info "Regulatory context: TGA CDSS exemption"
    In Australia, software that makes clinical decisions — diagnosing conditions or recommending treatments — is regulated as a medical device by the Therapeutic Goods Administration (TGA). Systems that stay within the role of Clinical Decision Support Software (CDSS), providing information for clinicians to interpret rather than making decisions themselves, qualify for an exemption from device registration. IssuedOS is architecturally designed to remain within that exemption. Agents are explicitly prohibited from producing diagnostic conclusions or treatment recommendations. This is enforced at the instruction level for each agent and verified by a separate validation layer on every output.

Staying within these boundaries is a regulatory requirement, and the architecture is built to make it impossible for an agent to drift outside them.

---

## Two layers of safety checking

Every output from an IssuedOS agent passes through two independent safety checks before it reaches the clinician. Neither layer can be bypassed, and they operate independently — a failure in either one suppresses the output.

The first layer is built into each agent's instructions. Every agent has explicit, role-specific guidance about what it must not do — what kinds of conclusions it must not draw, what language it must not use, what it must refer back to the clinician rather than resolve itself. These instructions are tailored to the agent's specific function; the extraction agent's boundaries are not the same as the gap-detection agent's, because their tasks are different and the failure modes are different.

The second layer is a separate safety validator that runs on every agent output after it is produced. This validator uses deterministic pattern matching to check for boundary violations — phrases or structures that would constitute a diagnostic conclusion, a treatment recommendation, or any other output that should not reach the clinician. This is not another AI model making a judgement call; it is code with explicit rules. If the validator flags a concern, the output is suppressed and the incident is logged. The clinician sees nothing from that agent call, and the system records that the output was held.

---

## Everything is logged

The audit trail is complete. Every agent invocation is recorded — what triggered it, what it produced, and what the safety checks concluded. Every suggestion that reaches a clinician is recorded. Every decision a clinician makes on a suggestion — accept, edit with the edit captured, or reject — is recorded with their identity and the exact time.

If anyone needs to ask "what did the AI propose for this patient, and what did the clinician decide?" — that question has a complete, retrievable answer. The log does not age out or get summarised. The full record of every AI interaction is available for clinical audit, governance review, or incident investigation.

This audit trail is the concrete evidence that human oversight was maintained throughout. It is not a report generated after the fact — it is a live record written as each decision is made.

---

*For the detailed safety architecture, including the multi-stage validation pipeline and per-agent boundary specifications, see the [Safety Design Decisions](https://github.com/your-org/issuedos-mvp/blob/main/SAFETY_DESIGN_DECISIONS.md) document in the IssuedOS repository.*

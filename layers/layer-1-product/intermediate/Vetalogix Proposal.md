# Vetalogix Build Proposal

**Prepared by:** Dual Boot Partners
**Client:** K9 Wand / WAND Holdings LLC
**Date:** April 2026
**Version:** 1.0

---

## Overview

Vetalogix is a veterinary diagnostic decision-support tool for dog owners. A user describes their dog's symptoms through a structured input form, and the system returns a ranked list of possible conditions, each scored, urgency-tiered, and explained in plain language, to help the owner decide how quickly they need to act.

The core of the product is a proprietary weighted-scoring engine called the Valtorta Logic Engine, which runs against a dataset of 397 conditions (the Master IDS). This deterministic engine does the clinical math; an LLM layer generates plain-language narratives on top of the results. The output is not a diagnosis; it is a triage signal designed to inform, not replace, veterinary care.

The system is composed of four technical layers: a structured input UI and results dashboard, a data collection and packaging service, the core AI processing engine, and a static reference data library. A fifth layer, a data archive and admin console, is scoped for a later phase.

Dual Boot Partners will design and build the complete technical system: the diagnostic engine, the supporting data pipeline, the results dashboard, and the integration layer between them. The marketing website rebuild and brand design are handled by a separate contracted agency. We assume the broader wvere.com platform will be operational when this system is integrated.

---

## System Definition and Core Capabilities

### What the System Does

A dog owner logs into the platform and opens the Vetalogix diagnostic tool. They fill out their dog's profile (breed, age, weight, sex, medications, vaccination history, and lifestyle data) and then select symptoms from a structured dropdown menu covering 355 clinical symptoms across categorized groups. They also answer five binary stability questions (appetite, energy, gum color, stool quality, temperature) that the engine uses to calibrate urgency.

Once submitted, the system runs the full scoring algorithm against all 397 conditions in the Master IDS, applies breed-specific risk modifiers, medication factors, age adjustments, and stability penalties, then sorts and surfaces the top 10 results. Each result is assigned an urgency tier (Critical, Urgent, or Monitor) and an LLM-generated narrative explaining why the condition matched this dog's profile.

The user sees their results in a personal dashboard (the Ethos Dashboard), with each condition color-coded by urgency and accompanied by a plain-language explanation.

### The Five System Layers

**Layer 0: User Interface and Dashboard**
The structured input form and results dashboard. Captures the full dog profile (biometrics, lifestyle, medications, vaccinations), symptom selections (up to 3 primary and 10 secondary, each with category, symptom, and duration), and the five Sentry binary fields. Packages this into a clean JSON payload and strips all owner PII before passing it downstream. Displays results back to the user in the Ethos Dashboard.

**Level 1: Data Collection Layer**
A passive, non-interpretive service. No AI, no scoring. Receives the structured input from the UI, packages it into a single Sovereign Case Object, verifies PII has been stripped, and forwards it to Level 2. Its only job is clean handoff.

**Level 2: Core AI Processing Engine**
The diagnostic brain. Receives the Sovereign Case Object and runs the full Valtorta Logic Engine: the TDS formula across all 397 conditions, querying Level 3 for breed and condition reference data. Applies all scoring components: Anatomical Gate, Symptom Velocity, Age Factor, Breed Surge, Medications Factor, History Surge, Acute Emergency Surge, Sentry Stability Brakes, and Prevalence Headwinds. Sorts results, extracts the Top 10, assigns urgency tiers, generates Diagnostic Handshake narratives via LLM, and flags any Logic Conflicts. Returns the final Valtorta Response to the user's dashboard.

**Level 3: Resource Library**
A static, locked reference data repository. Stores the Master IDS (397 conditions with symptom weights, breed risk flags, lethality levels, and prevalence headwinds), the Breed Bio table, Merck Manual reference data, and Condition Tags. Level 2 queries this library during scoring. Data can be updated without touching application code.

**Level 4: Data Archive and Admin Console** *(Phase 2)*
Long-term data archive and owner admin layer. Stores the complete session log for every case. Provides admin-only exports of anonymized case data (Sovereign Audit) and an admin-only logic update tool to push updates to the Level 3 Resource Library without a full redeployment (Sovereign Injection). Runs automated encrypted backups. Strictly limited to the platform owner's admin account.

---

## The Valtorta Logic Engine

The scoring engine is the intellectual core of the product. For every case submitted, it calculates a Total Diagnostic Score (TDS) for each of the 397 conditions in the Master IDS:

```
TDS = (Symptom_Vector × Age_Factor) + Breed_Surge + Medications_Factor + History_Surge + Acute_Surge − Stability_Penalty + Prevalence_Headwind
```

The components work as follows:

- **Anatomical Gate:** hard exclusion for conditions biologically impossible for the patient (e.g., gender-specific conditions). Score set to -100; excluded from results.
- **Symptom Velocity Engine:** each submitted symptom is matched against the condition's weight in the IDS. Primary symptoms carry a 1.5× multiplier; secondary symptoms carry 1.0×. Weights can be negative for mutual exclusion logic.
- **Age Factor:** 1.5× for geriatric conditions when the dog is over 8 years old; 0.5× for pediatric conditions under 1 year; 1.0× otherwise.
- **Breed Surge:** +1.0 when the patient's breed appears in a condition's high-risk breed list.
- **Medications Factor:** applied based on current medications in the patient profile. *(Exact interaction rules to be confirmed during Phase 0.)*
- **History Surge:** +2.0 when the condition matches the dog's known medical history.
- **Acute Emergency Surge:** +0.5 for crisis-type conditions when symptom duration is seconds or minutes.
- **Sentry Stability Brakes:** for high-lethality conditions, each stable Sentry marker (normal appetite, energy, gums, stool) applies a -2.0 penalty, suppressing high-severity matches when the dog's overall stability is normal.
- **Prevalence Headwinds:** a penalty of 0.0, -0.25, or -1.0 applied to rare conditions to prevent statistical outliers from dominating results.

Results are sorted descending, the Top 10 are extracted, and urgency tiers are assigned:

| Zone | Score Range | Action | Color |
|:---|:---|:---|:---|
| **Critical Strike** | 3.0+ | EMERGENCY: GO NOW | Neon Red |
| **Urgent Consult** | 1.5 – 2.9 | CLINICAL REVIEW REQUIRED | Amber Yellow |
| **Monitor / Guidance** | Below 1.5 | OBSERVE & DOCUMENT | Cobalt Blue |

---

## Key Assumptions

This proposal is built on the following assumptions. If any of these change, the scope, timeline, and cost estimates will need to be revisited.

1. **The Master IDS dataset is provided by the client and validated before Phase 1 begins.** The 397-condition dataset, including symptom weights, breed risk flags, lethality classifications, and prevalence headwinds, is the core of the product. Until it is in a usable format and confirmed as clinically accurate, Level 2 and Level 3 cannot be built. Resolving this is the primary objective of Phase 0.

2. **A licensed veterinarian or clinical authority is responsible for validating the IDS data.** The clinical accuracy of the symptom weights and condition classifications is not within Dual Boot's scope. We strongly recommend the client confirm that a licensed veterinary professional has signed off on the dataset before it is used in a live product.

3. **The Medications Factor interaction rules will be defined during Phase 0.** The exact logic for how specific medications boost or suppress condition scores, and where that mapping is defined in the IDS, is currently unresolved.

4. **The wvere.com platform is operational at integration time.** We scope and build the Vetalogix diagnostic interface (the input form and Ethos Dashboard) as a self-contained module. We are not responsible for the broader site rebuild.

5. **The Merck Manual API access and licensing are the client's responsibility.** We will build the integration to cache Merck data periodically in Level 3; the client must confirm they have the required commercial license before that integration is activated.

6. **Regulatory framing is the client's responsibility.** The output of this system uses urgency-tier language ("EMERGENCY: GO NOW") that may attract scrutiny under FDA or USDA guidelines depending on how it is marketed. The client must obtain legal input on how outputs must be framed and disclaimed before the product goes live. This may affect UX copy throughout the system.

7. **The Sovereign Injection feature (Phase 2) updates the Level 3 data assets, not the model itself.** Uploading a new logic manifest will update IDS rows, condition entries, and symptom weights in near-real time. It does not trigger model fine-tuning, which is a multi-hour process and a different system entirely.

8. **Engineering access to production Levels 3 and 4 will be handled via a break-glass model.** A fully air-gapped production environment with no engineering access creates operational risk: there is no one to respond if the AI engine starts returning bad results or a service goes down. We recommend a break-glass access model with full audit logging rather than a permanent hard air-gap.

---

## Why a Phased Approach?

Building a system of this complexity, a proprietary AI diagnostic engine sitting on top of a 397-condition clinical dataset, requires a structured rollout for two reasons.

**First, the Master IDS is the product.** Every other component is infrastructure. The value of Vetalogix lives in the quality and structure of the dataset and the calibration of the scoring formula. Until Phase 0 is complete and the dataset is validated, we cannot reliably estimate the effort required to build Level 2 or Level 3. Phase 0 exists to de-risk this.

**Second, the core diagnostic loop must work before anything else matters.** Phase 1 delivers the minimum system that a dog owner can actually use: submit symptoms, receive a scored and explained result. Everything in Phase 2 (the data archive, the admin console, the logic update tool) operates on top of a working Phase 1 system.

This structure keeps the first deliverable focused and testable, allows us to validate the engine against real input before committing to the full platform, and gives the client a working product at the end of Phase 1 rather than waiting for the full build.

---

## Roadmap and Implementation Phases

### Phase 0: Discovery and Design

The primary objective of this phase is to close all open questions that currently block scoping. Most importantly: receive the Master IDS in a usable format, assess its structure and data quality, and define the exact schema Level 2 will work against. In parallel, we finalize all UI/UX design, define the data flow and API contracts between layers, finalize infrastructure decisions on GCP (Vertex AI Agent, Pub/Sub or equivalent messaging, Cloud Run or GKE for Level 2, Cloud Firestore/BigQuery for Level 4), and produce the full technical specification for Phase 1.

Deliverables from this phase include the IDS schema and data quality assessment, finalized UI wireframes for the input form and Ethos Dashboard, the complete system architecture document, the API and data contract specifications for all layers, and a refined Phase 1 scope and estimate.

**Estimated Effort: 80 – 120 hours** (~2–3 weeks) | **Est. Cost: $6,800 – $10,200**

---

### Phase 1: MVP - Core Diagnostic Engine

This phase builds the complete, end-to-end diagnostic loop. A user can log in, fill out their dog's profile and symptoms, receive a scored and urgency-tiered result with plain-language explanations, and view their results in the Ethos Dashboard.

**What we build:**

- **Level 1: Data Collection Service:** The passive data packaging layer. Receives structured input from the UI, validates the payload structure, strips PII, and forwards the Sovereign Case Object to Level 2 via a secure messaging channel (Pub/Sub or equivalent).

- **Level 2: Valtorta Logic Engine:** The full TDS scoring implementation against all 397 conditions. All formula components (Anatomical Gate, Symptom Velocity, Age Factor, Breed Surge, Medications Factor, History Surge, Acute Emergency Surge, Sentry Stability Brakes, Prevalence Headwinds). Top 10 result extraction, urgency tier assignment, Logic Conflict flagging. LLM integration (Vertex AI) for Diagnostic Handshake narrative generation. Merck Manual validation check against cached reference data.

- **Level 3: Resource Library:** Structured storage for the Master IDS, Breed Bio table, Merck Manual cached data, and Condition Tags. Queryable by Level 2 during scoring. Built to allow data updates without code changes.

- **Layer 0: Diagnostic UI and Ethos Dashboard:** The structured input form covering 355 symptoms across categorized dropdowns. The full K9 Ethos Profile capture (biometrics, lifestyle, medications, vaccinations). Primary symptom selector (up to 3, 1.5× multiplier) and secondary selector (up to 10, 1.0×), each with category, symptom, and duration. Five Sentry binary fields and contextual fields (Recent Travel, Primary Region). The Ethos Dashboard displaying results: condition name, SGS score, urgency tier with color coding, and Diagnostic Handshake narrative.

- **Security and infrastructure baseline:** VPC isolation between layers, IAM roles and service account configuration, Cloud Audit Logs, and the biometric login gate (D-Scope) protecting the Ethos Dashboard.

**Estimated Effort: 480 – 640 hours** (~12–16 weeks) | **Est. Cost: $40,800 – $54,400**

---

### Phase 2: Full Platform - Archive, Admin, and Hardening

With the core engine validated in production, Phase 2 completes the platform: the data archive, the admin console, and the operational hardening needed for long-term sustainable operation.

**What we build:**

- **Level 4: Data Archive:** Cloud Firestore for real-time session storage; BigQuery for analytical queries across the full case history. Vertex AI Vector Search for trend analysis across cases. Every session's full Synthesis Log (raw input + scores + Valtorta Response) is archived here automatically.

- **Admin Console:** Sovereign Audit export: the platform owner selects a date range or case type and exports a clean, anonymized CSV. Sovereign Injection: the platform owner uploads a new logic manifest (updated IDS rows, revised condition entries, new symptom weights) and the system propagates the changes to the Level 3 Resource Library near-immediately, without engineering intervention or redeployment.

- **Resurrection Protocol (Pillar 26):** Automated encrypted backup that mirrors Level 4 data to an off-platform location. Backup destination and frequency to be confirmed with the client.

- **Access hardening:** Break-glass access model for production Levels 3 and 4, with full Cloud Audit Log coverage on every access event.

- **Architecture foundations for future multimodal input:** The Phase 1 system is designed so that a future preprocessing layer can extract clinical symptom data from uploaded images or video and inject it into the standard scoring pipeline. No multimodal functionality is built in Phase 2; only the architectural seams that make it addable without a redesign.

**Estimated Effort: 280 – 400 hours** (~7–10 weeks) | **Est. Cost: $23,800 – $34,000**

---

## Agile Adaptation and Prioritization

This roadmap defines a clear path, but it is not rigid. The modular architecture means individual components can be accelerated or deferred based on what we learn during Phase 0 and what the client needs to prioritize. If specific admin features become critical earlier than planned, or if the IDS data requires more preparation work than anticipated, we adjust the queue accordingly. We maintain an open feedback loop throughout the engagement; scope decisions are made collaboratively, not handed down.

---

## Security, Privacy, and Compliance

The security model is built around the sensitivity of health data and the proprietary nature of the IDS dataset.

**Data isolation:** Each system layer runs in its own VPC perimeter with IAM-enforced service account permissions. Level 2 can query Level 3; nothing else can. Levels 3 and 4 are inaccessible to the general engineering team post-deployment.

**PII handling:** Owner PII is stripped at the point of submission before the payload leaves Level 1. Only clinical and dog data moves through the diagnostic pipeline. Anonymized case data is all that is stored in Level 4.

**Access control:** The admin console (Level 4) is limited to a single owner account. All access attempts to the inner perimeters are logged via Cloud Audit Logs with full telemetry.

**Break-glass access:** We recommend, and will build, a time-limited, audited break-glass access model for engineering to respond to production incidents in Levels 3 and 4. A fully permanent air-gap creates operational risk with no upside.

**Regulatory note:** The urgency-tier language used in results ("EMERGENCY: GO NOW") may attract regulatory scrutiny depending on how the product is marketed. The client is responsible for obtaining legal input on output framing and disclaimers before launch. We will implement whatever copy and disclaimer structure legal confirms is required.

---

## Suggested Team Composition

To execute this roadmap, we propose the following team structure for Phase 1 and Phase 2:

- **Product Manager:** owns requirements, backlog prioritization, and client communication throughout the build
- **Tech Lead (Full-Stack):** system architecture ownership, Level 1 and Level 2 implementation lead
- **AI / ML Engineer:** Vertex AI Agent implementation, LLM integration, TDS formula implementation, Level 3 data pipeline
- **Backend Engineer:** Level 1 service, Pub/Sub integration, Cloud infrastructure, Level 4 data archive (Phase 2)
- **Frontend Engineer:** Layer 0 UI, symptom selector, Ethos Dashboard, D-Scope auth integration
- **QA Engineer:** test coverage for scoring engine correctness, regression testing for IDS updates, end-to-end flow validation
- **DevOps / Cloud Engineer** (part-time): GCP infrastructure, VPC configuration, IAM, CI/CD pipelines

**Clinical data validation is not within Dual Boot's scope.** We strongly recommend the client engage a licensed veterinary professional to validate the Master IDS (its symptom weights, condition classifications, lethality tiers, and breed risk flags) before Phase 1 begins. The accuracy of the product is entirely dependent on the quality of that dataset.

---

## Conclusion

Vetalogix is a well-defined product with a clear technical path. The scoring architecture is sound, the system layers are distinct and buildable, and the core value proposition, a structured, scored, plain-language triage signal for dog owners, is achievable within the phase structure outlined here.

The single most important pre-build step is resolving the Master IDS. Every estimation, every design decision, and every build dependency traces back to that dataset. Phase 0 exists precisely to convert that uncertainty into a confirmed scope.

Dual Boot Partners is prepared to lead this engagement from discovery through delivery. The phased structure keeps risk low, delivers a working product at the end of Phase 1, and gives the client a platform they can extend without rebuilding.

**Total Estimated Effort (all phases): 840 – 1,160 hours**
**Total Estimated Cost: $71,400 – $98,600**

*Estimates are based on current assumptions. Final Phase 1 and Phase 2 estimates will be refined at the conclusion of Phase 0.*

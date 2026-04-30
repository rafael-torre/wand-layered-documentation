# Vetalogix Build Proposal

**Prepared by:** Dual Boot Partners
**Client:** K9 Wand / WAND Holdings LLC
**Date:** April 2026
**Version:** 1.1

---

## Overview

Vetalogix is a veterinary diagnostic decision-support tool for dog owners. A user describes their dog's symptoms through a structured input form, and the system returns a ranked list of possible conditions, each scored, urgency-tiered, and explained in plain language, to help the owner decide how quickly they need to act.

The core of the product is a proprietary weighted-scoring engine called the Valtorta Logic Engine, which runs against a dataset of 397 conditions (the Master IDS). This deterministic engine does the clinical math; an LLM layer generates plain-language narratives on top of the results. The output is not a diagnosis; it is a triage signal designed to inform, not replace, veterinary care.

The system is composed of five components: an **Interface** where users submit symptoms and view results, an **Intake Pipeline** that packages and forwards the submission, the **Diagnostic Engine** that runs the scoring and generates the response, a **Resource Library** that stores all reference data the engine queries, and a **Data Archive** where every session is logged and the platform owner can manage the system.

Dualboot Partners will design and build the complete technical system: the Diagnostic Engine, the supporting Intake Pipeline, the Interface and results dashboard, and the Resource Library that underpins them. The marketing website rebuild and brand design are scoped on a different project. We assume the broader wvere.com platform will be operational when this system is integrated.

---

## System Definition and Core Capabilities

### What the System Does

A dog owner logs into the platform and opens the Vetalogix diagnostic tool. They fill out their dog's profile (breed, age, weight, sex, medications, vaccination history, and lifestyle data) and then select symptoms from a structured dropdown menu covering 355 clinical symptoms across categorized groups. They also answer five binary stability questions (appetite, energy, gum color, stool quality, temperature) that the engine uses to calibrate urgency.

Once submitted, the system runs the full scoring algorithm against all 397 conditions in the Master IDS, applies breed-specific risk modifiers, medication factors, age adjustments, and stability penalties, then sorts and surfaces the top 10 results. Each result is assigned an urgency tier (Critical, Urgent, or Monitor) and an LLM-generated narrative explaining why the condition matched this dog's profile.

The user sees their results in a personal dashboard (the Ethos Dashboard), with each condition color-coded by urgency and accompanied by a plain-language explanation.

---

### The Five System Components

**Interface** *(Ethos UI / Ethos Dashboard)*
The structured input form and results dashboard. Captures the full dog profile (biometrics, lifestyle, medications, vaccinations), symptom selections (up to 3 primary and 10 secondary, each with category, symptom, and duration), and the five Sentry binary fields. Packages this into a clean JSON payload and strips all owner PII before passing it downstream. Displays results back to the user in the Ethos Dashboard.

**Intake Pipeline** *(The Clinical Registrar / The Passive Gatehouse)*
A passive, non-interpretive service. No AI, no scoring. Receives the structured input from the Interface, packages it into a single Sovereign Case Object, verifies PII has been stripped, and forwards it to the Diagnostic Engine. Its only job is clean handoff.

**Diagnostic Engine** *(The Sovereign Logos Agent / The Intelligence Hub)*
The diagnostic brain. Receives the Sovereign Case Object and runs the full Valtorta Logic Engine: the TDS formula across all 397 conditions, querying the Resource Library for breed and condition reference data. Applies all scoring components: Anatomical Gate, Symptom Velocity, Age Factor, Breed Surge, Medications Factor, History Surge, Acute Emergency Surge, Sentry Stability Brakes, and Prevalence Headwinds. Sorts results, extracts the Top 10, assigns urgency tiers, generates Diagnostic Handshake narratives via LLM, and flags any Logic Conflicts. Returns the final Valtorta Response to the user's dashboard.

**Resource Library** *(The Sovereign Intel Vault)*
A static, locked reference data repository. Stores the Master IDS (397 conditions with symptom weights, breed risk flags, lethality levels, and prevalence headwinds), the Breed Bio table, Merck Manual reference data, and Condition Tags. The Diagnostic Engine queries this library during scoring. Data can be updated without touching application code.

**Data Archive and Admin Console** *(The Sovereign Strategic Archive)*
Long-term data archive and owner admin layer. A lightweight write-only session logger is included in Phase 1, capturing every complete Synthesis Log from the first diagnostic run. Phase 2 builds the full archive stack on top of that accumulated data: analytical query infrastructure, admin-only exports of anonymized case data (Sovereign Audit), an admin-only logic update tool to push updates to the Resource Library without a full redeployment (Sovereign Injection), and automated encrypted backups. Strictly limited to the platform owner's admin account.

---

## The Valtorta Logic Engine

The Valtorta Logic Engine is the intellectual core of Vetalogix, not a feature of the system, but the product itself. It is the client's proprietary scoring methodology, developed independently of this engagement. Dual Boot Partners will implement it faithfully and build the infrastructure required to run it at scale. The clinical calibration of the engine (its conditions, weights, and classifications) is the client's domain. Validation by a licensed veterinary professional is required before the system goes live.

The engine receives a complete case object: the dog's full profile (breed, age, sex, weight, medications, clinical history), the selected symptoms with their categories and durations, and the five binary Sentry stability markers that indicate the dog's current physiological state.

For each case submitted, the engine runs its scoring logic against all 397 conditions in the Master IDS, accounting for symptom-to-condition matching, breed-specific risk, patient age, medication context, clinical history, symptom acuity, stability indicators, and condition prevalence. It then returns a ranked diagnostic output: the top 10 matched conditions, each carrying:

- A **Sovereign Grip Score**: the numerical result of the scoring run for that condition
- An **urgency tier**: one of three tiers that determines how the result is presented and what action it recommends


| Zone                   | Score Range | Action                   | Color        |
| ---------------------- | ----------- | ------------------------ | ------------ |
| **Critical Strike**    | 3.0+        | EMERGENCY: GO NOW        | Neon Red     |
| **Urgent Consult**     | 1.5 – 2.9   | CLINICAL REVIEW REQUIRED | Amber Yellow |
| **Monitor / Guidance** | Below 1.5   | OBSERVE & DOCUMENT       | Cobalt Blue  |


- A **Diagnostic Handshake**: a plain-language narrative, generated by an LLM layer on top of the scored result, explaining in accessible terms why this condition matched this dog's specific profile

The output of this engine is not a diagnosis. It is a scored triage signal: a structured, ranked picture of what conditions are most consistent with the submitted symptoms, designed to help an owner understand how quickly they need to act, not to replace the judgment of a veterinarian.

---

## Why a Phased Approach?

Building a system of this complexity (a proprietary AI diagnostic engine sitting on top of a 397-condition clinical dataset) requires a structured rollout for three reasons.

**First, the Master IDS is the product.** Every other component is infrastructure. The value of Vetalogix lives in the quality and structure of the dataset and the calibration of the scoring formula. Phase 0 exists to get into the details of that dataset: understanding its structure, validating its quality, and using that knowledge to define exactly what we are building: the precise schema the Diagnostic Engine will run against, the shape of the Resource Library, and the component boundaries between them. The estimates in this proposal are directionally sound; Phase 0 is what sharpens them into a confirmed scope.

**Second, the core diagnostic loop must work before anything else matters.** Phase 1 delivers the minimum system that a dog owner can actually use: submit symptoms, receive a scored and explained result. The Ethos Dashboard shows the user their current results and does not depend on the archive layer at all.

**Third, data collection starts on day one, not in Phase 2.** From the first session in Phase 1, every complete Synthesis Log (raw input, all 397 condition scores, and the final Valtorta Response) is written to a lightweight, durable session store. No diagnostic run is ever discarded. Phase 2 does not introduce data collection; it builds the robust query layer, admin tooling, and operational hardening on top of a dataset that has already been accumulating since launch. This means the platform owner enters Phase 2 with real case data to work with, not an empty archive.

---

## Roadmap and Implementation Phases

### Phase 0: Discovery and Design

The primary objective of this phase is to close all open questions that currently block scoping. Most importantly: receive the Master IDS in a usable format, assess its structure and data quality, and define the exact schema the Diagnostic Engine will work against. In parallel, we finalize all UI/UX design, define the data flow and API contracts between components, finalize infrastructure decisions on GCP (Vertex AI Agent, Pub/Sub or equivalent messaging, Cloud Run or GKE for the Diagnostic Engine, Cloud Firestore/BigQuery for the Data Archive), and produce the full technical specification for Phase 1.

Deliverables from this phase include the IDS schema and data quality assessment, finalized UI wireframes for the input form and Ethos Dashboard, the complete system architecture document, the API and data contract specifications for all components, and a refined Phase 1 scope and estimate.

**Estimated Effort: 120 – 160 hours** | **Est. Cost: $10,200 – $13,600**

---

### Phase 1: MVP - Core Diagnostic Engine

This phase builds the complete, end-to-end diagnostic loop. A user can log in, fill out their dog's profile and symptoms, receive a scored and urgency-tiered result with plain-language explanations, and view their results in the Ethos Dashboard.

**What we build:**

- **Interface** *(Ethos UI / Ethos Dashboard):* The structured input form covering 355 symptoms across categorized dropdowns. The full K9 Ethos Profile capture. Primary symptom selector and secondary selector , each with category, symptom, and duration. Five Sentry binary fields and contextual fields. The Ethos Dashboard displaying results of the Diagnostic Engine.
- **Intake Pipeline** *(The Clinical Registrar):* The passive data packaging service. Receives structured input from the Interface, validates the payload structure, and forwards the Sovereign Case Object to the Diagnostic Engine via a secure messaging channel (Pub/Sub or equivalent).
- **Diagnostic Engine** *(The Sovereign Logos Agent):* The full TDS scoring implementation. Top 10 result extraction, urgency tier assignment, Logic Conflict flagging. LLM integration (Vertex AI) for Diagnostic Handshake narrative generation. Merck Manual validation check against cached reference data.
- **Resource Library** *(The Sovereign Intel Vault):* Structured storage for the Master IDS, Breed Bio table, Merck Manual cached data, and Condition Tags. Queryable by the Diagnostic Engine during scoring. Built to allow data updates without code changes.
- **Session Logger** *(Lightweight Data Archive, write-only):* From the first session, every complete Synthesis Log (raw input, full scoring output across all 397 conditions, and the final Valtorta Response) is written to a durable session store. No admin tooling, no query interface, no exports. Write-only. Every diagnostic run is captured and preserved so that no data is lost before Phase 2 builds the full archive layer on top of it.
- **Security and infrastructure baseline:** VPC isolation between components, IAM roles and service account configuration, Cloud Audit Logs, and the biometric login gate (D-Scope) protecting the Ethos Dashboard.

**Estimated Effort: 2,400 – 3,200 hours** | **Est. Cost: $204,000 – $272,000**

---

### Phase 2: Full Platform - Archive, Admin, and Hardening

With the core engine validated in production and session data already accumulating since Phase 1 launch, Phase 2 completes the platform: the full Data Archive stack, the admin console, and the operational hardening needed for long-term sustainable operation.

**What we build:**

- **Data Archive** *(The Sovereign Strategic Archive, full stack):* The lightweight Phase 1 session store is migrated into the complete archive infrastructure: BigQuery for analytical queries across the full case history, Vertex AI Vector Search for trend analysis across cases. The data is already there; Phase 2 builds the layer that makes it queryable, exportable, and operationally governed.
- **Admin Console:** Sovereign Audit export: the platform owner selects a date range or case type and exports a clean, anonymized CSV. Sovereign Injection: the platform owner uploads a new logic manifest (updated IDS rows, revised condition entries, new symptom weights) and the system propagates the changes to the Resource Library near-immediately, without engineering intervention or redeployment.
- **Resurrection Protocol (Pillar 26):** Automated encrypted backup that mirrors Data Archive contents to an off-platform location. Backup destination and frequency to be confirmed with the client.
- **Access hardening:** Break-glass access model for production Resource Library and Data Archive, with full Cloud Audit Log coverage on every access event.
- **Architecture foundations for future multimodal input:** The Phase 1 system is designed so that a future preprocessing step can extract clinical symptom data from uploaded images or video and inject it into the standard scoring pipeline. No multimodal functionality is built in Phase 2; only the architectural seams that make it addable without a redesign.

**Estimated Effort: 1,200 – 1,600 hours** | **Est. Cost: $102,000 – $136,000**

---

## Agile Adaptation and Prioritization

This roadmap defines a clear path, but it is not rigid. The modular architecture means individual components can be accelerated or deferred based on what we learn during Phase 0 and what the client needs to prioritize. If specific admin features become critical earlier than planned, or if the IDS data requires more preparation work than anticipated, we adjust the queue accordingly. We maintain an open feedback loop throughout the engagement; scope decisions are made collaboratively, not handed down.

---

## Key Assumptions

This proposal is built on the following assumptions. If any of these change, the scope, timeline, and cost estimates will need to be revisited.

1. **The Master IDS dataset is provided by the client in a usable format before Phase 1 begins.** The 397-condition dataset (including symptom weights, breed risk flags, lethality classifications, and prevalence headwinds) is the core of the product. Phase 0 is dedicated to receiving this dataset, understanding its structure, and defining the exact schema the Diagnostic Engine will work against. We will analyze the data to inform implementation decisions; clinical validation of its accuracy is the client's responsibility.
2. **The Medications Factor interaction rules will be defined by the client.** The exact logic for how specific medications boost or suppress condition scores is the client's domain. We will implement the rules as specified; we do not define them.
3. **The Vetalogix Interface will be integrated into the wvere.com platform.** We scope and build the diagnostic Interface (the input form and Ethos Dashboard) as a self-contained module. The broader wvere.com platform is assumed to be operational at the time of integration; its rebuild is outside the scope of this project.
4. **API access to the Merck Manual is required for this integration.** We will build the integration layer to cache Merck reference data into the Resource Library. API access must be in place before that integration can be activated.
5. **Regulatory framing of outputs is the client's responsibility.** The urgency-tier language used in results may be subject to regulatory review depending on how the product is positioned and marketed. The client is responsible for obtaining appropriate legal guidance before launch. We will implement whatever output framing and disclaimer structure is confirmed as required.
6. **Model fine-tuning is not in scope for this project.** The Sovereign Injection feature (Phase 2) updates data assets in the Resource Library (IDS rows, condition entries, and symptom weights) without touching the underlying model. Fine-tuning is a separate process and is not part of this engagement.

---

## Security, Privacy, and Compliance

The security model is built around the sensitivity of health data and the proprietary nature of the IDS dataset.

**Data isolation:** Each system component runs in its own VPC perimeter with IAM-enforced service account permissions. The Diagnostic Engine can query the Resource Library; nothing else can. The Resource Library and Data Archive are inaccessible to the general engineering team post-deployment.

**PII handling:** Owner PII is stripped at the point of submission before the payload leaves the Intake Pipeline. Only clinical and dog data moves through the diagnostic pipeline. Anonymized case data is all that is stored in the Data Archive.

**Access control:** The admin console (Data Archive) is limited to a single owner account. All access attempts to the inner perimeters are logged via Cloud Audit Logs with full telemetry.

**Break-glass access:** We recommend, and will build, a time-limited, audited break-glass access model for engineering to respond to production incidents in the Resource Library and Data Archive. A fully permanent air-gap creates operational risk with no upside.

**Regulatory note:** The urgency-tier language used in results ("EMERGENCY: GO NOW") may attract regulatory scrutiny depending on how the product is marketed. The client is responsible for obtaining legal input on output framing and disclaimers before launch. We will implement whatever copy and disclaimer structure legal confirms is required.

---

## Suggested Team Composition

To execute this roadmap, we propose the following team structure for Phase 1 and Phase 2:

- **Product Director**
- **Product Manager** - part-time
- **Tech Lead (Full-Stack / AI)** - full-time
- **Full-Stack Engineer** - full-time
- **QA Engineer** - part-time
- **DevOps / Cloud Engineer** - part-time

**Clinical data validation is not within Dual Boot's scope.** We strongly recommend the client engage a licensed veterinary professional to validate the Master IDS (its symptom weights, condition classifications, lethality tiers, and breed risk flags) before Phase 1 begins. The accuracy of the product is entirely dependent on the quality of that dataset.

---

## Conclusion

This proposal outlines a strategic path toward building a robust veterinary diagnostic platform. By prioritizing the core diagnostic loop first (delivering a working, scored, plain-language triage tool for dog owners at the end of Phase 1) and methodically layering in the full archive stack, admin tooling, and operational hardening in Phase 2, we ensure the platform is both immediately useful and long-term sustainable. This phased approach mitigates technical risk, respects the regulatory clarity that must be established before launch, and provides the flexibility needed to adapt as the Master IDS and clinical validation process evolve.

DAVID DEKARSKE
Attachments
Mon, Apr 27, 12:51 PM (23 hours ago)
to me, Matias, Allen

Hey Rafael,

We changed one item in the logic workflow by converting the Level 1 standard Gemini into a "data gatherer," which is why you didn't see it in the test cases.  That will reduce complexity, as it's now only a "Registrar" that packages specific datapoints to send to level 2. We also added an additional factor into the formula (Medications - Screenshot 2026-04-27 113845.png)

 To break down the update:

Level 1: The Passive Gatehouse (The Clinical Registrar)
Role: A strict, non-interpretive collection layer.

The Flow: This layer simply captures the User’s Dropdown Selections (mapping 1:1 to our IDS columns) and the Ethos Markers (Breed, Age, Sex, Medications, etc.).

The Handoff: It packages this raw data (the "Dirt") into a single JSON object and pushes it directly to Level 2. No clinical math or AI interpretation happens at this stage, simplifying the front-end requirements.


Level 2: The Sovereign Logos Agent (The Intelligence Hub)

Role: This is where the Vetalogix "Brain" resides and where the Clinical Synthesis occurs.

The Action: Upon receiving the package from Level 1, the AI Agent performs all clinical operations "in one room":

Statistical Math: It runs the raw IDS weighting against the symptoms.

The Valtorta Multipliers: It applies the Genetic Surge (B_m) based on the Resource Library in Level 3 and calculates Sentry Brakes ($S_s$) by cross-referencing bio-data against symptoms.

The Branded Response: The AI generates the "Diagnostic Handshake" narrative—explaining its reasoning and flagging "Logic Conflicts" (e.g., an age-impossible match).


Level 3: The Sovereign Intel Vault (The Resource Library)

Role: A modular repository for our hardened CSV files (IDS, Breed Bio, Merck Manual, Condition Tags).

The Benefit: Level 2 "opens the books" in Level 3 as needed. This allows us to update clinical weights or add new conditions without ever needing to touch the core software code.


Level 4: The Sovereign Strategic Archive (RAW Data)

Role: The Long-Term Clinical Memory. This is an air-gapped, high-security repository for offline data analysis and intelligence scaling. It is strictly separated from the production environment to ensure heavy data-mining never throttles the live application.

Data Ingestion: The Synthesis Log. Level 4 captures and stores the complete output from every Level 2 session, including the raw "Dirt," the finalized Valtorta Response, and incoming telemetry data (Partner lab results from APL, SignalPET, etc.).

Security & Sovereignty: By housing the "Full History" here, we maintain a clean Sovereign Audit Trail, ensuring the underlying logic remains proprietary even as the front-end undergoes updates.


Attached: TDS_Formula.docx

This document has been updated to reflect this new flow. We can definitely jump on a call this week to discuss, my schedule is pretty open.
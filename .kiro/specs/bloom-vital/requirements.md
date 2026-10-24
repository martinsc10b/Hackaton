# Requirements Document

## Introduction

Bloom Vital is a patient-prepared, source-linked **English-language brief** assembled from **fictional Spanish-language medical documents only**. The brief is intended for a receiving care team. In this demo the system **displays prewritten (seeded) facts and prewritten English translations** that are already mapped to their source documents; it does **not** implement live extraction or live machine translation. Every displayed fact is linked to its original source document with exact passage/page evidence (showing the exact original Spanish text and source location), and original Spanish text is kept alongside its English rendering.

The demo scope is exactly **one fictional patient, Nicolás, with three bundled fictional Spanish documents** and prewritten (seeded) English facts. There are **no general uploads, no OCR, and no real data** — the three bundled documents for Nicolás are the only inputs the system handles. The seeded facts are clearly labeled as prewritten demo content; the system does **not** claim to perform live extraction.

The patient's **review experience is in Spanish** (Nicolás reviews and corrects in his own language), while the **brief for the clinician is in English**. The original Spanish evidence stays accessible separately from the English brief, and the exported brief carries a **short Spanish explanation of what the patient is sharing**.

The output is a **phone-readable English patient-prepared brief plus a separate source appendix** — Nicolás opens Bloom Vital and shows it to his doctor on his phone, or exports/prints it. Nothing is ever sent to a hospital automatically.

The system is **synthetic / demo-only**. It never processes real patient data. It performs **no clinical reasoning**: it does not diagnose, assess, infer risk, plan treatment, or recommend care. Its job is faithful presentation of seeded facts, transparent translation, provenance preservation, human review, and export of a readable brief. A human (the patient) MUST review and correct extracted items and resolve or acknowledge every unresolved ambiguity **before** export — a hard review gate. Acknowledgment is **not** medical certification and **not** translation certification; acknowledged-but-unresolved items stay visibly flagged even after acknowledgment. The system never automatically sends the brief to anyone.

Out of scope for this product: OCR, real uploads, voice or chatbot features, medical recommendations, and hospital/EHR integrations.

The CMS ACCESS template is used as a **reference layout only**. Bloom Vital claims **no CMS affiliation, no CMS endorsement, and no FHIR compliance**.

## Glossary

- **Bloom_Vital**: The overall system that displays seeded facts and prewritten translations, links them to sources, supports review, and exports the patient-prepared brief.
- **Fact_Catalog**: The seeded set of prewritten structured facts (medications, labs, conditions, procedures, allergies, patient basics) for Nicolás, each already mapped to its source document. The demo displays these; it does not extract them live.
- **Prewritten_Translation**: The seeded English rendering paired with each Spanish Source_Fact. The demo displays these; it does not translate live.
- **Evidence_Linker**: The stored mapping that ties every displayed fact to a durable reference (source document + passage/page + exact Spanish text).
- **Review_Gate**: The mandatory human review-and-correction step that must complete before export is permitted.
- **Exporter**: The component that produces the readable patient-prepared brief.
- **Source_Fact**: A fact extracted directly from a source document, carrying its evidence reference.
- **Patient_Correction**: A change, annotation, or acknowledgment entered by the patient during review; stored separately from the Source_Fact it relates to.
- **Evidence_Reference**: A durable pointer to source document + page/section + exact original text that supports a fact.
- **Unresolved_Item**: An extracted fact that is flagged (not-documented, ambiguity, conflict, or unknown status) and not yet resolved or acknowledged by the patient.
- **Not_Documented**: The state of a medication, allergy, or other field that is blank or absent in the source; means "not documented," NOT "none" or a negative finding.
- **Conflict**: Two or more claims about the **same fact in matching context** that genuinely contradict each other. Values that differ only because they describe different points in time or different measurements (e.g., dated lab history) are NOT conflicts. Coming from different source documents does NOT, by itself, make claims non-conflicting: a genuine contradiction is still a Conflict even when its claims live in different documents.
- **Acknowledgment**: A patient action at the Review_Gate that permits export of an item that remains unresolved; it is NOT clinical verification, and the item stays visibly flagged.
- **Taking_Status**: The current medication state of a documented medication, one of `taking`, `not taking`, or `unknown`.
- **Brief**: The exported English patient-prepared document.
- **Ambiguous_Date**: A date whose field order (month/day) or value cannot be determined with certainty from the source.

## Requirements

### Requirement 1: Seeded Fact Fidelity

**User Story:** As a patient preparing a brief, I want the seeded facts displayed exactly as documented in the fictional records, so that the receiving care team sees what the source actually says and nothing invented.

#### Acceptance Criteria

1. THE Bloom_Vital SHALL display, from the Fact_Catalog, the patient basics, medications, labs, conditions, procedures, and allergies as Source_Facts mapped to Nicolás's three bundled documents.
2. THE Bloom_Vital SHALL display the original documented value of each Source_Fact without alteration, normalization, or inference of unstated values.
3. WHERE a medication's dose and frequency are documented, THE Bloom_Vital SHALL display them as documented.
4. IF a medication's dose or frequency is blank or not documented in the source, THEN THE Bloom_Vital SHALL display the field as `NOT DOCUMENTED` and flag it as an Unresolved_Item, NOT as "none".
5. WHEN a medication or allergy field is blank or absent in a source, THE Bloom_Vital SHALL represent it as `NOT DOCUMENTED` rather than as "none", absent, or a negative finding.
6. WHEN a fact is absent from all source documents, THE Bloom_Vital SHALL represent the related condition, allergy, or medication as `UNKNOWN`/`NOT DOCUMENTED` rather than as absent or negated.
7. THE Bloom_Vital SHALL display every lab with its documented date and its original units.

### Requirement 2: Prewritten Translation (Spanish to English, Original Preserved)

**User Story:** As a patient, I want each Spanish Source_Fact shown with its prewritten English rendering and the Spanish kept visible, so that the care team can read the brief while the original wording remains verifiable.

#### Acceptance Criteria

1. WHEN a Source_Fact contains Spanish text, THE Bloom_Vital SHALL display its Prewritten_Translation in English. (The demo displays seeded translations; it does not translate live.)
2. THE Bloom_Vital SHALL preserve and display the exact original Spanish text alongside its English rendering.
3. WHERE a Prewritten_Translation is marked uncertain or a Spanish term has no confident English equivalent, THE Bloom_Vital SHALL flag the rendering as an Unresolved_Item and retain the original Spanish verbatim.
4. THE Bloom_Vital SHALL NOT alter numeric values, units, or dates between the Spanish source and its English rendering.

### Requirement 3: Evidence Navigation

**User Story:** As a reviewer, I want every fact linked to its original source with exact location and text, so that I can verify any claim in the brief against the document it came from.

#### Acceptance Criteria

1. THE Evidence_Linker SHALL attach to every Source_Fact — including each patient-basics field — an Evidence_Reference identifying the source document, the passage/page or section, and the exact original Spanish text, where available.
2. WHEN a user selects a Source_Fact, THE Bloom_Vital SHALL display the associated Evidence_Reference including the exact original Spanish text.
3. THE Bloom_Vital SHALL retain each Evidence_Reference for the life of the fact, including after Patient_Corrections are applied.
4. IF a Source_Fact (including a patient-basics field) has no attachable Evidence_Reference, THEN THE Bloom_Vital SHALL flag the fact as an Unresolved_Item and SHALL NOT present it as source-supported in the exported brief.

### Requirement 4: Patient Corrections and Review Gate

**User Story:** As the patient, I want to review and correct extracted items and resolve ambiguities before anything is exported, so that the brief reflects my input and no unreviewed content leaves the system.

#### Acceptance Criteria

1. THE Bloom_Vital SHALL store each Patient_Correction separately from its related Source_Fact.
2. WHEN a Patient_Correction is applied, THE Bloom_Vital SHALL preserve the original Source_Fact, its Evidence_Reference, and the full correction history.
3. THE Bloom_Vital SHALL NOT overwrite or delete the original evidence or prior correction history when a Patient_Correction is applied.
4. WHILE any fact has not been reviewed, OR any Unresolved_Item has not been resolved or acknowledged by the patient, THE Review_Gate SHALL block export.
5. WHEN the patient has reviewed every extracted fact AND resolved or acknowledged every Unresolved_Item, THE Review_Gate SHALL permit export.
6. WHEN a patient acknowledges an Unresolved_Item, THE Bloom_Vital SHALL treat the acknowledgment as permission-to-export only — NOT as medical certification and NOT as translation certification — AND SHALL keep the item visibly flagged after acknowledgment.
7. THE Bloom_Vital SHALL visibly distinguish Source_Facts from Patient_Corrections wherever facts are displayed or exported.
8. THE Bloom_Vital SHALL let the patient add separately labeled correction notes WITHOUT changing, erasing, or hiding the original clinical statement they annotate; the original Source_Fact and its Evidence_Reference remain intact and visible.
9. WHERE a Patient_Correction annotates a Source_Fact that is part of a Conflict, THE Bloom_Vital SHALL keep every conflicting Source_Fact visible and attributed; the correction note SHALL NOT remove or resolve away the conflict.

### Requirement 5: Export

**User Story:** As the patient, I want to export a phone-readable English brief with durable evidence references and remaining unresolved items shown, that the system never sends on its own, so that I stay in control of sharing.

#### Acceptance Criteria

1. WHEN the Review_Gate has permitted export, THE Exporter SHALL produce a phone-readable English Brief.
2. THE Exporter SHALL include durable Evidence_References for each source-supported fact in the Brief.
3. THE Exporter SHALL include any acknowledged-but-unresolved Unresolved_Items visibly in the Brief.
4. THE Exporter SHALL visibly separate Source_Facts from Patient_Corrections in the Brief.
5. THE Exporter SHALL include a separate source appendix that preserves the three original fictional Spanish documents in full and the stable source references used by the brief's citations.
6. THE Exporter SHALL include a short Spanish explanation of what the patient is sharing.
7. THE Bloom_Vital SHALL require an explicit patient-initiated action for every export through the app.
8. THE Bloom_Vital SHALL NOT transmit, send, or share the Brief with any party automatically.
9. THE Review_Gate SHALL govern only the app's own export action; THE Bloom_Vital SHALL disclose that it cannot fully prevent the operating system or browser from printing, screenshotting, or otherwise capturing what is on screen.

### Requirement 6: Synthetic / Demo-Only Data Handling

**User Story:** As a stakeholder, I want the system to operate strictly on fictional data and declare itself demo-only, so that no real medical data is ever involved.

#### Acceptance Criteria

1. THE Bloom_Vital SHALL use only Nicolás's three bundled fictional Spanish documents and their prewritten (seeded) English facts.
2. THE Bloom_Vital SHALL NOT accept general uploads, perform OCR, or process real data.
3. THE Bloom_Vital SHALL display a demo-only / synthetic-data notice, and SHALL label the facts and translations as prewritten (seeded) demo content rather than live extraction or live translation, in the application and in the exported Brief.
4. THE Bloom_Vital SHALL NOT claim CMS affiliation, CMS endorsement, or FHIR compliance in the application or the Brief.
5. WHERE the CMS ACCESS template is referenced, THE Bloom_Vital SHALL treat the template as a reference layout only and SHALL NOT present it as a mandated clinical workflow.

### Requirement 7: Ambiguity and Error Handling

**User Story:** As a reviewer, I want ambiguous and conflicting information preserved, attributed, and flagged rather than silently resolved, so that I can decide how to handle it with full context.

#### Acceptance Criteria

1. WHEN a date field order cannot be determined with certainty, THE Bloom_Vital SHALL preserve the original date text as an Ambiguous_Date and flag it as an Unresolved_Item.
2. THE Bloom_Vital SHALL NOT silently choose a month/day order for an Ambiguous_Date.
3. THE Bloom_Vital SHALL preserve a written-out date exactly as documented and SHALL NOT reinterpret it as a different calendar date.
4. WHEN values differ only because they describe different points in time or different measurements (e.g., dated lab history), THE Bloom_Vital SHALL preserve each value with its date, source, and original units and SHALL NOT treat the difference as a Conflict on that basis alone.
5. WHEN two or more claims about the same fact in matching context genuinely contradict each other, THE Bloom_Vital SHALL keep each value attributed to its own source and flag the Conflict as an Unresolved_Item — EVEN WHEN the claims come from different source documents. Coming from different sources SHALL NOT, by itself, prevent a genuine contradiction from being flagged.
6. THE Bloom_Vital SHALL NOT merge, average, or silently resolve Conflicts or distinct Source_Facts.
7. IF a seeded fact is incomplete, its Prewritten_Translation is marked uncertain, or its evidence link is missing, THEN THE Bloom_Vital SHALL flag the fact as an Unresolved_Item and retain any original Spanish text available.

### Requirement 8: Medication Current / Taking Status

**User Story:** As a reviewer, I want a medication's current status to be `unknown` unless explicitly documented, so that an old mention is never presented as a current medication.

#### Acceptance Criteria

1. WHEN a medication in the Fact_Catalog has no documented current-use information, THE Bloom_Vital SHALL display its Taking_Status as `unknown` and flag it as an Unresolved_Item.
2. THE Bloom_Vital SHALL NOT infer a Taking_Status of `taking` from the age, recency, or mere presence of a medication mention.
3. WHEN the patient sets a Taking_Status during review, THE Bloom_Vital SHALL record it as a Patient_Correction while preserving the original documented mention and its Evidence_Reference.

### Requirement 9: Laboratory Units and Comparison

**User Story:** As a reviewer, I want original lab values and units always visible, with no conversions or clinical comparisons performed, so that results are preserved exactly as documented.

#### Acceptance Criteria

1. THE Bloom_Vital SHALL display each lab result with its original value and original units at all times.
2. THE Bloom_Vital SHALL treat different units as different units only; different units do NOT by themselves mean incompatible or conflicting results.
3. THE Bloom_Vital SHALL NOT perform any unit conversion in this mockup.
4. THE Bloom_Vital SHALL NOT perform any clinical lab comparison in this mockup.
5. WHEN lab results are temporally distinct, THE Bloom_Vital SHALL preserve each result with its own date, source, and original value/units and SHALL NOT treat them as a Conflict.

### Requirement 10: Hard Boundaries (Testable Negative Requirements)

**User Story:** As a stakeholder, I want the system's clinical and data boundaries enforced as explicit, testable rules, so that the system never crosses into clinical reasoning or unsafe data handling.

#### Non-Goals (Out of Scope)

- Generating any clinical assessment, diagnosis, risk inference, treatment plan, or medical recommendation.
- Claiming CMS affiliation, CMS endorsement, or FHIR compliance; treating the CMS ACCESS template as a mandated workflow.
- Silently resolving ambiguous dates, genuine conflicts, or medication status.
- Converting lab units or performing clinical lab comparisons.
- Treating dated lab history (values differing by date or units) as conflicts; treating blank fields as "none". (Note: a genuine contradiction IS flagged even across different source documents.)
- General uploads, OCR, or handling real (non-fictional) patient data.
- Automatically sending or sharing the Brief.

#### Acceptance Criteria

1. THE Bloom_Vital SHALL NOT generate clinical assessments, diagnoses, risk inferences, treatment plans, or medical recommendations.
2. THE Bloom_Vital SHALL NOT present a medication as currently taken unless a Patient_Correction or documented source explicitly states it.
3. THE Bloom_Vital SHALL NOT merge conflicting Source_Facts into a single resolved value, and a Patient_Correction SHALL NOT erase, overwrite, or hide the conflicting Source_Facts; both conflicting Source_Facts remain visible and attributed alongside any correction note.
4. THE Bloom_Vital SHALL NOT remove or overwrite original Evidence_References or correction history.
5. THE Bloom_Vital SHALL NOT export while any Unresolved_Item remains unresolved and unacknowledged.
6. THE Bloom_Vital SHALL NOT auto-send the Brief to any recipient.

## Acceptance Examples

These concrete examples illustrate expected behavior and serve as test scenarios.

1. **Ambiguous numeric date "03/12/2025"** — The system preserves the original text "03/12/2025", classifies it as an Ambiguous_Date, flags it as an Unresolved_Item, and does not select March vs. December on its own. The patient resolves or acknowledges the ambiguity during review before export (Req 7.1, 7.2).

2. **Written-out "December 3"** — The system stores and renders the date exactly as "December 3" (or "diciembre 3" with its English rendering) and never transforms it into "March 12" or any other calendar date (Req 7.3, Req 2.4).

3. **Old medication mention with unknown status** — A medication mentioned in an older of Nicolás's documents is shown with Taking_Status `unknown`, flagged as an Unresolved_Item, and is never presented as currently taken based on the mention alone. The patient may set status during review as an attributed Patient_Correction (Req 8.1, 8.2, 8.3, Req 10.2).

4. **Blank allergy field (not documented, not "none")** — One of Nicolás's documents has a blank allergy field; it is shown as `NOT DOCUMENTED` and flagged as an Unresolved_Item, never rendered as "no known allergies" or "none" (Req 1.5, Req 6).

5. **Genuinely conflicting allergy claims across two of Nicolás's documents** — Two of Nicolás's documents, in matching context, assert contradictory allergy claims (e.g., "alergia a la penicilina" vs. "sin alergia a la penicilina"). Both are kept, each attributed to its own document, and the Conflict is flagged as an Unresolved_Item even though the claims come from different documents. A patient correction note may be added, but it does not erase either conflicting Source_Fact (Req 7.5, 7.6, Req 4.8, 4.9, Req 10.3).

6. **Dated lab history with differing original units** — Two of Nicolás's lab results, recorded on different dates and in different original units, are each preserved with their own date, source, and original value/units as a history. They are NOT converted, NOT compared, and NOT flagged as a conflict merely for differing in date or units (Req 9.1–9.5, Req 7.4).

## Open Questions

The following inputs and outputs are unsettled and MUST be resolved later. No implementation detail is assumed here.

1. **Export file format beyond print** — The output is a phone-readable English brief plus a source appendix. Whether a non-print export (e.g., saved PDF/HTML file) is offered in addition to the app's export action and ordinary browser printing is undecided.
2. **Evidence-reference durability mechanism** — How durable Evidence_References are represented so they stay stable in the brief + appendix (e.g., embedded excerpts, numbered citations, appendix anchors). The mockup uses numbered citations into the appendix; the production mechanism is undecided.
3. **Patient-basics field set** — Exact set of "patient basics" fields to capture and link. Undecided.
4. **Content of Nicolás's three bundled documents** — The exact original Spanish text and prewritten English facts for each of Nicolás's three fictional documents are placeholders in the mockup and need final authored content.

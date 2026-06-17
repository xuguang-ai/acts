# Impaired_Cognition Extraction Guideline

## Objective

Determine whether the patient has evidence of impaired cognition documented in the clinical note.

Output:

* 1 = Evidence of impaired cognition present
* 0 = No evidence of impaired cognition

Use only information documented about the patient. Do not infer beyond the note.

---

## Output Schema

| Field | Values | Description |
|---|---|---|
| `Impaired_Cognition` | 0 / 1 | 1 = evidence of impaired cognition present; 0 = absent |
| `Supporting_Evidence` | text | Verbatim snippet supporting the label (the diagnosis, testing result, or clinician statement); empty when the label is 0 |

JSON form:

```json
{
  "Impaired_Cognition": 0,
  "Supporting_Evidence": "<verbatim snippet, or empty>"
}
```

Example — "Diagnosed with Alzheimer's disease.":

```json
{ "Impaired_Cognition": 1, "Supporting_Evidence": "Diagnosed with Alzheimer's disease." }
```

---

## Clinical Definition

Impaired cognition refers to a decline in memory, executive function, attention, language, visuospatial skills, reasoning, or other cognitive abilities that is greater than expected for normal aging. (Per NIA-AA core clinical criteria, impairment may occur in any one or more of these domains; memory is the domain most commonly involved in MCI that progresses to AD dementia.)

According to NIA-AA and related Alzheimer's disease diagnostic frameworks, cognitive impairment encompasses both:

1. Mild Cognitive Impairment (MCI)
2. Dementia and major neurocognitive disorders

Therefore, both MCI and dementia should be considered positive evidence of impaired cognition.

---

## Label = 1 (Impaired Cognition Present)

Assign 1 if ANY of the following are documented.

### A. Explicit Cognitive Impairment Diagnoses

Examples:

* Mild Cognitive Impairment (MCI)
* Amnestic MCI
* Cognitive impairment
* Mild neurocognitive disorder
* Major neurocognitive disorder
* Dementia
* Alzheimer's disease
* Alzheimer's dementia
* Vascular dementia
* Lewy body dementia
* Frontotemporal dementia
* Mixed dementia

Examples:

"Patient has mild cognitive impairment."

"Diagnosed with Alzheimer's disease."

"History of dementia."

---

### B. Explicit Cognitive Decline

Examples:

* Cognitive decline
* Progressive cognitive decline
* Declining memory
* Significant memory impairment
* Impaired cognition
* Neurocognitive decline

Note: Per NIA-AA core clinical criteria, a *concern* about cognitive change — whether reported by the patient, an informant/family, or a clinician — is necessary but NOT sufficient on its own. It must be paired with objective evidence of impairment (testing or bedside exam) or a clinician's determination that cognition is impaired. Informant- or patient-reported decline alone, without such corroboration, should be treated as subjective concern (see "Do NOT Count as Positive" below), not confirmed impairment.

Examples:

"Family reports progressive cognitive decline, and clinician documents impaired performance on examination." → 1

"Patient demonstrates worsening memory impairment on objective assessment." → 1

"Family reports memory is getting worse." (no objective or clinician corroboration) → 0 (subjective concern only)

---

### C. Objective Cognitive Testing Indicates Impairment

Examples:

* Abnormal MMSE
* Abnormal MoCA
* Neuropsychological evaluation documenting cognitive impairment
* Cognitive testing interpreted as impaired by clinician

Examples:

"MoCA consistent with mild cognitive impairment."

"Neuropsychological testing demonstrates cognitive deficits."

---

### D. Clinician Assessment Indicates Cognitive Dysfunction

Examples:

* Cognitive deficits noted
* Executive dysfunction
* Impaired memory affecting function
* Cognitive disorder

Examples:

"Assessment: cognitive impairment likely due to Alzheimer's disease."

"Executive dysfunction and memory deficits present."

---

## Label = 0 (No Impaired Cognition)

Assign 0 if the note explicitly indicates normal cognition or if there is no evidence of impairment.

### A. Cognition Normal

Examples:

* Cognitively intact
* Cognition normal
* No cognitive impairment
* Memory intact
* Normal cognitive function
* No evidence of dementia

Examples:

"Patient remains cognitively intact."

"No cognitive impairment identified."

---

### B. Normal Cognitive Testing

Examples:

* MMSE normal
* MoCA normal
* Neuropsychological testing normal

Examples:

"MoCA within normal limits."

"Neuropsychological evaluation did not identify cognitive deficits."

---

### C. No Mention of Cognitive Impairment

If the note contains no evidence supporting impairment, assign 0.

---

## Do NOT Count as Positive

The following should NOT be considered evidence of impaired cognition.

### Family History

Examples:

* Mother had Alzheimer's disease
* Family history of dementia

Reason:
This describes relatives, not the patient.

---

### Diagnostic Evaluation Only

Examples:

* Referred for memory evaluation
* Dementia workup planned
* Neuropsychological testing ordered

Reason:
Evaluation does not confirm impairment.

---

### Subjective Concern Without Clinical Evidence

Examples:

* Concern for memory problems
* Possible cognitive decline
* Rule out dementia

Reason:
Suspicion alone is insufficient.

---

### Delirium or Temporary Confusion

Examples:

* Acute encephalopathy
* Delirium
* Postoperative confusion
* Altered mental status from infection

Reason:
Transient cognitive changes should not be labeled as chronic impaired cognition unless the clinician explicitly diagnoses cognitive impairment, MCI, or dementia.

---

## Priority Rules

When conflicting information exists:

1. Explicit diagnosis overrides general statements.
2. Specialist assessment overrides screening language.
3. Current assessment overrides remote history.
4. Confirmed diagnosis overrides suspected diagnosis.

Examples:

"Possible dementia" → 0

"Diagnosed with dementia" → 1

"Memory evaluation planned" → 0

"Neuropsychological testing confirms MCI" → 1

---

## Annotation Decision Logic

1. Does the note contain a diagnosis of MCI, dementia, Alzheimer's disease, or another neurocognitive disorder?

   * Yes → 1

2. Does the note document cognitive decline, memory impairment, or cognitive deficits assessed by a clinician?

   * Yes → 1

3. Does cognitive testing demonstrate impairment?

   * Yes → 1

4. Is cognition documented as normal or is there no evidence of impairment?

   * Yes → 0

5. Are there only family history, evaluation plans, screening orders, or unconfirmed concerns?

   * Yes → 0

# Vaccine Extraction Guideline

## Objective

Identify vaccines documented in the patient's medical record.

The abstraction task is designed as a two-step process:

### Step 1: Vaccine Extraction

Extract all vaccines documented as administered, received, completed, or currently active in the patient's vaccination history.

### Step 2: Vaccine Classification

After extraction, map each vaccine to one or more categories using the CDC vaccine reference table (`../references/CDC_Vaccine_Reference_Table.md`).

Target categories:

* Active Amyloid or Tau Immunization
* Live Vaccine
* Non-Live Vaccine
* BCG

The chart reviewer should perform Step 1 only. Category assignment should be completed through standardized post-processing using the reference table (see `../references/CDC_Vaccine_Reference_Table.md`).

---

# Part 1: Vaccine Extraction

## Definition

A vaccine mention refers to any documented vaccine administered, received, completed, or recorded in the patient's vaccination history.

Extract:

* Vaccine name
* Supporting evidence
* Administration date if available

---

## Positive Evidence

Examples:

"Received influenza vaccine."

"COVID-19 booster administered."

"MMR vaccine completed."

"Vaccination history includes pneumococcal vaccine."

"Patient received Shingrix in 2023."

---

## Output Schema (Step 1)

Each extracted vaccine produces one record with the fields below. A note may yield zero, one, or multiple records. Category is **not** assigned in Step 1 — it is added in Step 2 (post-processing) using `../references/CDC_Vaccine_Reference_Table.md`.

| Field | Required | Description |
|---|---|---|
| `Vaccine_Name` | Yes | The vaccine name exactly as documented (verbatim) |
| `Supporting_Evidence` | Yes | The verbatim text snippet that supports the extraction |
| `Administration_Date` | No | The documented date/time; `Not documented` if absent |

JSON form (the list consumed by Step 2):

```json
[
  {
    "Vaccine_Name": "<verbatim>",
    "Supporting_Evidence": "<verbatim snippet>",
    "Administration_Date": "<date or 'Not documented'>"
  }
]
```

### Examples

Note: "Patient received Shingrix vaccine in 2023."

Output:

* Vaccine_Name: Shingrix
* Supporting_Evidence: "Patient received Shingrix vaccine in 2023."
* Administration_Date: 2023

---

Note: "MMR vaccine administered during childhood."

Output:

* Vaccine_Name: MMR
* Supporting_Evidence: "MMR vaccine administered during childhood."
* Administration_Date: Not documented

---

# Part 2: Exclusion Criteria

Do NOT extract vaccines that are:

### Planned

Examples:

"Influenza vaccine recommended."

"Will receive RSV vaccine next visit."

---

### Declined

Examples:

"Patient declined influenza vaccination."

---

### Contraindicated

Examples:

"Live vaccines contraindicated."

---

### Educational Discussion Only

Examples:

"Discussed shingles vaccine."

"Reviewed vaccine schedule."

---

# Part 3: Post-Processing Classification

After vaccine extraction, classify vaccines according to the CDC vaccine reference table (`../references/CDC_Vaccine_Reference_Table.md`). That table's "How the mapping agent should use this table" section defines the matching rules (match by brand → abbreviation → disease; brand wins; flag genuinely ambiguous disease-only mentions rather than guessing).

The abstraction reviewer should not manually determine vaccine categories unless explicitly instructed.

### Step 2 Output Schema

Each Step 1 record gains a category. Output one object per extracted vaccine:

```json
[
  {
    "Vaccine_Name": "<verbatim from Step 1>",
    "matched_table_row": "<brand / abbreviation / disease matched, or null>",
    "category": "<one of the categories below>",
    "match_basis": "brand | abbreviation | disease | name-only | none",
    "note": "<candidate categories if Ambiguous, else empty>"
  }
]
```

Categories: `Live Vaccine`, `Non-Live Vaccine`, `BCG`, `Active Amyloid or Tau Immunization`, `Ambiguous`, `Not a vaccine`.

---

## Live Vaccine

Examples may include:

* MMR
* Varicella
* Live attenuated influenza vaccine (LAIV)
* Yellow fever
* Rotavirus
* Oral typhoid
* BCG

---

## Non-Live Vaccine

Examples may include:

* COVID-19 vaccines
* Influenza injection
* Pneumococcal vaccines
* Tdap
* Hepatitis A
* Hepatitis B
* HPV
* Shingrix
* RSV vaccines

---

## BCG

Examples:

* BCG
* Bacillus Calmette–Guérin vaccine

---

## Active Amyloid or Tau Immunization

Examples:

* AADvac1
* ACI-24
* UB-311
* Amyloid vaccine
* Tau vaccine
* Active amyloid immunization
* Active tau immunization

These vaccines are typically encountered in Alzheimer's disease clinical trials or research documentation.

---

# Reviewer Instructions

1. Identify all documented vaccines administered to the patient.
2. Extract the vaccine name exactly as documented.
3. Extract administration date when available.
4. Do not infer vaccine categories from memory.
5. Final categorization should be performed using the standardized CDC vaccine reference table (`../references/CDC_Vaccine_Reference_Table.md`).

---

# Example

Note:

"Patient received MMR vaccine in childhood and influenza vaccine in October 2024."

Extraction (Step 1):

Record 1:

* Vaccine_Name: MMR
* Supporting_Evidence: "Patient received MMR vaccine in childhood"
* Administration_Date: Not documented

Record 2:

* Vaccine_Name: Influenza vaccine
* Supporting_Evidence: "influenza vaccine in October 2024"
* Administration_Date: October 2024

Post-processing (Step 2, via `../references/CDC_Vaccine_Reference_Table.md`):

* MMR → Live Vaccine
* Influenza vaccine → Non-Live Vaccine

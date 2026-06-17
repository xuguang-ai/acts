# APOE Genotype Extraction Guideline

## Objective

Determine whether the patient carries APOE ε2, ε3, and/or ε4 alleles based on documented APOE genetic testing results.

Output three independent labels, each taking one of three values:

* APOE2: 0, 1, or NA
* APOE3: 0, 1, or NA
* APOE4: 0, 1, or NA

Definitions:

* 1 = allele present (documented)
* 0 = allele absent (a documented genotype shows this allele is not present)
* NA = not determinable from the note — either no APOE genotype / testing is documented, or only partial genotype information is given and this allele's presence/absence cannot be established

Only use explicitly documented APOE genotype information.

Note: Every individual carries exactly two APOE alleles (each ε2, ε3, or ε4). Therefore a fully documented genotype always yields at least one label = 1, and 0 is assigned only when a documented genotype rules the allele out. If no APOE genotype is documented, all three labels are **NA** (never 0/0/0, which is genotypically impossible).

Do not infer genotype from Alzheimer's disease diagnosis, cognitive impairment, family history, demographic factors, or risk statements.

---

## Background

The APOE gene has three common alleles:

* ε2
* ε3
* ε4

Each individual inherits two APOE alleles, producing one of six common genotypes:

* ε2/ε2
* ε2/ε3
* ε2/ε4
* ε3/ε3
* ε3/ε4
* ε4/ε4

---

## Label Definitions

### APOE2

Assign:

* APOE2 = 1 if at least one ε2 allele is present.
* APOE2 = 0 otherwise.

Positive examples:

* APOE ε2/ε2
* APOE ε2/ε3
* APOE ε2/ε4

---

### APOE3

Assign:

* APOE3 = 1 if at least one ε3 allele is present.
* APOE3 = 0 otherwise.

Positive examples:

* APOE ε2/ε3
* APOE ε3/ε3
* APOE ε3/ε4

---

### APOE4

Assign:

* APOE4 = 1 if at least one ε4 allele is present.
* APOE4 = 0 otherwise.

Positive examples:

* APOE ε2/ε4
* APOE ε3/ε4
* APOE ε4/ε4
* APOE4 carrier
* APOE ε4 positive

---

## Genotype Mapping

| Genotype | APOE2 | APOE3 | APOE4 |
| -------- | ----- | ----- | ----- |
| ε2/ε2    | 1     | 0     | 0     |
| ε2/ε3    | 1     | 1     | 0     |
| ε2/ε4    | 1     | 0     | 1     |
| ε3/ε3    | 0     | 1     | 0     |
| ε3/ε4    | 0     | 1     | 1     |
| ε4/ε4    | 0     | 0     | 1     |

---

## Evidence Sources

Accept evidence from:

* Genetic testing reports
* Neurology notes
* Genetics consultations
* Alzheimer's disease clinic notes
* Laboratory results
* Problem lists containing APOE genotype

Examples:

"APOE genotype: ε3/ε4"

"Patient is heterozygous APOE4 carrier."

"Genotyping demonstrated APOE ε4/ε4."

"APOE result: e2/e3."

---

## Surface Forms / Lexical Variants

APOE and its genotype are documented with consistent but not identical phrasing. Recognize the following as equivalent when extracting.

**Gene name:**

* APOE, ApoE, APO-E, APO E
* Apolipoprotein E
* "APOE gene", "APOE genotype", "APOE genotyping"

**Allele notation** (all equivalent — ε2/ε3/ε4):

* ε2 / ε3 / ε4 (Greek epsilon)
* e2 / e3 / e4 and E2 / E3 / E4
* "epsilon 2 / 3 / 4", "ε4", "type 4", "allele 4"
* Bare digits **only in an explicit APOE genotype context** (e.g., "APOE 3/4", "APOE 4/4")

**Genotype separators** (all equivalent): `ε3/ε4`, `ε3ε4`, `e3/e4`, `E3/E4`, `3/4`, `3,4`

**Zygosity / carrier phrasing:**

* "heterozygous", "homozygous"
* "ε4 carrier", "APOE4 carrier", "ε4 positive", "ε4+", "carries one/two ε4 alleles"

**Negation / absence of a specific allele:**

* "ε4 negative", "APOE4 negative", "no ε4 allele", "non-carrier"

> Caution — disambiguation (general domain note, not from any source paper): "ApoE" can also refer to the **apolipoprotein E protein / lipid measurement** (e.g., "ApoE 4.2 mg/dL"), which is a serum protein level, **not** a genotype. Only extract allele labels from genotype documentation; a numeric lab value with units (e.g., mg/dL) is not an APOE genotype.

---

## Special Cases

### APOE4 Carrier (second allele not specified)

If documentation states a single-allele carrier status **without** giving the full genotype:

* APOE4 carrier
* APOE ε4 positive
* APOE4 heterozygous

Then:

* APOE4 = 1
* APOE2 = NA  (the other allele is unspecified — could be ε2)
* APOE3 = NA  (the other allele is unspecified — could be ε3)

Note: "heterozygous" means the two alleles differ, so exactly one of APOE2/APOE3 is truly present — but which one is not stated, so both are NA.

The same logic applies to any single-allele carrier statement (e.g., "ε2 carrier", "ε3 positive"): the named allele = 1; the unspecified second allele's contributions are NA.

---

### No APOE Genotype Documented

If the note contains no APOE genotype or testing result for the patient:

* APOE2 = NA
* APOE3 = NA
* APOE4 = NA

---

### Homozygous Genotypes

Homozygous statements fully specify both alleles, so all three labels are determined (no NA). "APOE4 homozygous" = ε4/ε4; "APOE2 homozygous" = ε2/ε2, etc.

Example:

"APOE ε4/ε4" (or "APOE4 homozygous")

Output:

* APOE2 = 0
* APOE3 = 0
* APOE4 = 1

Example:

"APOE ε2/ε2"

Output:

* APOE2 = 1
* APOE3 = 0
* APOE4 = 0

---

## Do Not Use as Evidence

Do not infer APOE genotype from the following. When one of these is the only APOE-related content in the note (i.e., no genotype is documented elsewhere), the affected labels are **NA**, not 0.

Do not infer APOE genotype from:

### Alzheimer's disease diagnosis

Example:

"Patient has Alzheimer's disease."

Insufficient evidence.

---

### Cognitive impairment

Example:

"Patient diagnosed with MCI."

Insufficient evidence.

---

### Family history

Example:

"Mother carries APOE4."

Insufficient evidence.

---

### Risk discussion

Example:

"APOE4 increases risk for Alzheimer's disease."

Insufficient evidence.

---

### Planned testing

Example:

"APOE genotyping ordered."

Insufficient evidence.

---

## Output Schema

| Field | Values | Description |
|---|---|---|
| `APOE2` | 0 / 1 / NA | ε2 allele present / absent / not determinable |
| `APOE3` | 0 / 1 / NA | ε3 allele present / absent / not determinable |
| `APOE4` | 0 / 1 / NA | ε4 allele present / absent / not determinable |
| `Supporting_Evidence` | text | Verbatim genotype quote supporting the labels; empty if no genotype is documented |

JSON form:

```json
{
  "APOE2": 0,
  "APOE3": 1,
  "APOE4": 1,
  "Supporting_Evidence": "APOE genotype: ε3/ε4"
}
```

Example (full genotype):

Evidence:
"APOE genotype: ε3/ε4"

Output:

APOE2 = 0
APOE3 = 1
APOE4 = 1

Example (partial information):

Evidence:
"Patient is a heterozygous APOE4 carrier."

Output:

APOE2 = NA
APOE3 = NA
APOE4 = 1

Example (no genotype documented):

Evidence:
(none)

Output:

APOE2 = NA
APOE3 = NA
APOE4 = NA

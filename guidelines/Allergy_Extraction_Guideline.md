# Allergy Entity Extraction Guideline

## Objective

Extract, from the clinical note, the specific substance(s) / allergen(s) that the patient is **allergic, hypersensitive, or intolerant to**.

This is an **entity (span) extraction** task, not a binary classification task. The unit of output is the **allergen entity** — one extracted entity per substance the patient reacts to.

* A note documenting allergies to penicillin and shellfish yields **two** entities: `penicillin`, `shellfish`.
* A note with no documented patient allergen yields **zero** entities.

Each extracted allergen is captured as the verbatim text span naming the substance (and may be normalized to a coded concept downstream).

Use only information documented about the patient. Do not infer beyond the note.

---

## Output Schema

Output a list of allergen entities (zero, one, or many). Each entity:

| Field | Required | Values / Description |
|---|---|---|
| `Allergen` | Yes | The substance span, verbatim |
| `Supporting_Evidence` | Yes | Verbatim text snippet supporting the extraction |
| `Category` | Optional | medication / food / environment / biologic |
| `Type` | Optional | allergy / intolerance |
| `Reaction` | Optional | manifestation (e.g., rash, anaphylaxis) |
| `Severity` | Optional | mild / moderate / severe |
| `Clinical_Status` | Optional | active / inactive / resolved |
| `Verification_Status` | Optional | confirmed / unconfirmed / refuted / entered-in-error |

JSON form:

```json
[
  {
    "Allergen": "<verbatim substance>",
    "Supporting_Evidence": "<verbatim snippet>",
    "Category": "medication | food | environment | biologic | null",
    "Type": "allergy | intolerance | null",
    "Reaction": "<text or null>",
    "Severity": "mild | moderate | severe | null",
    "Clinical_Status": "active | inactive | resolved | null",
    "Verification_Status": "confirmed | unconfirmed | refuted | entered-in-error | null"
  }
]
```

Notes:

* No documented patient allergen → empty list `[]`.
* "No known allergies" (NKA / NKDA) → empty list, optionally with `NoKnownAllergy: true` if your schema records it.
* If attributes are out of scope, return `Allergen` + `Supporting_Evidence` only.

---

## What to Extract (the allergen entity)

Extract the **minimal text span that names the substance** the patient reacts to. The span is the allergen itself — not the surrounding phrase, and not the reaction.

The allergen falls into one of four categories:

### Medication / Drug

Examples (allergen span in **bold**):

* "Allergic to **penicillin** (rash)" → `penicillin`
* "Drug allergy: **sulfa**" → `sulfa`
* "Reaction to **iodinated contrast**" → `iodinated contrast`
* Drug-class allergens are valid: **NSAIDs**, **sulfa drugs**, **cephalosporins**

### Food

* "Known **peanut** allergy" → `peanut`
* "Allergic to **shellfish**" → `shellfish`
* "Intolerant to **lactose**" → `lactose`
* Food-group allergens are valid: **tree nuts**, **dairy**

### Environment

* "Seasonal allergic rhinitis to **pollen**" → `pollen`
* "**Latex** allergy" → `latex`
* "**Bee sting** anaphylaxis" → `bee venom` / `bee sting`
* Also: **dust mites**, **mold**, **animal/pet dander**

### Biologic

(Allergens of biological origin — vaccines, blood products, antisera, biologic therapeutics.)

* "Allergy to **influenza vaccine**" → `influenza vaccine`
* "Reaction to **monoclonal antibody therapy**" → the named agent
* Vaccine components: **egg protein**, **gelatin**

---

## Span Conventions

1. **Mark the substance term only.** In "allergic to penicillin," extract `penicillin`, not "allergic to penicillin."
2. **Class / group allergens are valid entities** (`sulfa drugs`, `NSAIDs`, `tree nuts`) when that is how the note states it.
3. **Lists → one entity per substance.** "Allergies: penicillin, sulfa, shellfish" → three entities.
4. **Brand vs. generic:** extract the substance as written (e.g., `Augmentin` if that is what appears); normalization to a code happens downstream.
5. **The reaction is not the allergen.** In "penicillin → hives," `penicillin` is the entity; `hives` is a reaction attribute (see below), not a separate allergen.
6. **Conjoined allergens:** "allergic to penicillin and cephalosporins" → two entities (`penicillin`, `cephalosporins`).

---

## Optional Attributes per Entity

If your annotation schema captures attributes, record them per extracted allergen:

* **type** — allergy vs intolerance
* **category** — medication / food / environment / biologic
* **reaction / manifestation** — e.g., rash, hives, anaphylaxis, angioedema, GI upset
* **severity** — mild / moderate / severe; low / high
* **clinical status** — active / inactive / resolved
* **verification status** — confirmed / unconfirmed / suspected / refuted / entered in error

If attributes are out of scope, extract the allergen span alone.

---

## What NOT to Extract

Do **not** extract a substance as a patient allergen in these cases.

### No Known Allergies (NKA / NKDA)

"NKDA", "No known allergies", "Allergies: none", "Denies allergies" → **no allergen entity**.
(This is a distinct "no known allergy" statement; if your schema marks it, tag it as a `NoKnownAllergy` marker rather than as an allergen.)

### Family History

"Mother allergic to penicillin", "FH of food allergies" → **no entity** (the allergen belongs to a relative, not the patient).

### Refuted or Entered-in-Error

"Penicillin allergy refuted on rechallenge — not truly allergic", "allergy entry made in error" → **no entity** (the allergy is negated). If your schema records negated entities, tag them as refuted / entered in error rather than treating them as active allergens.

### Suspected / Unconfirmed

"Possible sulfa allergy", "rule out penicillin allergy", "may be allergic to ..." → **do not extract** as a confirmed allergen (or extract and tag as unconfirmed/suspected if uncertain entities are in scope).

### Allergy Evaluation Orders

"Allergy panel ordered", "referred for skin testing" → **no entity** (no allergen confirmed).

### Side Effect Without an Allergy Label

"Nausea from metformin (side effect)" → **no entity**, unless the clinician documents it as an allergy / hypersensitivity / intolerance.

### The Reaction Word Alone

`rash`, `hives`, `anaphylaxis`, `swelling` are reactions/manifestations — not allergens. Extract them only as the **reaction attribute** of an allergen entity, never as the allergen itself.

---

## Status Handling for Extraction

Recast from the binary version — status now governs *whether (and how) an allergen is extracted*, not a 0/1 flag:

* **Resolved / inactive** allergen → still **extract** it (the reaction was real; it remains in the patient's allergy history). Tag as resolved/inactive if attributes are captured.
* **Refuted / entered-in-error** → **do not extract** as an active allergen (negated).
* **Confirmed / presumed** → extract.
* **Unconfirmed / suspected** → exclude, or extract-and-flag if uncertain entities are in scope.

> Decision point: whether to extract uncertain (`unconfirmed`) or negated (`refuted`/`entered-in-error`) allergens at all, vs. extract-and-tag them, depends on your downstream use. Default here: extract only confirmed/presumed and resolved/inactive allergens.

---

## Worked Examples

| Note text | Extracted allergen entit(ies) | Notes |
|---|---|---|
| "Allergic to penicillin (rash)." | `penicillin` | reaction = rash |
| "Allergies: penicillin, sulfa, shellfish" | `penicillin`, `sulfa`, `shellfish` | list → 3 entities |
| "Allergic to penicillin and cephalosporins" | `penicillin`, `cephalosporins` | conjoined → 2 entities |
| "Seasonal allergic rhinitis to pollen" | `pollen` | category = environment |
| "Intolerant to lactose" | `lactose` | type = intolerance |
| "Penicillin allergy, resolved" | `penicillin` | status = resolved |
| "Penicillin allergy refuted on testing" | (none) | negated |
| "NKDA" | (none) | no known allergy |
| "Mother allergic to bees" | (none) | family history |
| "Allergy panel ordered" | (none) | evaluation only |
| "Nausea from metformin (side effect)" | (none) | side effect, not allergy |

---

## Annotation Decision Logic (per candidate substance)

1. Is a specific substance named **as something the patient is allergic / hypersensitive / intolerant to**?

   * No → not an allergen entity.

2. Is it attributed to the **patient** (not a relative)?

   * No → do not extract.

3. Is the allergy **refuted**, **entered-in-error**, or only **suspected/unconfirmed**?

   * Yes → do not extract (or extract-and-tag, per schema).

4. Is the mention only a **side effect**, an **evaluation order**, or a **reaction word** (not the substance)?

   * Yes → do not extract as an allergen.

5. Otherwise → **extract the substance span** as an allergen entity (include `resolved`/`inactive` allergens), with optional attributes.

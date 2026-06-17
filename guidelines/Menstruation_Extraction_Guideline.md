# Menstruation Extraction Guideline

## Objective

Extract menstruation-related information from clinical notes.

The Menstruation domain contains two sub-concepts:

1. Postmenopause
2. Last Menstruation Date

Each concept should be abstracted independently.

Output:

* Postmenopause: 0/1 (binary label)
* Last_Menstruation_Date: extracted text span — the documented date/time of the last menstrual period; empty / none if not documented

Use only information explicitly documented in the patient's medical record.

---

## Output Schema

| Field | Type | Description |
|---|---|---|
| `Postmenopause` | 0 / 1 | 1 = patient is postmenopausal / has undergone menopause |
| `Postmenopause_Evidence` | text | Verbatim snippet supporting `Postmenopause`; empty when 0 |
| `Last_Menstruation_Date` | span / null | Extracted date or time expression of the LMP; null if not documented |
| `Last_Menstruation_Date_Evidence` | text | Verbatim snippet containing the date; empty if none |

JSON form:

```json
{
  "Postmenopause": 0,
  "Postmenopause_Evidence": "<verbatim snippet or empty>",
  "Last_Menstruation_Date": "<date/time span or null>",
  "Last_Menstruation_Date_Evidence": "<verbatim snippet or empty>"
}
```

(The Example sections below show the two core fields; the evidence fields follow the same per-field convention.)

---

# Part 1: Postmenopause

## Definition

Postmenopause refers to the stage of life after menopause, indicating that the patient has permanently ceased menstruation and is currently postmenopausal.

## Label

### Postmenopause = 1

Assign 1 if there is documentation indicating that the patient is postmenopausal or has undergone menopause.

Examples:

* Postmenopausal
* Post-menopausal
* Postmenopausal female
* History of menopause
* Menopause at age 50
* Natural menopause
* Surgical menopause

Examples:

"Patient is postmenopausal."

"Menopause occurred at age 52."

"History of surgical menopause."

---

### Postmenopause = 0

No evidence that the patient is postmenopausal.

---

# Part 2: Last Menstruation Date

## Definition

Documentation of the patient's last menstrual period (LMP) or last menstruation date.

## Extraction (span)

`Last_Menstruation_Date` is a **span** extraction, not a binary label. Extract the text stating **when** the patient's last menstrual period / last menstruation occurred — i.e., the date or time expression itself.

### What to extract

* Explicit calendar dates — e.g., `05/10/2026`, `5/10/26`
* Month / year — e.g., `January 2025`
* Relative time expressions tied to the LMP — e.g., `two weeks ago`, `3 months ago`

### Span conventions

* Extract the **date/time expression only**, not the surrounding label words. "LMP: 05/10/2026" → span = `05/10/2026`.
* If multiple LMP mentions appear, extract the one designated as the most recent / last menstrual period.
* Do **not** extract an age of menopause (e.g., "menopause at age 51") as a last menstruation date — an age is not a documented LMP.

### Examples

"LMP: 05/10/2026." → `05/10/2026`

"Last menstrual period was two weeks ago." → `two weeks ago`

"Last menses occurred in January 2025." → `January 2025`

### No span

If no last menstrual period or menstruation date is documented, return no span (empty / none).

---

# Exclusion Criteria

Do NOT assign positive labels based on:

### Family History

Example:

"Mother entered menopause at age 45."

Not evidence for the patient.

---

### Educational Discussion

Example:

"Discussed menopause symptoms."

Not evidence unless patient status is documented.

---

### Planned Evaluation

Example:

"Gynecology referral for menstrual concerns."

Not evidence unless menstrual information is documented.

---

# Evidence Hierarchy

Strong evidence:

* Postmenopausal
* Menopause at age X
* Surgical menopause
* LMP documented
* Last menstrual period documented

Weak evidence (do not use alone):

* Menopause discussed
* Menstrual issues discussed
* Menstrual evaluation planned

---

# Example 1

Note:

"Patient is postmenopausal."

Output:

* Postmenopause = 1
* Last_Menstruation_Date = (none)

---

# Example 2

Note:

"LMP 05/10/2026."

Output:

* Postmenopause = 0
* Last_Menstruation_Date = "05/10/2026"

---

# Example 3

Note:

"Postmenopausal female. Menopause at age 51."

Output:

* Postmenopause = 1
* Last_Menstruation_Date = (none)   (an age is not a documented LMP)

---

# Example 4

Note:

"LMP two weeks ago. Cycles regular."

Output:

* Postmenopause = 0
* Last_Menstruation_Date = "two weeks ago"

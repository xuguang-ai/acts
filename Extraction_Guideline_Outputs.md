# Extraction Guideline Outputs

Required output fields for the binary / entity / span extraction guidelines. Optional attributes (e.g., allergy category, reaction, status; vaccine administration date) are omitted here — see each guideline for the full schema. Every task additionally returns `Supporting_Evidence` (the verbatim snippet supporting the value).

| Variable | Type | Range / values | Meaning / notes | Guideline |
| --- | --- | --- | --- | --- |
| `Allergen` | Span (entity) | substance text | One entity per substance the patient is allergic / hypersensitive / intolerant to; empty if none documented | [Allergy](guidelines/Allergy_Extraction_Guideline.md) |
| `APOE2` | Categorical | `0`, `1`, `NA` | ε2 allele present / absent / not determinable | [APOE](guidelines/APOE_Genotype_Extraction_Guideline.md) |
| `APOE3` | Categorical | `0`, `1`, `NA` | ε3 allele present / absent / not determinable | [APOE](guidelines/APOE_Genotype_Extraction_Guideline.md) |
| `APOE4` | Categorical | `0`, `1`, `NA` | ε4 allele present / absent / not determinable | [APOE](guidelines/APOE_Genotype_Extraction_Guideline.md) |
| `Postmenopause` | Binary | `0`, `1` | `1` = patient is postmenopausal / has undergone menopause | [Menstruation](guidelines/Menstruation_Extraction_Guideline.md) |
| `Last_Menstruation_Date` | Span (date) | date / time expression, else `null` | Documented last menstrual period (LMP) date or time expression | [Menstruation](guidelines/Menstruation_Extraction_Guideline.md) |
| `Vaccine_Name` | Span (entity) | vaccine name text | One entity per vaccine documented as administered / received / completed | [Vaccine](guidelines/Vaccine_Extraction_Guideline.md) |
| `Vaccine_Category` | Categorical | `Live Vaccine`, `Non-Live Vaccine`, `BCG`, `Active Amyloid or Tau Immunization` | Assigned in post-processing from `Vaccine_Name` using the CDC reference table | [Vaccine](guidelines/Vaccine_Extraction_Guideline.md); [CDC table](references/CDC_Vaccine_Reference_Table.md) |

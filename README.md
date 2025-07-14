OMOP CDM ETL Pipeline
This project implements a complete Extract-Transform-Load (ETL) pipeline to convert synthetic Synthea healthcare data into the OMOP Common Data Model (CDM). 
The updated version directly addresses reviewer feedback related to completeness, deduplication, and vocabulary mapping.

Improvements Based on Feedback
1. OMOP CDM Table Completeness
Issue: Previous versions omitted many optional columns, reducing OMOP compliance and downstream utility.

Fix:
All OMOP tables (person, visit_occurrence, condition_occurrence, drug_exposure) are now created with full column definitions including required and optional fields.
Optional fields such as birth_datetime, visit_source_value, days_supply, sig, stop_reason, etc., are populated when available.
The create_complete_omop_tables() function ensures consistent schema initialization before every ETL run.

2. Medication Deduplication Logic
Issue: Prior logic used max-duration merging per (patient, drug), which incorrectly combined distinct treatment periods.

Fix:
A new deduplication function identifies separate treatment episodes based on a 7-day treatment gap rule.
This ensures multiple prescriptions for the same medication across different timeframes are treated as distinct exposures.
Although source prioritization (e.g., EHR > NLP) was not applicable to Synthea data, the design is extensible for that logic.

3. Standard Code Mapping
Issue: Mapping based solely on the CONCEPT table ignores OMOP-recommended mappings via CONCEPT_RELATIONSHIP.

Fix:
The get_concept_mapping() function uses the CONCEPT_RELATIONSHIP table with relationship_id = 'Maps to'.
This correctly links non-standard source vocabularies (e.g., ICD10CM, RxNorm) to standard SNOMED or RxNorm concepts.
Unmapped concepts are retained with a fallback concept_id = 0, allowing transparency and post-processing.


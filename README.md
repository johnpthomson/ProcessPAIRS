![ProcessPAIRS Logo](logo.png)


**PROCESSPAIRS**

---

**OVERVIEW**

ProcessPairs is an R-based framework for the identification and biological interpretation of PARP inhibitor (PARPi) resistance mechanisms using paired tumour samples collected before treatment (PRE) and after relapse (POST).

The framework integrates somatic SNVs, copy-number alterations, curated resistance gene annotations, BRCA restoration modelling, tumour evolutionary analysis and pre-existing resistance architecture (PRA) profiling to generate biologically informed resistance classifications and patient-level resistance summaries.

---

**KEY FEATURES**

SNV-ID resolved tumour evolution tracking

Copy-number and SNV integration

Biologically curated resistance gene annotation framework

BRCA restoration and ORF modelling

Driver event prioritisation

Pathway-level resistance annotation

Pre-existing Resistance Architecture (PRA) detection

Automated resistance classification

Human-readable resistance summaries and evolutionary narratives

---

**REQUIRED INPUT FILES**

merged

Gene-level copy-number and SNV matrix containing PRE, POST and gDNA samples.

snv_sh

Variant-level SNV catalogue used for evolutionary reconstruction.

Gene_list_input_processpairs_v10-7.txt

Curated resistance annotation table containing pathway, mechanism and classification information.

Oncokb_v1_Sept2023_sh.txt

OncoKB tumour suppressor and oncogene annotations.

exclude.txt

Optional patient exclusion list.

---

**WORKFLOW**

**1. DATA HARMONISATION**

Input datasets are loaded and standardised.

Missing copy-number values are converted to a diploid state (CN=2).

SNV counts are aggregated per gene and sample.

Patient identifiers are extracted from sample names.

Resistance-associated genes are selected using the curated annotation table.

---

**2. RESISTANCE GENE ANNOTATION**

Genes are annotated using curated biological priors.

Annotations include:

CN directionality (GAIN / LOSS / UNCLEAR)

SNV interpretation axis

Biological pathway

Resistance mechanism

High-level classifier group

Major resistance classes include:

BRCA restoration

HR bypass

Fork protection

Drug target adaptation

---

**3. GENE-LEVEL EVENT MATRIX CONSTRUCTION**

For every patient and resistance-associated gene, genomic states are summarised across:

gDNA

PRE tumour

POST tumour

Generated metrics include:

Copy-number state

SNV burden

Pathway annotation

Mechanism annotation

---

**4. SNV EVENT INTERPRETATION**

SNVs are interpreted using curated biological rules.

POST_ACQUIRED_PATHOGENIC

Assigned when mutations emerge in POST samples or expand during treatment.

SECONDARY_REVERSION

Assigned when candidate BRCA restoration-associated mutations emerge following treatment.

---

**5. COPY NUMBER EVENT INTERPRETATION**

CN_LOSS

Assigned when loss-driven resistance genes decrease in copy number from PRE to POST.

CN_GAIN

Assigned when gain-driven resistance genes increase in copy number from PRE to POST.

Genes without defined biological directionality are excluded from interpretation.

---

**6. SNV-ID EVOLUTIONARY RECONSTRUCTION**

Tumour evolution is reconstructed using variant-level SNV tracking.

Each SNV is classified as:

Acquired

Lost

Shared

Outputs:

Tumor_Evolution

SNV_Gene_Evolution

This ensures evolutionary inference is driven by variant-level dynamics rather than gene-level aggregation.

---

**7. RESISTANCE EVENT DETECTION**

SNV and CN interpretations are merged into composite resistance events.

Each event is annotated with:

Gene

Pathway

Mechanism

Classifier group

Event type

Outputs include:

resistance_report

driver_events

---

**8. DRIVER EVENT PRIORITISATION**

Resistance-associated events are ranked using a biologically informed scoring framework.

Highest priority:

True BRCA reversion events

BRCA restoration events

Intermediate priority:

HR bypass

Fork protection

Drug target adaptation

Outputs:

main_driver

main_driver_mechanism

driver_score

---

**9. BRCA RESTORATION ANALYSIS**

A dedicated BRCA restoration module evaluates potential open reading frame (ORF) rescue.

Frameshift annotations are parsed from protein change fields and cumulative frameshift lengths are calculated.

Putative ORF restoration is assigned when:

total_frameshift_length %% 3 == 0

Outputs:

BRCA_orf_restore

BRCA_orf_details

BRCA_orf_variants

Only ORF-restoring events are retained as true BRCA restoration events.

---

**10. PRE-STATE ANALYSIS**

Baseline genomic architecture is summarised prior to treatment.

Outputs:

PRE_STATE

PRE_SNV_SUMMARY

PRE_CN_GOI_SUMMARY

Genes of interest include:

BRCA1

BRCA2

TP53BP1

CCNE1

---

**11. PRE-EXISTING RESISTANCE ARCHITECTURE (PRA)**

The PRA framework identifies adaptive genomic features already present in PRE samples.

Sentinel genes:

BRCA1

BRCA2

TP53BP1

Outputs:

PRA_events

PRA_event_count

PRA_gene_count

PRA_high_confidence

PRA is intended to capture genomic architecture that may predispose tumours to future resistance evolution.

---

**12. RESISTANCE CLASSIFICATION**

Class 1

BRCA-associated adaptive restoration

Class 2A

HR bypass

Class 2B

Fork protection

Class 2C

Drug target adaptation

Class 3

No clear genomic resistance mechanism

---

**13. BIOLOGICAL OVERRIDE RULES**

Fork protection genes:

TP53BP1

MAD2L2

REV7

RIF1

SHLD1

SHLD2

SHLD3

Drug adaptation genes:

PARP1

PARP2

PARG

BRCA restoration classifications are never overridden.

---

**14. PATHWAY ANNOTATION**

All resistance-associated genes are mapped to curated pathways including:

Homologous recombination

Fanconi anaemia

Shieldin/NHEJ

PARP signalling

Alternative end joining

Replication stress response

Chromatin regulation

Outputs:

pathways_involved

mechanisms_involved

---

**15. RESISTANCE NARRATIVES**

The pipeline generates publication-ready biological summaries.

Outputs:

resistance_summary

resistance_headline

resistance_journey

Example:

"Pre-relapse adaptive resistance features were present followed by acquisition of a BRCA reversion event with concurrent HR bypass features."

---

**MAIN OUTPUT FILES**

final_out_v10-7.txt

Primary publication-ready summary table.

Contains:

Patient identifier

Final resistance classification

BRCA context

PRA status

Resistance reports

Dominant resistance driver

BRCA CN status

BRCA ORF restoration status

Tumour evolution summaries

Pathway annotations

Biological interpretations

Evolutionary narratives

---

hrd_status_wide_v10-7.txt

Complete patient-level output containing all intermediate and derived variables generated by the pipeline.

---

driver_events_v10-7.txt

Event-level resistance table.

One row per interpreted resistance event.

Includes:

Gene

Event type

Mechanism

Classifier group

Driver score

---

supplementary_resistance_journey_v10-7.txt

Compact publication-ready summary table containing:

Patient identifier

Final classifier

Resistance headline

Resistance journey narrative

---

**BIOLOGICAL INTERPRETATION NOTES**

BRCA restoration requires evidence of functional restoration.

Events lacking ORF restoration evidence are reclassified as:

BRCA_secondary_mutation_event

rather than true BRCA restoration.

PRA is intended to identify adaptive genomic configurations present before treatment that may facilitate future resistance evolution.

---

**INTENDED USE**

ProcessPairs is designed for exploratory and translational cancer genomics studies investigating resistance evolution following PARP inhibitor exposure.

The framework combines genomic observations with curated biological priors to generate mechanistically informed resistance classifications suitable for downstream biological interpretation and hypothesis generation.


![ProcessPAIRS workflow diagram](ProcessPAIRS%20v10-7%20workflow%20diagram.png)

________________________________________
**CODE HISTORY AND RECENT UPDATES**
________________________________________


**Key Update in v10-1**
This version represents a substantial redesign of the resistance interpretation framework, with major conceptual updates in how genomic events are defined and prioritised. Major change in the input data parsing tables which drive the model logic, complete rebuild of logic based on table loading and BRCA ORF modelling.  

**1. Integration of curated, peer-reviewed resistance priors**
The pipeline now directly incorporates a manually curated and externally informed resistance annotation table (Gene_list_input_processpairs_v9-8.txt), which encodes prior biological knowledge for each gene, including:
•	CN directionality rules (GAIN / LOSS / UNCLEAR) 
•	SNV functional axis (POST_ACQUIRED_PATHOGENIC vs SECONDARY_REVERSION) 
•	Pathway membership 
•	Mechanism classification 
•	High-level classifier grouping (e.g. HR bypass, fork protection, BRCA restoration) 
This represents a shift from purely data-driven event calling to a hybrid inference model, where:
genomic observations are explicitly interpreted through a biologically pre-defined resistance framework
As a result, gene-level SNV and CN events are no longer interpreted in isolation, but are contextualised within curated, peer-reviewed resistance biology.
________________________________________
**2. Explicit BRCA ORF restoration module**
A dedicated BRCA structural restoration module has been introduced to predict orf restoration in cases with secondary mutation. 
This module:
• Operates only where second POST only SNV event occurs downstream of original SNV event seen in both PRE and POST. 
•	Extracts frameshift protein annotations (e.g. fs*21) 
•	Sums cumulative frameshift length across variants per gene 
•	Evaluates reading-frame preservation using modulo-3 logic: 
total frameshift length % 3 == 0 → putative ORF restoration
This enables detection of:
•	cryptic BRCA1/2 reactivation events 
•	compound frameshift rescues 
•	structural restoration despite persistent SNV burden 
Importantly, BRCA ORF restoration is then used as a hierarchical override feature in downstream classification, ensuring that:
structural reversion signals take priority over aggregate SNV burden or CN state changes when defining BRCA-associated adaptive resistance
________________________________________
**3. Conceptual impact on resistance modelling**
Together, these updates shift the framework from:
•	purely observational genomic comparison
to 
•	biologically constrained, annotation-driven resistance inference 
Specifically:
•	Gene behaviour is now interpreted through curated resistance priors (not post-hoc clustering) 
•	BRCA biology is extended beyond SNV/CN state into functional protein restoration space 
•	Resistance tiers are therefore anchored to mechanistic interpretation rather than purely statistical recurrence 


________________________________________
**Key Updates in v10-2**
This version introduced exclusion table load ins to parse patient IDs for cohort curation.

**Added support for external cohort exclusion using exclude.txt.**
Patients listed in this file are removed immediately prior to final output table generation.
Enables:
QC-driven exclusions
reproducible cohort curation

________________________________________
**Key Updates in v10-3**
This version substantially refines the biologically informed resistance attribution framework through hierarchical driver override prioritisation, ORF-aware BRCA restoration interpretation, mechanism-aware main driver reassignment, expanded canonical fork protection logic, and improved harmonisation between genomic events, resistance mechanisms, and final adaptive resistance state classification.

**1. Biological driver override framework refined**
Refined biologically informed driver override hierarchy to improve concordance between final_classifier, resistance_mechanism, and main_driver.
Added hierarchical prioritisation of:
canonical fork protection genes
PARP adaptation genes
HR bypass drivers
validated BRCA restoration events
Prevents biologically weaker events from incorrectly dominating final driver assignment when stronger mechanistic resistance signals coexist.

**2. BRCA restoration interpretation updated**
Added stricter BRCA restoration interpretation logic such that:
SECONDARY_REVERSION is only assigned when ORF restoration evidence is present (BRCA_orf_restore == "Y")
non-restorative secondary BRCA events are reclassified as:
SECONDARY_MUTATION
Improves separation of:
probable functional BRCA rescue events
secondary BRCA alterations lacking evidence of restored protein function.

**3. Main driver selection aligned to biological resistance states**
Updated main driver assignment rules to prevent isolated BRCA CN gain events from dominating non-BRCA resistance states.
Ensures fork protection states preferentially select:
TP53BP1
MAD2L2
Shieldin-axis genes
Ensures drug adaptation states preferentially select:
PARP1
PARP2
PARG
Improves biological consistency between:
final_classifier
resistance_mechanism
main_driver


________________________________________
**Key Updates in v10-7**
This version classifies BRCA_CN status in relapse and also summarises PRE and POST pathogenic and likely pathogenic SNV counts and names.


________________________________________
**Key Updates in v10-8**
This version adds a column for PRE CN status over 5 genes of interest, BRCA1,BRCA2,TP53BP1,CCNE1 and MYC.




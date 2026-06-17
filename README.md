![ProcessPAIRS Logo](logo.png)


**PROCESSPAIRS V11-2 (17th June 2026)**

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


SNV data : extract of MAF with patient ID per PRE and POST, variant details, VAF and protein change.

CN data: CN calls . per gene, per PRE and POST sample

Gene_list_input_processpairs.txt : Table with published info and logic to support possible resistance mechanisms and directions (i.e. CN loss or LOF mutation)

Pathway information : published gene set pathway lists

Oncokb_v1_Sept2023_.txt : OncoKB gene set info

exclude.txt : Optional patient exclusion list.

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



---
**EXAMPLE_OUTPUT_VISUALS**

![Alluvial Stankey](Alluvial%20stankey4.png)





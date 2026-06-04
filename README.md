![ProcessPAIRS Logo](logo.png)


**Methods Overview (v10-8: June 4th 2026)**
This analysis pipeline was developed to identify and classify homologous recombination deficiency (HRD)-associated genomic resistance mechanisms in paired tumour samples collected before and after PARP inhibitor (PARPi) exposure. The workflow integrates somatic single nucleotide variant (SNV) data (SNV-ID resolved), copy-number (CN) alterations and pathway-level biological priors to infer adaptive tumour evolution and resistance states as well as explain baseline genomic states which may be selected for resistance.
The analysis was implemented in R using custom scripts and includes the following major stages:
Data loading and harmonisation
Resistance gene annotation integration
SNV-ID resolved evolutionary reconstruction
CN and SN event interpretation (PRE and POST)
Resistance event detection
Tiered resistance mechanism classification
Driver event prioritisation
Pathway-level annotation
Tumour evolutionary profiling
Final biologically informed resistance classification
________________________________________
**1. Input Data and Preprocessing**
The pipeline integrates the following input datasets:
•	Paired tumour SNV calls from pre-treatment and post-relapse samples (snv_sh) 
•	Copy-number calls from matched tumour samples (merged) 
•	Curated HRD / resistance gene annotation table (Gene_list_input_processpairs_v9-8.txt) 
•	OncoKB gene annotations defining tumour suppressor genes (TSGs) and oncogenes 
Genes were stratified into curated resistance-associated categories defined in the annotation table, including:
•	HR bypass genes 
•	Fork protection genes 
•	BRCA restoration-associated genes 
•	Replication stress and chromatin modifiers 
Copy-number values were standardised using a custom helper function in which missing or undefined values were treated as diploid (CN = 2).
SNV burden per gene was calculated per sample and aggregated across SNV slots (snv.id.1, snv.id.2) in the merged object.
________________________________________
**2. Generation of Gene-Level HRD Event Matrices**
For each patient and resistance-associated gene, SNV counts and CN states were aggregated across:
•	gDNA (germline) 
•	Pre-treatment tumour 
•	Post-relapse tumour 
These data were reshaped into a long-format matrix (hrd_long) enabling direct temporal comparison of genomic states across treatment.
Each gene-timepoint combination is represented by:
•	CN state (diploid-normalised) 
•	SNV count per timepoint 
•	Resistance gene annotations (pathway, mechanism, classifier group) 
________________________________________
**3. Interpretation of Copy-Number Events**
Copy-number alterations were interpreted using curated directional rules defined in the annotation file.
For loss-directed genes:
•	CN reduction from pre- to post-treatment → CN_LOSS 
For gain-directed genes:
•	CN increase from pre- to post-treatment → CN_GAIN 
Unannotated or ambiguous genes were excluded from directional inference.
BRCA1/2 CN alterations were treated separately within downstream BRCA context classification.
________________________________________
**4. Interpretation of SNV Events (Gene-Level)**
SNV dynamics were interpreted using gene-level longitudinal comparisons:
•	Pre-treatment SNV count 
•	Post-relapse SNV count 
SNV interpretations include:
•	POST_ACQUIRED_PATHOGENIC: 
o	Emergence of SNVs post-treatment 
o	Expansion in SNV burden 
•	SECONDARY_REVERSION: 
o	Emergence or expansion of putative reversion-associated SNVs 
Optional VAF expansion criteria were included in annotation logic but not required for classification.
________________________________________
**5. SNV-ID Resolved Evolutionary Reconstruction (Key Update v10-1)**
A major update in this version introduces SNV-ID resolved evolutionary tracking using the full SNV catalogue (snv_sh).
5.1 SNV catalogue construction
SNVs were parsed from:
•	gene 
•	snv_id 
•	patient 
•	sample type (gDNA / Pre / Post) 
This creates a master SNV catalogue ensuring variant-level resolution.
5.2 Presence/absence matrix
Each SNV-ID was encoded as binary presence across:
•	Pre-treatment 
•	Post-relapse 
5.3 SNV evolutionary states
Each SNV-ID was classified as:
•	Acquired: absent Pre → present Post 
•	Lost: present Pre → absent Post 
•	Shared: present in both Pre and Post 
5.4 Gene-level aggregation
SNV-ID level states were collapsed to gene-level summaries including:
•	gene lists per evolutionary class 
•	number of SNVs per class 
•	SNV-ID counts (primary quantitative measure) 
5.5 Patient-level summaries
Two outputs were generated:
•	Tumor_Evolution: SNV state counts per patient 
•	SNV_Gene_Evolution: gene-level breakdown of SNV state classes 
This ensures evolutionary inference is driven by variant-level dynamics rather than gene-level aggregation bias.
________________________________________
**6. Identification of Resistance Events**
Gene-level SNV and CN interpretations were merged into composite resistance events.
Each event is annotated according to:
•	Gene identity 
•	SNV event type 
•	CN event type 
•	HRD gene classification 
•	Pathway membership 
Only genes with at least one SNV or CN event in the resistance gene set are considered.
________________________________________
**7. Driver Event Scoring System**
A quantitative driver scoring framework ranks resistance-associated events.
Scoring prioritises:
•	BRCA restoration and reversion events (highest score) 
•	HR bypass and fork protection mechanisms (intermediate score) 
•	chromatin and replication stress modifiers (lower score) 
•	complex multi-pathway events (lowest structured score tier) 
Each gene-event pair receives a driver score used for ranking patient-level dominant events.
________________________________________
**8. Patient-Level Resistance Aggregation**
For each patient, resistance features are aggregated into:
•	Full resistance report string 
•	Presence of: 
o	BRCA restoration events 
o	HR bypass events 
o	Fork protection events 
o	Drug target adaptation events 
o	Complex multi-mechanism states 
Complexity metrics include:
•	total driver events 
•	number of unique driver genes 
•	diversity of event types 
________________________________________
**9. Resistance Tier Assignment**
A hierarchical classification framework assigns each patient to a resistance tier:
Tier 1 — BRCA-associated adaptive restoration
Includes:
•	BRCA SNV acquisition 
•	BRCA reversion-like events 
•	CN-associated BRCA restoration 
Tier 2a — HR bypass
Includes replication stress adaptation and HR pathway rewiring
Tier 2b — Fork protection
Includes Shieldin-axis and replication fork stability mechanisms
Tier 2c — Drug target adaptation
Includes PARP axis and drug-response pathway changes
Tier 3 — Complex adaptive genomic state
Defined by:
•	multiple concurrent resistance mechanisms 
•	high driver diversity 
•	mixed event classes 
Tier 4 — No clear genomic resistance mechanism
________________________________________
**10. Biological Override Rules**
Post-classification biological sanity checks enforce known pathway biology:
•	TP53BP1 / FANCA / FANCD2 → forced fork protection assignment 
•	PARP pathway and drug response genes → forced drug target adaptation (unless overridden by fork protection) 
•	BRCA-associated adaptive restoration is never overridden 
These rules ensure canonical biological interpretation is preserved.
________________________________________
**11. Main Driver Assignment**
A single dominant resistance event per patient is selected using:
•	highest driver score 
•	biological hierarchy 
•	event coherence 
Outputs include:
•	main driver gene 
•	associated mechanism 
•	classifier group 
•	driver score 
________________________________________
**12. Pathway-Level Annotation**
All resistance-associated genes are mapped to curated pathways:
•	Homologous recombination (HR) 
•	Fanconi anaemia (FA) 
•	NHEJ / Shieldin axis 
•	PARP signalling axis 
•	Alternative end joining (Alt-EJ / POLQ) 
•	Replication stress response 
•	Chromatin and DNA repair modifiers 
Both SNV- and CN-driven events contribute to pathway enrichment.
________________________________________
**13. Tumour Evolution Analysis**
SNV evolution is quantified across timepoints using SNV-ID resolved states:
•	Acquired 
•	Lost 
•	Shared 
Patient-level summaries quantify event counts per category.
Genomic directionality is inferred as:
•	Adaptive (dominant resistance evolution) 
•	Mixed (multiple competing processes) 
•	Emergent (limited or early events) 
•	No evidence of adaptation 

________________________________________
**14. BRCA ORF Analysis**
This module:
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
•	coded as either BRCA_restoration or BRCA_secondary_mutation_event

________________________________________
**15. PRE and POST SNV count and GOI list**
This module:
• Identifies genes of interest (GOIs) from the curated resistance panel harbouring SNVs in the Pre- and Post-treatment samples
• Counts the number of unique resistance-associated genes with SNV events in each sample
• Generates compact patient-level summaries in the format:
2; ATM, BRCA1
This enables detection of:
• Baseline resistance-associated mutational burden
• Emergence of new resistance-gene SNVs at relapse
• Persistence or loss of resistance-associated mutations across treatment
• Expansion of HRD and PARPi resistance pathway alterations over time
Summaries are restricted to the curated resistance gene panel txt file, providing a biologically focused measure of resistance evolution rather than a genome-wide SNV count.
Outputs:
• PRE_SNV_SUMMARY = SNV count and gene list for Pre-treatment samples
• POST_SNV_SUMMARY = SNV count and gene list for Post-relapse samples

________________________________________
**16. external cohort exclusion**
•	Load in exclude.txt with patient IDs intending to remove from analysis. Patients listed in this file are removed immediately prior to final output table generation.

________________________________________
**17. Final Biological Resistance Classification**
A unified classifier integrates:
•	resistance tier
•	PRE tumour genomic states
•	POST tumour genomic states
•	POST sample resistance driver event structure 
•	pathway involvement 
•	BRCA restoration status 
•	evolutionary complexity 
Final classes:
Class	Interpretation
1.BRCA_associated_adaptive_restoration	BRCA restoration or adaptive reversion
2a.HR_bypass	HR pathway bypass / replication stress adaptation
2b.Fork_protection	Fork protection / Shieldin-axis resistance
3.Complex_adaptive_genomic_state	Mixed adaptive evolution
4.No_clear_genomic_resistance	No convincing mechanism
Post-hoc biological checks ensure canonical pathway assignments remain consistent with known resistance biology.


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




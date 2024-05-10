# CBaSE

CBaSE is a tool which derives gene-specific probabilistic estimates of the strength of negative and positive selection in cancer.

## Running CBaSE

Download the CBaSEv1.2.zip file from [https://weghornlab.net/software.html](https://weghornlab.net/software.html), unzip it and run the following command within the folder:

> python CBaSE_v1.2.py INFILE VCF BUILD CMODE MODEL OUTNAME

<br>

| Argument | Description | Type |
|:--------------|:-----------|:------------|
| INFILE | Somatic mutation data input file. | str |
| VCF      | 1 = input format is vcf, 0 = input format is CBaSE v1.0 input format. | int |
| BUILD      | One of [hg19, hg38] (not used when VCF==0). | str |
| CMODE      | 3 = trinucleotides, 5 = pentanucleotides, 7 = heptanucleotides. | int |
| MODEL      | Model choice for P(lambda): 0 = all, 1 = Gamma (G), 2 = InverseGamma (IG), 3 = Exp+G, 4 = Exp+IG, 5 = G+G, 6 = G+IG. | int |
| OUTNAME      | Name of data set. | str |

#### More on input formats:

| Value | Input file columns |
|:--------------|:-----------|
| VCF==0 | [1. gene symbol, 2. mutation effect, 3. alternate allele, 4. context index, 5. sample ID (optional)] |
| VCF==1 | [1. CHROM, 2. POS (1-based), 3. ID (not used), 4. REF, 5. ALT, ... (not used), 10. SAMPLE_ID (optional)] or<br>[1. CHROM, 2. POS (1-based), 3. ID (not used), 4. REF, 5. ALT, 6. SAMPLE_ID (optional)] |

Example input file format for VCF==0:
| Gene symbol |	Mutation effect |	Alternate allele |	Context index |
|:---|:---|:---|:---| 
| ECE1 |	coding-synon |	C |	26 |
| SAMD11 |	missense |	A |	53 |
| TNFRSF4 |	nonsense |	A |	52 |

(1) **Gene symbol**: Official gene symbol as used in the [UCSC knownGene track](http://genome.ucsc.edu/cgi-bin/hgTables?clade=mammal&org=Human&hgta_group=genes).<br>
(2) **Mutation effect**: One of {“missense”, “nonsense”, “coding-synon”}, denoting missense, nonsense (stop-gain and stop-loss), and synonymous mutations, respectively.<br>
(3) **Alternate allele**: The final allele after the mutation event; one of {A, C, G, T}.<br>
(4) **Context index**: Index of the sequence context of the reference allele; 0-based indices of tri- or pentanucleotide contexts can be found [here](http://genetics.bwh.harvard.edu/cbase/contexts_map.txt).<br>

Example input file format for VCF==1:
| #CHROM |	POS |	ID |	REF | ALT |
|:---|:---|:---|:---|:---| 
| 1	| 1221031	| .	| G	| A |
| 1	| 1230156	| .	| G	| A |
| 1	| 1247523	| .	| G	| T |

#### More on model choice:

| Argument | Input file columns |
|:--------------|:-----------|
| MODEL==0 | All six possible models are fitted and model selection is done based on the Akaike information criterion. |
| MODEL>0 | Only the model with index "MODEL" of the six possible functional forms is fitted. |

## Output

CBaSE creates different output files:

| File name | Description |
|:---|:---|
| q_values_\*.txt | Results of the selection inference and summary stats (see below). |
| mutation_matrix_\*.txt | Per-context mutation probabilities. |
| output_data_preparation_\*.txt | Auxiliary file with data also contained in q_values_\*.txt, except exonic sequence length in bp (column 8) and the expected value of lambda_s (column 9) for each gene. |
| pofkgivens_\*.txt | Estimated probability distribution of the number of nonsense mutations per gene. |
| pofkigivens_\*.txt | Estimated probability distribution of the number of nonsense mutations per gene per tumour. |
| pofmgivens_\*.txt | Estimated probability distribution of the number of missense mutations per gene. |
| pofmigivens_\*.txt | Estimated probability distribution of the number of missense mutations per gene per tumour. |
| param_estimates_\*.txt | Auxiliary file containing the estimated parameters for a given model. |
| used_params_and_model_\*.txt | Auxiliary file containing the estimated parameters of the chosen model. |

The output file q_values_\*.txt contains the following columns:
| Column index | Column name | Description |
|:---|:---|:---|
| 1 | gene | gene symbol, as provided in the input file |
| 2 | p_phi_m_neg | p-value of the negative selection signal on missense variants |
| 3 | q_phi_m_neg | q-value of the negative selection signal on missense variants |
| 4 | phi_m_neg | Meta statistic of the negative selection signal on missense variants |
| 5 | p_phi_k_neg | p-value of the negative selection signal on nonsense variants |
| 6 | q_phi_k_neg | q-value of the negative selection signal on nonsense variants |
| 7 | phi_k_neg | Meta statistic of the negative selection signal on nonsense variants |
| 8 | p_phi_neg | p-value of the negative selection signal on all nonsynonymous variants |
| 9 | q_phi_neg | q-value of the negative selection signal on all nonsynonymous variants |
| 10 | phi_neg | Meta statistic of the negative selection signal on all nonsynonymous variants |
| 11 | p_phi_m_pos | p-value of the positive selection signal on missense variants |
| 12 | q_phi_m_pos | q-value of the positive selection signal on missense variants |
| 13 | phi_m_pos_or_p(m=0\|s) | Meta statistic of the positive selection signal on missense variants |
| 14 | p_phi_k_pos | p-value of the positive selection signal on nonsense variants |
| 15 | q_phi_k_pos | q-value of the positive selection signal on nonsense variants |
| 16 | phi_k_pos_or_p(k=0\|s) | Meta statistic of the positive selection signal on nonsense variants |
| 17 | p_phi_pos | p-value of the positive selection signal on all nonsynonymous variants |
| 18 | q_phi_pos | q-value of the positive selection signal on all nonsynonymous variants |
| 19 | phi_pos_or_p(m=0\|s)*p(k=0\|s) | Meta statistic of the positive selection signal on all nonsynonymous variants |
| 20 | m_obs | Observed number of missense mutations on the gene |
| 21 | k_obs | Observed number of nonsense mutations on the gene |
| 22 | s_obs | Observed number of synonymous mutations on the gene |
| 23 | m_ell | Mutation-probability-corrected missense mutation target size |
| 24 | k_ell | Mutation-probability-corrected nonsense mutation target size |
| 25 | s_ell | Mutation-probability-corrected synonymous mutation target size |
| 26 | m_mean | Expected number of missense mutations on gene under neutral evolution |
| 27 | k_mean | Expected number of nonsense mutations on gene under neutral evolution |
| 28 | dm/ds | Ratio of observed and expected number of missense mutations |
| 29 | dk/ds | Ratio of observed and expected number of nonsense mutations |
| 30 | d(m+k)/ds | Ratio of observed and expected number of nonsynonymous mutations |

## Good to know

To sort by positive (negative) selection signal, sorting by the meta-statistic phi_pos (phi_neg) in decreasing order can break ties when several q-values take on the value zero. 

Runtime depends on model complexity and the size of the data set (for individual cancer types usually on the order of minutes, while for 10000 tumours it can take several hours).

The pentanucleotide model estimates 3072 mutation probability parameters and should therefore only be used when the number of mutations in the data set is sufficiently large (on the order of tens of thousands).

Changes relative to the legacy version [CBaSE v1.1](http://genetics.bwh.harvard.edu/cbase/downloads.html):

- Export of many new estimates (see above):
  - Magnitude of selection: dN/dS, dM/dS and dK/dS.
  - Mutation target sizes for missense, nonsense and synonymous mutations.
  - Expected number of missense and nonsense mutations under neutrality.
  - Distribution of missense and nonsense mutations per gene (per tumour) under neutrality.
  - Expected value of lambda_s|s_obs.
- Possibility to use vcf files as input, supporting both the hg19 and hg38 genome build.
- Accounting for transcription strand bias.
- Removal of pseudocounts in the pentanucleotide matrix.

If you have any questions at all, do not hesitate to write to us at <dweghorn@crg.eu>.

If you find our method useful, we would appreciate it if you could cite [our paper](https://www.nature.com/articles/ng.3987):

Weghorn, D., & Sunyaev, S. (2017). Bayesian inference of negative and positive selection in human cancers. Nature Genetics, 49(12), 1785-1788.

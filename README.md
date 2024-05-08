# CBaSE

CBaSE is a tool which derives gene-specific probabilistic estimates of the strength of negative and positive selection in cancer.

Running CBaSE

Unzip the CBaSEv1.1.zip file and run the following command within the folder:

python CBaSE_v1.1.py pathInputFile auxFolder context model dataName

INFILE  = str(sys.argv[1])		#	somatic mutation data input file (str)
VCF			= int(sys.argv[2])		#	1 = input format is vcf, 0 = input format is CBaSE v1.0 input format (int)
BUILD		= str(sys.argv[3])		#	one of [hg19, hg38] (not used when VCF==0) (str)
CMODE		= int(sys.argv[4])		#	3 = trinucleotides, 5 = pentanucleotides, 7 = heptanucleotides (int)
MODEL	  = int(sys.argv[5])		#	model choice for P(lambda): 0 = all, 1 = Gamma (G), 2 = InverseGamma (IG), 3 = Exp+G, 4 = Exp+IG, 5 = G+G, 6 = G+IG (int)
OUTNAME  = str(sys.argv[6])		#	data set name (str)

VCF==0 input format:	[1. GENE, 2. EFFECT, 3. ALT, 4. CONTEXT, 5. SAMPLE_ID (optional)]
VCF==1 input format:	[1. CHROM, 2. POS (1-based), 3. ID (not used), 4. REF, 5. ALT, ..., 10. SAMPLE_ID (optional)] or [1. CHROM, 2. POS (1-based), 3. ID (not used), 4. REF, 5. ALT, 6. SAMPLE_ID (optional)]

MODEL=0: All six possible models are fitted and model selection is done based on the Akaike information criterion.
MODEL>0: Only the model with index model of the six possible functional forms is fitted.



Command line arguments:

(1) pathInputFile: Path to file containing somatic mutation data (see below).
(2) auxFolder: Folder containing program and auxiliary files (default "Auxiliary").
(3) context: Sequence context size used to compute the cancer-type-specific mutation matrix; one of {0=trinucleotides, 1=pentanucleotides}. Note that choosing pentanucleotides is only suitable for sufficiently large mutation data sets.
(4) model: Model assumption for the distribution of expected synonymous mutation counts; one of {0,1,2,3,4,5,6}, where 0 corresponds to the default option that compares all six possible models.
(5) dataName: Name of the dataset used to identify output files (e.g. cancer type).

Input file format:
Gene symbol	Mutation effect 	Alternate allele 	Context index
ECE1 	coding-synon 	C 	26
SAMD11 	missense 	A 	53
TNFRSF4 	nonsense 	A 	52

(1) Gene symbol: corresponds to the official gene symbol as used in the UCSC knownGene track.
(2) Mutation effect: one of {“missense”, “nonsense”, “coding-synon”}, denoting missense, nonsense (stop-gain and stop-loss), and synonymous mutations, respectively.
(3) Alternate allele: the final allele after the mutation event; one of {A, C, G, T}.
(4) Context index: index of the sequence context of the reference allele; 0-based indices of tri- or pentanucleotide contexts can be found here.

Output file format:

CBaSE writes the output, including the q-values for negative and positive selection, into the file "q_values_dataName.txt", located in the folder "Output". The columns contain the following values:
gene    	gene symbol, as provided in the input file
p_phi_neg    	p-value of the negative selection signal
q_phi_neg    	q-value of the negative selection signal
phi_neg    	meta-statistic phi of negative selection
p_phi_pos    	p-value of the positive selection signal
q_phi_pos    	q-value of the positive selection signal
phi_pos    	meta-statistic phi of positive selection
m_obs    	observed number of missense mutations
k_obs    	observed number of nonsense mutations
s_obs    	observed number of synonymous mutations

To sort by positive (negative) selection signal, sorting by the meta-statistic phi_pos (phi_neg) in decreasing order can break ties when several q-values take on the value zero. 

Runtime depends on server usage, model complexity and the size of the data set.

Link to legacy version CBaSE v1.1: 

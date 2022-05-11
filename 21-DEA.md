# Differential Expression Analysis

## The Transcriptome

The transcriptome consists of various RNA species that are transcribed
from the genome. The amounts of each species depends on how fast it is
transcribed and how stable the RNA is. RNA is degraded in the cytoplasm,
so each transcript may have a different half life.

**mRNA**, or messenger RNA, contains exons of genes, and can be
transcribed into proteins. Usually, scientists are most interested in
assessing the levels of mRNA species. mRNA length depends on length of
gene.

**pre-mRNA** is the unprocessed version of mRNA. It contains introns,
which are spliced out. Further, enzymes add a 5’ cap and a 3’ poly-A
tail to protect the mRNA from degradation in the cytoplasm. Longer than
its corresponding mRNA

**rRNA**, or ribosomal RNA, makes up the ribosomal sub-units and help in
protein synthesis

**tRNA**, or transfer RNA, is able to specifically bind both RNA and
amino acids. During protein synthesis, a tRNA will recognize the 3-nt
codon sequence, and will transfer the corresponding amino acid to the
growing peptide chain.

**snRNA**, or small nuclear RNA, are components of the spliceosome that
contribute to removing introns from pre-mRNA.

**snoRNA**, or small nucleolar RNA, guide modification of other RNAs.
They often do this by methylating or pseudouridylating their target
RNAs.

**miRNA**, or microRNA, are about 22 nt and contribute to gene
regulation, mostly by binding complementary mRNAs and silencing gene
expression.

**siRNA**, or small interfering RNA, are about 21 nt double stranded RNA
molecule with overhanging 5’ and 3’ ends. Similar to miRNA, siRNAs bind
complementary mRNAs and silence their expression.

**lncRNA**, or long noncoding RNA, are RNA molecules longer than 200 nt.
They can regulate protein abundance at the transcriptional, RNA
processing, and translational level.

**asRNA**, or Antisense RNA, is a single stranded RNA that is
complementary to an mRNA molecule. asRNA can bind and inhibit
translation of the mRNA.

When performing a sequencing experiment, it is important to think about
which RNA species you want to measure. Specific protocols have been
developed to look at mRNAs, miRNAs, and even nascent transcripts.
Developments in long read sequencing have allowed us to look at
differences in isoforms and alternate splicing.

## Splicing

Each gene consists of introns and exons. The spliceosome is a collection
of enzymes responsible for recognizing the boundaries of exons and
removing introns.

Some genes have more than one functional form due to different exon
usage. Isoform A may use exons A, B and C, while isoform B uses exons B
and C only. Genetic variation and abundance of cytoplasmic enzymes can
effect the abundance of each isoform.

With traditional short read sequencing, we can only identify different
isoforms if the read fragments happen to overlap an exon-exon boundary.
Long read sequencing allows us to read entire mRNA molecules and
determine every exon present in that transcript.

## Gene Expression Quantification

Genome wide quantification of transcription is relatively new
technology. The first major development was the microarray chip, which
has quickly been replaced by short and long read RNA-sequencing.

**Micro-Arrays**- The first genome-wide transcriptional assay was the
micro-array. These were first produced by Affymetrix. Microarrays
consist of probes that hybridize with specific RNA molecules. The probes
are grouped into locations on a chip. When the target DNA binds to the
probe, fluorescent signal is released. The chips are imaged to quantify
the relative amounts of each probe.

A major limitation is that you can only measure known transcripts for
which you have a complementary probe. This makes it hard to profile both
unknown genes and unknown isoforms of genes.

**RNA-seq**- RNA-seq is currently the most widely used way to profile
the transcriptome. In next generation sequencing, reads are ligated to
adapters in a flow cell. Then each read is replicated many times to
ensure a measurable signal. Then, fluorescently labeled NTPs are added,
the RNA is replicated, and the fluorescent signal is measured after each
base pair addition. In this way, RNA-seq can measure many transcript
species.

The quality of the RNA-seq data will depend on the sequencing depth and
the coverage across the genome, as well as the quality of the input RNA
library.

## Gene Expression Analysis

Usually, we want to know which genes are turned on or off in the
presence of some treatment, as compared to the control. In this case, we
want to quantify the relative amounts of mRNA species and test whether
the amounts differ between control and treatment. This often results in
a massive gene list, so a final step is often to perform pathway
enrichment analysis using published gene sets. In addition, we will
perform quality control checks on our data and will normalize across
samples.

### Typical Workflow:

### 1. Quality Control

Microarray and RNA-seq data can contain errors and biases due to the
technology used to measure the gene expression and the input RNA
library. To identify and remove these errors, researchers perform
extensive quality control tests on these data. These quality metrics
differ for arrays and RNA-seq. Array data must be controlled for
multiple chips and for input RNA quality. RNA-seq errors are more
complex; they are often due to the amplification of certain reads or
types of reads. Much of quality control is identifying duplicated reads,
duplicated k-mers, and unexpected GC content that could be indicative of
large scale errors. Further, RNA-seq reads usually begin very high
quality, however more and more errors are introduced toward the end of
the read. Metrics showing the overall quality and quality per base pair
are important in determining where to trim reads and which to remove
entirely. For either array or RNA-seq, QC is an important step in the
gene expression analysis pipeline.

#### Tools:

**FASTQC** - A method for identifying and removing low quality reads
from raw RNA-seq data. This method performs a variety of quality control
steps to identify biases and problems due to both the sequencer and the
input library. FASTQC takes in FASTQ input files (or SAM/BAM aligned
reads). For each input file, a number of analysis modules are run. Each
module tests a specific quality metric, such as quality scores per base
and per sequence, GC content, length distribution, and k-mer enrichment.
For example, the k-mer enrichment module assumes that any 7-mer should
be found equally distributed in any position in a read. This module uses
a binomial test to determine if any 7-mer is more enriched in certain
positions. This may indicate duplication of reads. For more info on the
other modules, check out
<a href="https://www.bioinformatics.babraham.ac.uk/projects/fastqc/" class="uri">https://www.bioinformatics.babraham.ac.uk/projects/fastqc/</a>

### 2. Alignment

Reads must be aligned to genes or transcripts to quantity the amount of
gene expression. Alignment was covered in more depth in lectures 4 and
5. Briefly, aligning RNA is slightly more difficult due to the lack of
introns. Each exon must be matched separately to its unique location in
the genome. Several tools use different methods to address this problem
(listed below). Array data does not need to be aligned.

#### Tools:

**HISAT** - A method for aligning NGS reads to reference genomes. In
HISAT, the Burrows-Wheeler Transform (BWT) is used to create a
compressed version of the reference genome. Using the FM index, the
reference can be searched quickly. HISAT creates one genome-wide index,
and then breaks the genome into smaller genomic regions with local
indexing. Therefore, when reads span exon junctions, the large part can
be globally aligned, then the search space is reduced to the local
alignment to find the smaller exon. This method uses less memory
intensive than its competitors. More info can be found here:
<a href="https://www.nature.com/articles/nmeth.3317" class="uri">https://www.nature.com/articles/nmeth.3317</a>
and here:
<a href="https://www.nature.com/articles/s41587-019-0201-4" class="uri">https://www.nature.com/articles/s41587-019-0201-4</a>

**STAR** - This method aligns RNA-seq reads to a reference genome STAR
stores the reference genome in an uncompressed suffix tree. STAR then
tries to identify and map the longest continuous exon in the read. Then,
STAR separately searches for the remaining unmapped portion of the read.
STAR then stitches the two parts together into a full transcript.
Because the two searches are independent and the suffix tree structure
is quickly searchable, this method is very fast, but memory intensive.
More info here:
<a href="https://academic.oup.com/bioinformatics/article/29/1/15/272537" class="uri">https://academic.oup.com/bioinformatics/article/29/1/15/272537</a>

### 3. Estimation of Expression Levels

Once we have aligned reads to the genome, we want to estimate the amount
of mRNA is present for each gene. Essentially, we want to estimate the
mean expression value for each gene. This is complicated by the fact
that most experiments have 2-4 replicates on average, due to the high
cost. This makes estimates of variance very unreliable. To account for
this, many tools attempt to pool information across probes and across
samples to strengthen the estimations. For microarray data, we perform
normalizations before estimating expression.

#### Tools:

**dChip** - This method estimates expression values from array data by
pooling data across samples. dChip tries to estimate the expression of
each probe in each array by setting the measured expression minus
measured background equal to the relative expression level times the
affinity of the RNA for the probe. The measured background is based on
the binding of mismatch containing probes compared to perfect matches.
Then, the relative expression and affinity term are iteratively fit.
This enables accurate quantification of gene expression across arrays.
More info is hard to find unless you have the book, but check out this:
<a href="https://academic.oup.com/bioinformatics/article/20/4/500/192364" class="uri">https://academic.oup.com/bioinformatics/article/20/4/500/192364</a>

**RMA** - This method estimates expression values from array data by
pooling data across samples. RMA follows a similar framework to dChip.
However, they noticed that measured expression is usually log scale.
Therefore, they set log(measured expression) equal to log(relative
expression) + log(affinity) + a constant background term. Again, they
iteratively fit the expression and affinity terms to get a good estimate
across arrays. These changes make RMA outperform dChip. For more info:
<a href="https://pubmed.ncbi.nlm.nih.gov/12925520/" class="uri">https://pubmed.ncbi.nlm.nih.gov/12925520/</a>

**GCRMA** - This method estimates expression values from array data by
pooling data across samples. GCRMA is also based on the dChip and RMA
algorithms. Essentially, the formula is the same as dChip with a
non-constant background term. They found that the background signal
strongly depends on the probe sequence. Therefore, the background term
here is a function of both probe sequence and probe with one mismatch
binding. This correction improves its performance over RMA and dChip.
More info can be found here:
<a href="https://www.jstor.org/stable/pdf/27590474.pdf" class="uri">https://www.jstor.org/stable/pdf/27590474.pdf</a>

**Rsubread** - This tool performs alignment to a reference and estimates
counts for RNA-seq reads. First, Rsubread creates an index of the
reference genome by creating a hash table. Next, they use a two pass
strategy to align reads. First, Rsubread selects 16-mers from the read
and maps these to the reference via the hash table. By selecting
multiple 16-mers, they identify the largest location of mapping. Then,
local alignment is used to map the rest of the read. This accounts for
introns and indels. Reads are then counted if they overlap features.
Features can be genes, exons, promoters, etc. Features and degree of
overlap are user defined. More info can be found here:
<a href="https://academic.oup.com/nar/article/47/8/e47/5345150" class="uri">https://academic.oup.com/nar/article/47/8/e47/5345150</a>

**StringTie** - This method aligns RNA-seq reads and annotated
transcripts and calculated their expression levels simultaneously.
StringTie first maps reads to a reference genome using a spliced-based
aligner. An optional step is to assemble “super-reads”, or multiple
unique transcripts that map together that can be treated as a single
read. Then, StringTie groups all reads mapped to the same gene. From
these reads, a splice graph is constructed. This graph shows “paths” of
possible exon usage in the gene, and “weights” based on how many
transcripts follow that path. In this way, a relative expression value
can calculated for each isoform in the gene. More info here:
<a href="https://www.nature.com/articles/nbt.3122#Sec2" class="uri">https://www.nature.com/articles/nbt.3122#Sec2</a>

**RSEM** - A method for aligning RNA-seq reads to reference transcripts
and quantifying gene expression. RSEM does not require a reference
genome, and is able to work with reads aligned de novo. It only requires
reference transcripts, which can be input be the user from a de novo
alignment. RSEM creates a graphical model with latent and observed
variables. The observed variables are read lengths, quality scores, and
sequences and the latent variables are the transcript from which the
read was derived and positional information. The primary variable is the
probability that a read comes from a transcript. Using this model, RSEM
uses Expectation-Maximization to estimate this probability and the
probabilities of the unobserved variables until the values converge. The
output is a wiggle file. More info can be found here:
<a href="https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-12-323" class="uri">https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-12-323</a>

**Sailfish** - This method estimates transcript abundance from annotated
RNA isoforms. Sailfish is one of the first tools to implement k-mer hash
tables for pseudo-alignment. Here, they divide a reference genome into
its k-mers and store k-mer- transcript pairs in a hash table. Then, each
read is divided into k-mers and hashed against the index to determine
which transcripts it could be derived from. Then, using
expectation-maximization, transcript abundances are estimated from
average k-mer coverage and read k-mers are reassigned to transcripts.
More info here:
<a href="https://www.nature.com/articles/nbt.2862" class="uri">https://www.nature.com/articles/nbt.2862</a>

**kallisto** - This method estimates transcript abundance from RNA-seq
reads, both bulk and single-cell. kallisto uses two newer methods, de
Bruijn graphs and k-mer hash tables, to drastically improve the speed
and memory of expression quantification. Further, kallisto does not
require aligned reads, instead uses k-mers. First, de Bruijn graphs are
constructed using k-mers present in the reads. Paths through the de
Bruijn graphs represent transcripts observed in the data. Each k-mer and
its transcript compatibility, or equivalence class, are stored in a hash
table. For each read, we can hash each k-mer to see which transcripts it
could have possibly come from. Then, expectation-maximization is used to
estimate the probability that a read came from a transcript, similar to
other tools. More info can be found here:
<a href="https://www.nature.com/articles/nbt.3519" class="uri">https://www.nature.com/articles/nbt.3519</a>

**Salmon** - A method for estimating the expression of transcripts from
RNA-seq data. Salmon improves on kallisto and Sailfish by controlling
for sequence specific biases, GC content, and positional biases. Salmon
can input aligned or unaligned reads. First, salmon estimate initial
abundances for each transcript and computes the possible transcript
compatibility, or equivalence class for each read. In the second step,
expectation-maximization is used to estimate the probability that a read
came from a transcript, similar to other tools. Salmon adds GC content
and sequence information to the model to improve estimation and lower
false positives. More info here:
<a href="https://www.nature.com/articles/nmeth.4197" class="uri">https://www.nature.com/articles/nmeth.4197</a>

### 4. Normalization Across Samples

When estimating gene expression, it is important to have comparable
replicates and samples. In micro arrays, batch effects are introduced
when using more than one Chip, while in sequencing, the sequencing depth
and read length strongly influence RNA-seq quantification. An increase
in sequencing depth or gene length would lead to more reads per gene by
chance, and this should be accounted for. Further, RNA-seq is skewed by
strong differentially expressed genes, so accounting for RNA species is
important as well. Often, we present RNA-seq reads as transcripts per
kilobase per million reads (TPM), or reads per kilobase exon per million
reads (RPKM) which accounts for length and total sequencing depth.

#### Tools:

**DESeq2 Normalization** - DESeq2 normalization creates a metric similar
to TPM that accounts for sequencing depth and RNA composition. For each
gene, DESeq2 takes the geometric mean the expression across samples to
create a pseudo-reference. Then they divide each sample by the reference
and finds the median value per sample. Then, each value in the sample is
divided by this median value. This takes all genes into account when
normalizing. More info can be found here:
<a href="https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0550-8" class="uri">https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0550-8</a>

**EdgeR Normalization** - EdgeR normalizes data by computing scaling
factors for the library size used in the TPM calculation for each
sample. The scaling factors are chosen because they minimize fold change
between samples. EdgeR uses weighted trimmed mean of M-values (TMM) to
calculate the differences. In this procedure, the upper and lower parts
of the data are trimmed, then a mean is taken. More info here:
<a href="https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-3-r25" class="uri">https://genomebiology.biomedcentral.com/articles/10.1186/gb-2010-11-3-r25</a>

**Quantile Normalization** - covered in more depth in lecture 16. This
is a method that forces two data sets to share the same mean and
distribution. In brief, each data set is rank ordered, the mean of each
rank is found, then the rank means are substituted in the original data
sets in place of the value found at that rank. This tool is very good at
removing batch effects in array data. More here:
<a href="https://en.wikipedia.org/wiki/Quantile_normalization#" class="uri">https://en.wikipedia.org/wiki/Quantile_normalization#</a>:\~:text=In%20statistics%2C%20quantile%20normalization%20is,and%20sort%20the%20reference%20distribution.

### 5. Differential Expression Anlaysis

Once we have determined that are data is high quality, our reads have
been aligned to the genome/ transcriptome if needed, and data has been
normalized between samples, we often want to identify genes that change
drastically between two experimental conditions. These changes in gene
expression often lead to the observable phenotype that we want to study.
Therefore, identifying genes that change in response to treatment with a
small number of false positives is usually the goal of the researcher.

We perform differential expression analysis to determine which genes
change expression levels between groups. At its most basic, we can
simply test whether the mean expression is significantly higher in one
group vs another, perhaps using a t-test. Usually, we are interested in
genes that are both significantly differently expressed, and genes that
change expression by a large amount. We use volcano plots to show
log-fold-change and significance levels, and usually select the most
drastic in both categories.

There are a few problems with this basic method, first, their are
thousands of genes in the genome. Correcting for testing each gene is an
important step. Secondly, usually we have very few samples for each
treatment/ phenotype. The cost of sequencing is decreasing, but
experiments usually will have 2-4 replicates only. This makes estimating
variances challenging and unreliable. Further, because the n is small,
outliers will have a large effect and must be dealt with. To solve these
problems, many tools will pool information across genes to improve these
estimations of variance. Some of these tools are described below.

#### Tools:

**limma** - This tool performs differential gene expression analysis on
array and RNA-seq data. limma uses linear modeling to obtain estimates
of gene expression between samples. limma organizes the data into a
matrix of genes and samples, where each sample belongs to a treatment/
phenotype group. Then, for each gene, a linear model is made that sets
the expectation (mean value) of each gene equal to a coefficient matrix
times a design matrix that describes the experimental groups. From this
linear model, coefficients can be computed. Then, limma creates a
modified t-statistic. Using empirical Bayes methods, they adjust the
variance of each specific gene toward the pooled variance across all
genes, and they increase the degrees of freedom. In this way, limma
identifies genes that distinguish each experimental group best. More
info can be found here:
<a href="https://academic.oup.com/nar/article/43/7/e47/2414268?login=true" class="uri">https://academic.oup.com/nar/article/43/7/e47/2414268?login=true</a>

**edgeR** - edgeR is a tool for analyzing differential gene expression.
edgeR starts with a matrix of expression counts across genes and
samples. They create a model where the observed counts data is equal to
a negative binomial model that depends on the total number of reads, the
abundance/mean value for the gene in each treatment group, and a
modified variance. The variance is again modified with a dispersion
coefficient that describes the differences between samples. The
dispersion per gene and the abundance per group are first estimated
using maximum likelihood, then refined using empirical Bayes. Finally,
edgeR uses a test similar to the Fisher’s Exact test that has been
adapted for over-dispersed data. This test identifies differentially
expressed genes. More info can be found here:
<a href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2796818/pdf/btp616.pdf" class="uri">https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2796818/pdf/btp616.pdf</a>

**DESeq2** - This tool estimates differential expression from RNA-seq
data. DESeq2 starts with a matrix of expression counts across genes and
samples. Like edgeR, they create a model that sets the observed counts
data equal to a negative binomial model with a mean and dispersion term.
The variance is modeled as a mean term and a dispersion term, which
allows researchers to model both biological and technical variation. The
mean is modeled as the concentration of reads in a gene times a
normalization factor that accounts for differences in sequencing depth.
Genes that are not differentially expressed have the same normalization
factor between samples, while DE genes do not. First, DESeq2 estimates
dispersion for each gene using maximum likelihood. They then use
empirical Bayes to shrink these dispersion values towards the global
trend. Finally, they provide the maximum a posteriori estimate for each
dispersion and use this dispersion value to shrink the fold changes of
genes with high dispersion (variance). Then, the same procedure is used
to estimate log fold change. They first use a linear model to estimate
the maximum likelihood for each fold change. Then they fit a
transcriptome wide distribution and use empirical Bayes to modify the
fold changes toward the common distribution. Finally, the give a maximum
a posteriori estimate for each log-fold change. More info here:
<a href="https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0550-8" class="uri">https://genomebiology.biomedcentral.com/articles/10.1186/s13059-014-0550-8</a>

### 6. Gene Set Enrichment

Sets of differentially expressed (DE) genes, or even sets of highly
expressed genes can be long lists, often hundreds or thousands of genes.
This would be time consuming to manually search. Further, genes are
activated in programs or pathways that function together to achieve some
cellular function.

We can use databases of gene sets, pathways, interaction networks, etc
to make sense of our large gene sets. Many organizations exist to curate
and organize what is known about genes, pathways, disease, mechanisms,
etc, into distinct gene sets. Some of these databases maintain
non-overlapping gene sets, but most adhere to a hierarchical structure
of overlapping gene sets that span broad to specific categories. Other
databases not listed below include Kegg, Gene Ontology, Reactome, and
Wikipathways.

To determine whether our gene set is similar to any published gene sets,
we often look for enrichment of the published gene set in our genes
compared to background. For example, if our set of 50 DE genes contains
20 Wnt signaling genes (40%), but our background universe of genes is
only 3% Wnt signaling genes. We can use the Fisher’s Exact Test, a
hypergeometric test, or a leading edge test to determine if there are
significantly more Wnt related genes in our DE set.

#### Tools and Databases:

**MSigDB** - The molecular signatures database is a database of almost
40,000 annotated gene sets. The database contains gene sets such as
“Hallmark Apoptosis” or “Adipogenesis at 8HR”. Each gene added to the
set has been published and verified to be involved with the gene set
term. Using MSigDB, researchers can learn about a particular gene,
identify genes involved with certain processes, or test an input gene
set for enrichment for any of the MSigDB gene sets. GSEA can be
performed on the MSigDB gene sets. More info here, but requires login:
<a href="https://www.gsea-msigdb.org/gsea/msigdb/index.jsp" class="uri">https://www.gsea-msigdb.org/gsea/msigdb/index.jsp</a>

**GSEA** - Gene set Enrichment Analysis (GSEA) is a tool for
interpreting gene expression data. First, samples are divided into
control vs treatment, or any two phenotypes. Then, the genes are ranked
by their correlation to the treatment. Then, using this ranked gene
list, GSEA determines whether genes from a curated gene set are found at
the top/bottom of the ranked list or randomly distributed. This is
called the leading edge test. They compute an enrichment score by
walking through the data and increasing the running sum when genes from
the curated set are encountered. Using a permutation test, they
determine how often the enrichment score happens randomly. Finally, they
correct for multiple tests. More info can be found here:
<a href="https://www.pnas.org/doi/10.1073/pnas.0506580102" class="uri">https://www.pnas.org/doi/10.1073/pnas.0506580102</a>

**String** - String is a database of protein-protein interactions. They
curate both functional and direct protein interactions from published
data, functional studies, genomic prediction, and co-expression. You can
query individual proteins to see their interaction network. Further, you
can input a set of genes/ proteins and test for enrichment of published
gene sets and String interaction modules. They use a hypergeometric test
to determine over-representation of the String modules in the input gene
set (expected vs observed). More info here:
<a href="https://academic.oup.com/nar/article/47/D1/D607/5198476?login=true" class="uri">https://academic.oup.com/nar/article/47/D1/D607/5198476?login=true</a>
and here:
<a href="https://string-db.org/cgi/about?footer_active_subpage=content" class="uri">https://string-db.org/cgi/about?footer_active_subpage=content</a>

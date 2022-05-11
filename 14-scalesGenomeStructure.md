# Epigenomic domains, Hierarchy and scales of genome structure

## Epigenomics and chromatin structure

- Interest in epigenomic factors that interact with genetic DNA.
- DNA is wrapped around histone octamers (2 histones of: H2A, H2B, H3, H4) called **nucleosomes** (core particles)
- Each nucleosome is wrapped by 147 bp of DNA.
- Histone tails have residues that can be modified (chemical modification/variants)
- Covalent modifications on histone tails include: methylation (me), acetylation (ac), phosphorylation, etc.
- Histone modifications play an important role in gene transcription since they can define areas of repression/activation of genes.

## Functional annotation of common histone marks

| Functional Annotation        | Histone Marks    |
| ---------------------------- | ---------------- |
| Promoters                    | H3K4me3          |
| Bivalent/Poised Promoter     | H3K4me3/H3K27me3 |
| Transcribed gene body        | H3K36me3         |
| Enhancer (active and poised) | H3K4me1          |
| Active Enhancer              | H3K4me1/H3K27ac  |
| Polycomb Repressed Regions   | H3K27me3         |
| Heterochromatin              | H3K9me3          |

Extra notes:

- Bivalent domains are mainly seen in embryonic stem cells. These are primed with H3K4me3 an activating mark for genes. This allows them to differentiate into their specific cell types. H3K27me3 is also present to repress transcription as needed.
- H3K36me3 is mainly found in exons which suggests that splicing functions are also coded in the epigenome

## Comparing transcription factors and histone marks

**Cell Type Specificity:**

- Transcription Factors: Cell type specific. They also have their own profile where they can bind in DNA.
- Histone marks: They only have a profile. They're not cell type specific. Most of them can be found in all cell types. (with exceptions)

**Signal width**

- Transcription Factors: Narrow (signals are in 'peaks')
- Histone marks: Marks can be narrow (e.g. H3K4me3) or broad (e.g. H3K9me3)

**Chromatin accessibility**

- Transcription Factors: They need chromatin to be open to be able to bind to their specific sequence motif in DNA
- Histone marks: Can be found in both open/closed chromatin

**DNA sequence motif**

- Transcription Factors: Have a specific sequence motif
- Histone Marks: They don't have sequence specificity they look for their histone tail residues

**Resolution**

- Transcription Factors: 1-10 bp (we can accurately determine their position in the genome)
- Histone marks: 200 bp. This is based on the nucleosome that is composed of 147 bp and linker DNa that is ~50 bp

## Histone modification patterns

- These patterns tend to be diffue, noisy, hard to see, enriched in spread-out regions and lack saturation
- These patterns tend to be broad because once a nucleosome is modified it's a chain reaction to proximal nucleosomes. If a complex is attracted to one nucleosome (nucleation) it will start binding the adjacent nucleosomes creating chromatin regions of open/closed marks (propagation). (e.g. HP1 and H3K9me3, PRC1/PRC2 and H3K27me3)

## Chip-seq analysis (continued)

**Chip-seq pipeline**

<p float="left">
  <img src="./chipseq.png" width=550/>

</p>

**Data preprocessing comparison**

|                              | MACS                                                      | SICER                                                                                                                 |
| ---------------------------- | --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| DNA fragment size estimation | Peak model (optimal for TFs)                              | Cross-correlation (optimal for histone modifications)                                                                 |
| DNA fragment retrieval       | Full length (extend d) (fragment length)                  | Point position (shift d/2) Actual center location of fragment                                                         |
| Signal profile generation    | Fragment pile up (it's easy because TF are very enriched) | Read count in bins (Bin the entire genome with no overlaps. Each bin is 200 bp=nucleosome. Reads are counted per bin) |

**Detecting the signal**

|                            | MACS                                          | SICER                                                                             |
| -------------------------- | --------------------------------------------- | --------------------------------------------------------------------------------- |
| Initial model              | Poisson                                       | Poisson                                                                           |
| Signal scan                | Sliding windows with bandwidth                | Non-overlapping bin read count                                                    |
| Peak region identification | Merge windows                                 | Merge windows allowing gaps                                                       |
| Peak scoring               | Pile-up signal amount (#reads\*fragment size) | Aggregate score on islands                                                        |
| Significance modeling      | Poisson with dynamic $\lambda$                        | Asymptotic estimation of island score statistics model, then compare with control |
| Additional information     | Read count, Pile-up height,Summit position    | Read count, peak score, E-value                                                   |

**Extra Notes:**

- Poisson distribution is selected to separate the real signal from the random background. It takes into account the randomness of looking at 10's millions of bins in the genome and determine the signal from background. _Null hypothesis_: All reads are randomly distributed in the genome. From this distribution a cut-off is determined to differentiate between noise and statistically significant signal.

## SICER

**ISLANDS**

- To determine the islands a Poisson distribution model is selected with very linient parameters. This is a first pre-selection spet which doesn't need to be as stringent. It'll only give some statistical significance to sart differentiating signal from noise.
- There are eligible and ineligible windows.
- Eligible windows are separated by _gaps_ of ineligible windows
- Islands are defined as the clusters of eligible windows that are separated by gaps of size at most _g_ windows.

**Scoring Islands**

- This is based on the probability of finding the observed tag count in a random background
- Having m reads in a window the probability of findinf reads in Poisson (m,$\lambda$) is the average number of reads in each window
- Eligible windows are scored with the function: S=-lnP(m,$\lambda$)
- This score will aggregate all the eligible windows in the island and corresponds to the background probability of finding the observed pattern

_Scales of histone mark islands and chromatin domains:_

- Narrow: a few nucleosomes ,0.5kb~5kb, e.g. H3Kme4
- Broad: 5kb~100kb, Gene Loci, chromatin domains, super-enhancers, e.g. H3K4me1, H3K27ac, H3K36me3, H3K27me3, etc.
- Very Broad: > 100kb, Large chromatin domains, chromatin compartments, e.g. H3K9me3, H3K27me3

**SICER Statistics**

- The statistics of SICER heavily rely on recurssion methods.
- To find an island there has to be a gap on both sides of the extremes.
- It follows and asymptotic distribution in the background, modeled by an exponential distribution.
- Advantage: Since it's not looking for peaks but islands then it's able to capture broad areas that are enriched. Since various nucleosomes that are close together are usually enriched with the same histone marks then these islands can be 5-100kb in size. (Gene loci, chromatin domain, super-enhancers). This makes this method **SCALE FREE**.

**Other approaches for chromatin domains**

- ChromHMM: Hidden Markov Models (Ernst & Kellis) -> It relies on a chromatin region state and the transition between states. Limitation: not scale free which doesn't account for different sized regions/islands
- Recognicer: Coarse-graining (Zang, et al. 2020) -> Includes scale free analysis. It does block transformation under a majority rule, this is recursive and then there's a trace back to identify candidate enriched regions.
- Scale free chromatin domains follow the power law (This can be seen when plotting the frequency of islands to width (bp) of islands)

## Hi-C

Experiment Pipeline:

<p float="left">
  <img src="./hic2.png" width=500/>
  <img src="./hicexp.png" width=550/>
</p>

Processing Pipeline:

<p float="left">
  <img src="./hic.png" width=550/>

</p>

Analysis:

- Using a PCA Analysis the first 2 components are selected to separate chromatin into two big compartments: open and closed chromatin.
- Topologically Associating Domains (TADs): These are domains with high probability of interactions.
- 2-D map depicting the density or number of reads between each part of the chromatin

Genome wide contact maps are created to visualize the results. It is expected to have high enrichment in the diagonal because there's interactions between close nucleosomes.

<p float="left">
  <img src="./hicresult.png" width=550/>

</p>

Some interpretations of Hi-C results:

<p float="left">
  <img src="./hicpattern.png" width=550/>

</p>

**Hi-C Follows the power-law property**

- From 500kb to a few megabites contact probability observed follows a linear model with m=-1. The theoretical calculation gives an m=-1.5. This suggests that intrachromosomal contact probability follows a fractal structure.
- It is proposed that chromatin is organized like spaghetti. The only difference is that chromatin does not have knots. This is because all areas have to have the same probability of interacting with its surrounding. Also, when chromosomes are going to split in mitosis chromatin has to be able to separate and not be knotted.
- The fractal structure compared to the equilibrium allows for clear organization of chromatin domains and going down at the sub scale this way of organization is also maintained.

<p float="left">
  <img src="./fractals.png" width=550/>
    <img src="./fracequi.png" width=400/>
</p>

- A better proposed model is that chromatin is like an instant noodle:

<p float="left">
  <img src="./noodle.jpeg" width=550/>
</p>

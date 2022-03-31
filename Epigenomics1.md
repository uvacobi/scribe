# Epigenomics 01
## Epigenome
Different cells with similar genome sequences have different genes expression. The epigenome can control gene activities to decide which genes are turned on or off.

*The epigenome is a multitude of chemical compounds that can tell the genome what to do. The epigenome is made up of chemical compounds and proteins that can attach to DNA and direct such actions as turning genes on or off, controlling the production of proteins in particular cells.          <p align="right">--from genome.gov</p>*

## Epigenomic marks
| |Chemical compounds |Proteins |Other molecules |Other information|
|---|---|---|---|---|
|DNA-associated|DNA methylation|Histones;<br> DNA-binding proteins<br> (transcription factors*)|RNA<br>(e.g., R loops)<td rowspan=2> -nucleosome positioning;<br> - chromatin accessibility;<br> - 3D genome organization</td>|
|Chromatin-associated|Histone modifications: methylations, acetylations, ... |Histone variants;<br> Chromatin regulators;<br> Histone modifying enzymes:writer, readers, erasers;<br> Chromatin remodeling complexes|Non-coding RNAs|

## Epigenomics
### Transcription factors
- The transcription factors (TF) is one of the most important group of proteins which can directly interact with DNA. In this case, the DNA region should be open/accessible to proteins.
- The transcription factors should have DNA-binding domains which are used to recognize specific DNA sequences and sites. They also have effector domains which can regulate TF activity, such as ligand binding domains, can mediate protein-protein interactions, such as BTB domain, and can have enzymatic activities, such as SET domain.
- There are some functional studies related to transcription factors, such as studying for the cell-type specific gene expression, binding DNA sequence motif, genome-wide binding sites, target genes, TF co-factors, etc. 

### Sequence motif
In general, a motif is a distinctive pattern that occurs repeatedly. In biomelecular studies, a sequence motif is a pattern common to a set of DNA, RNA, or protein sequences that share a common biological property, such as functioning as binding sites for a particular protein.
#### Sequence motif finding
For motif finding, the input data is a set of DNA sequences and the output is enriched sequence patterns (motifs).
 - Motif representation
  
  Single-letter and ambiguity codes for nucleotides (Table)

|Symbol|Meaning|Origin of designation|
|---|---|---|
|G|G|***G***uanine|
|A|A|***A***denine|
|T|T|***T***hymine|
|C|C|***C***ytosine|
|R|G or A|pu***R***ine|
|Y|T or C|p***Y***rimidine|
|M|A or C|a***M***ino|
|K|G or T|***K***eto|
|S|G or C|***S***trong interaction (3H bonds)|
|W|A or T|***W***eak interaction (2H bonds)|
|H|A or C or T|not-G, H follows G in the alphabet|
|B|G or T or C|not-A, B follows A|
|V|G or C or A|not-T (not-U), V follows U|
|D|G or A or T|not-C, D follows C|
|N|G or A or T or C|a***N***y|
- Entropy

    <script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
    Entropy is used to measure the orderliness. Boltzmann entropy: \\S_B=


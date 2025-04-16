---
marp: true
author: Richard Morris
title: Dividing and conquering sequence alignment using Braids
math: katex
size: 16:9
headingDivider: 1
style: |
  h1 { /* Slide title */
    position: absolute; top: 20px; left: 20px; right: 75px; }
  section::after{ /* slide # */
    content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total);}
  section{ /* slide class */
      background-color: #ffffff; font-size: 2em; padding: 1rem; font-family:'Segoe UI';}
  .bottom_align_contents{
      display:flex; align-items:flex-end}
  .two_columns { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 1rem; }
  .three_columns {display: grid;grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 1rem; }
---
## BIOL8701: Dividing and conquering sequence alignment using braided De Bruijn Graphs
<!-- paginate: skip -->
<!-- _class: coverpage -->

![](images/debruijngraph.drawio.svg)
- Student: Richard Morris
- Huttley lab, Australian National University
- Supervisors: Gavin Huttley 

# Where does alignment fit in genomics?
<!-- paginate: true -->
<!-- paginate: hold -->

![bg fit 55%](images/paddy_cow_sample.drawio.svg)
<!-- _footer: "Original artwork motivated by chatGPT 4o"-->

# Where does alignment fit in genomics?
![bg fit 55%](images/paddy_cow_amplification.drawio.svg)


# Where does alignment fit in genomics?
![bg fit 55%](images/paddy_cow_assembly.drawio.svg)


# Where does alignment fit in genomics?
![bg fit 55%](images/paddy_cow_alignment.drawio.svg)

# Algorithm: Needleman-Wunsch$^1$ pairwise alignment
<!-- paginate: true -->

![fit](images/needleman_wunsch_0.drawio.svg)
<!-- _footer: "<sup>1</sup>[Needleman & Wunsch, 1970  doi.org/10.1016/0022-2836(70)90057-4](https://doi.org/10.1016/0022-2836(70)90057-4)" -->

# Algorithm: Needleman-Wunsch pairwise alignment
<!-- paginate: hold -->

![fit](images/needleman_wunsch_1.drawio.svg)

# Algorithm: Needleman-Wunsch pairwise alignment
<!-- paginate: hold -->

![fit](images/needleman_wunsch_2.drawio.svg)

# Algorithm: Smith Waterman$^2$ local alignment
<!-- paginate: true -->

![fit](images/smith_waterman.drawio.svg)

<!-- _footer: "<sup>2</sup>[Smith & Waterman, 1981  10.1016/0022-2836(81)90087-5](https://doi.org/10.1016/0022-2836(81)90087-5)" -->

# Multiple sequence alignment
"Multiple sequence alignment is just pairwise alignment, repeated many times".
<br/>
<br/>

Progressive multisequence alignment: $O(nL^2)$ 
  - $L$ = length of sequence
  - $n$ = number of sequences

# Algorithm: de Bruijn$^3$ graphs
<!-- paginate: true -->

![bg fit 80%](images/debruijngraph_example.drawio.svg)
<!-- _footer: "<sup>3</sup> de Bruijn (1946) -->

# Algorithm: de Bruijn graphs
<!-- paginate: hold -->

![bg fit 80%](images/debruijngraph_example_1.drawio.svg)

# Algorithm: de Bruijn graphs
<!-- _paginate: true -->

![fit](images/debruijngraph_parts_0.drawio.svg)

# Algorithm: de Bruijn graphs
<!-- paginate: hold -->

![fit](images/debruijngraph_parts_1.drawio.svg)

# Algorithm: de Bruijn graphs
<!-- _paginate: hold -->

![fit](images/debruijngraph_parts_2.drawio.svg)
<!-- _footer: "<sup>2</sup>[Karlin & Altschul, 1990  doi.org/10.1073/pnas.87.6.2264](https://doi.org/10.1073/pnas.87.6.2264)" -->

# Algorithm: Karlin Altschul$^4$ test
<!-- _paginate: true -->
The Karlin–Altschul test is used to assess the significance of local sequence alignments.  

Models the distribution of maximum alignment scores between two random sequences using a Gumbel (extreme value) distribution

$$E = K.m.n.e^{-\lambda S}$$

 - $E$: expected number of alignments with score $\geq$ S
 - $K$: scale & $\lambda$: decay (dependent on scoring scheme)
 - $S$: alignment score
 - $m$: length of sequence 1 & $n$: length of sequence 2

<!-- _footer: "<sup>4</sup>[Karlin & Altschul, 1990  doi.org/10.1073/pnas.87.6.2264](https://doi.org/10.1073/pnas.87.6.2264)" -->

# Algorithm: Karlin Altschul test **for biologists**
<!-- _paginate: hold -->

Whether a local alignment, containing substitutions, meets a threshold (p-value) for equality.

ie: If the local alignment `CAC|A|GTAC` & `CAC|T|GTAC` meets the threshold, it is a **qualified braid**.

![fit](images/debruijngraph_parts_2b.drawio.svg)

# Base data and methods

- Mammal genomes obtained from **Ensembl**: release 113 (current release)
- **Queried**: using `ensembl-tui`application
- **Extracted**: 1597 one-to-one orthologs to human chromosome 1 protein coding genes 
- **Alignment**
  - cogent3 (Smith-Waterman, Needleman-Wunsch)
  - madb (de Bruijn graph)

# Hypothesis 1
<!-- paginate: true -->

<br/>

The longest braid in a pair of sequences corresponds to the ungapped Smith-Waterman local alignment.* 

<br/>

![bg right fit 90%](images/experiment1.drawio.svg)

<!-- _footer: "* a gap not within $k$ characters of the start or end of a bubble ... "-->

# Background for Hypothesis 1

*A braid has no gaps, this corresponds to an ungapped local alignment*

<br/>

- the Smith-Waterman algorithm is an optimal solution for finding the highest scoring local alignment
- the longest braid in a de Bruijn graph should equal the Smith-Waterman alignment

# Results - longest braid vs ungapped Smith-Waterman

![bg fit](images/braid_vs_sw.png)

# Summary for hypothesis 1

### Smith-Waterman 

produces **just one** best local alignment in quadratic time $O(L^2)$

### de Bruijin graph 

produces **every** local alignment in linear time $O(L)$

![bg 80% right](images/blank.drawio.svg)

# Summary for hypothesis 1
<!-- paginate: hold -->

### Smith-Waterman 

produces **just one** best local alignment in quadratic time $O(L^2)$

### Bruijin graph 

produces **every** local alignment in linear time $O(L)$

![bg 80% right](images/linear_vs_quadratic.png)


# Hypothesis 2
<!-- paginate: true -->

Differences in braids, will be a better approximation to the actual *genetic distance* than *Jaccard*.


![bg right fit 90%](images/experiment2.drawio.svg)

# Background for Hypothesis 2

- Genetic distance is proportional to the time since two sequences diverged from a common ancestor
- Influences the probability that two nucleotides are a "match" (descended from a common ancestor)
- Required to construct reliable scoring functions for alignment methods

# Term: *Genetic distance*

### Consider the proportion different (PD) between 2 *aligned* sequences.

$$PD=\frac{substitutions}{positions}$$

![alt text](image-2.png)

$$PD_{human,mouse}=\frac{5}{50} = 0.1$$
$$PD_{human,wombat}=\frac{13}{50} = 0.26$$

# Term: *Jaccard distance*
<!-- paginate: true -->

- **Jaccard** distance is a measure of dissimilarity between two sets, defined as the size of the intersection divided by the size of the union of the sets.

- Jaccard can be calculated for *unaligned* sequences, by decomposing them into $k$-mers and comparing the ratio of common $k$-mers to total $k$-mers

$$Jaccard = \frac{I}{U}$$ 

# Key insight

By qualifying braid sequences per the *Karlin-Altshul* test, we are able to observe the proportion of nucleotides different in local alignments.  

This offers a measure of genetic distance between "unaligned" sequences.
> Thanks to Eric Stone for this valuable insight

# Result 2 Jaccard distance vs PD
 
![bg 65% fit](images/PD_vs_jaccard_LOWESS_fit.png)

# Term: *Stone metric (SM)*

![alt text](image-2.png)

The PD of all local alignments of the sequences will approach the PD of fully aligned sequences. 

$$SM =  \frac{sum\ of\ Braid\ differences}{sum\ of\ Braid\ lengths}$$ 


# Result 2 Stone braid metric

![bg 60% fit](images/HMP_ENSG00000143774_madb_vs_pd_LOWESS.png)

# Summary for hypothesis 2 

- Jaccard distance is a poor approximation of PD as it is noisy, and context dependent on the sequence being aligned 
- The Stone metric has been affected by a bug we have in resolving cycles

# Hypothesis 3
<!-- paginate: true -->

We predict that the de Brujin graph aligner will perform (statistically and computationally) better with less diverged sequences.

<br/>

![bg right fit 90%](images/experiment3.drawio.svg)

# Background for Hypothesis 3

⬆genetic distance ⇒ ⬆bubbles ⇒ ⬇braids ⇒ ⬆work 

![alt text](image-4.png)

Key insights: 
- for a given sequence the number of cycles will decrease as you increase $k$
- our ability to find braids will improve as we decrease $k$

# Result - TBD 

### MADB accuracy and performance by divergence

###### Human to Chimpanzee orthologous gene alignment
| quartile | mean alignment score (%) | mean performance (s) |
|---|---|---|
| 1st|TBD|TBD|
| 4th|TBD|TBD|

###### Human to Mouse orthologous gene alignment
| quartile | mean alignment score (%) | mean performance (s) |
|---|---|---|
| 1st|TBD|TBD|
| 4th|TBD|TBD|


# Summary for hypothesis 3

- the algorithm we are using to resolve cycles needs to be made more robust to sequence divergence
- next step is sampling another primate lineage more diverged from humans than chimpanzee such as marmoset

# Hypothesis 4
<!-- paginate: hold -->

<br/>

de Bruijn graph alignment will be more computationally efficient than Needleman-Wunsch.

<br/>

![bg right fit 90%](images/experiment4.drawio.svg)

# Results

![bg 70% fit](images/cogent3_vs_madb_alignment.png)

# Summary for hypothesis 4

- de Bruijn graph alignment is more computationally efficient than Needleman-Wunsch because it reduces costly computation 

# Thanks

<style scoped>
ul {font-size: 1.5em; }
</style>

- Gavin Huttley
- Yu Lin
- Xinjian Leng

## ... and the Huttleylab

<img src="images/teadance.gif" style="margin: 10px; width: 30%; height: auto;" />
</div>

# Questions & Answers

# Citations
<!-- paginate: False -->
1. [Needleman & Wunsch(1970), 'A general method applicable to the search for similarities in the amino acid sequence of two proteins'  doi.org/10.1016/0022-2836(70)90057-4, 2010](https://doi.org/10.1016/0022-2836(70)90057-4)
1. [Smith & Waterman(1981), 'Identification of Common Molecular Subsequences.'  doi.org/10.1016/0022-2836(81)90087-5](https://doi.org/10.1016/0022-2836(81)90087-5)
1. de Bruijn(1946) 'A Combinatorial Problem.' Koninklijke Nederlandse Akademie van Wetenschappen, Proceedings 49 (1946): 758–64.
1. [Karlin & Altschul(1990), 'Methods for assessing the statistical significance of molecular sequence features by using general scoring schemes'  doi.org/10.1073/pnas.87.6.2264](https://doi.org/10.1073/pnas.87.6.2264)


# Addendum - Removing cycles

![fit 70% ](images/DBG_cyclic_to_acyclic.drawio.svg)


---
marp: true
author: First Last
title: Project title
math: katex
size: 16:9
headingDivider: 1
style: |
  h1 { /* Slide title */
    position: absolute; top: 10px; left: 10px; right: 75px; }
  section::after{ /* slide # */
    content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total);}
  section{ /* slide class */
      background-color: #ffffff; font-size: 2em; padding: 1rem; font-family:'Segoe UI';}
  .bottom_align_contents{
      display:flex; align-items:flex-end}
  .two_columns { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 1rem; }
  .three_columns {display: grid;grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 1rem; }
---
# Dividing and conquering sequence alignment using braided De Bruijn Graphs  
<!-- paginate: skip -->

![](images/debruijngraph.drawio.svg)
- Student: First Last
- Huttley lab, Australian National University
- Supervisors: Gavin Huttley 

<style scoped> /*ANU logo*/ { background-image: url('./images/ANU_logo.png');background-repeat: no-repeat; background-size: 30%;background-position: right 10px bottom 10px;} </style>

# REVIEW: Elements of a De Brujin graphs
<!-- paginate: true -->

![height:13cm](images/examiner_meeting_Cell0.png)
![height:13cm](images/examiner_meeting_Cell1.png)

# Project aims

1. Construct **de Bruijn graph** from sequences
2. Identify **bubbles** in the graph
3. Extend **braids** using _Karlin and Altschul_$_1$ statistics
4. Compare longest **braids** with ungapped _Smith-Waterman_$_2$
5. Contrast performance

<!-- _footer: "<sup>1</sup>[Karlin & Altschul, 1990  doi.org/10.1073/pnas.87.6.2264](https://doi.org/10.1073/pnas.87.6.2264) <br/> <sup>2</sup>[Smith & Waterman, 1981  doi.org/10.1016/0022-2836(81)90087-5](10.1016/0022-2836(81)90087-5)"-->

![80% bg right fit](images/progress.drawio.png) 

# Construct de Bruijn graph from sequences


**Why**: Project multiple unaligned sequences into de Bruijn graph space
**How**: Development of a Python library with unit tests and sample data

![](images/examiner_meeting_Cell0.png)

# Identify **bubbles** and **braids** 

**Why**: Determine the regions of the graph where sequences differ
**How**: identify the beginnings of a divergence in the graph, and follow any sequence to the next node containing all the sequences that diverged

![](images/examiner_meeting_Cell1.png)

# bubble and braid identification 

## Bubbles
1. Identify each node that branches to more than one node as a **bubble start**
2. Note the set of sequences and pick any
3. Follow the sequence to find the first node with the same set of sequences as a **bubble end**
4. remove the first $kmer-1$ nodes from the bubble start
## Braids
1. Everything else is a braid

<div class="mermaid">
    graph LR;
        s(start);
        e(end);
        s --> ACG;
        s --> ACG;
        ACG(<span style='color: red'>A</span>CG) --> CGA(<span style='color: red'>C</span>GA);
        ACG(<span style='color: red'>A</span>CG) --> CGA(<span style='color: red'>C</span>GA);
        CGA(<span style='color: red'>C</span>GA) --> GAC(<span style='color: red'>G</span>AC);
        CGA(<span style='color: red'>C</span>GA) --> GAC(<span style='color: red'>G</span>AC);
        GAC(<span style='color: red'>G</span>AC) --> ACC(<span style='color: red'>A</span>CC);
        GAC(<span style='color: red'>G</span>AC) --> ACT(<span style='color: red'>A</span>CT);
        ACC(<span style='color: red'>A</span>CC) --> CCG(<span style='color: red'>C</span>CG);
        ACT(<span style='color: red'>A</span>CT) --> CTG(<span style='color: red'>C</span>TG);
        CCG(<span style='color: red'>C</span>CG) --> CGC(<span style='color: red'>C</span>GC);
        CTG(<span style='color: red'>C</span>TG) --> TGC(<span style='color: red'>T</span>GC);
        CGC(<span style='color: red'>C</span>GC) --> GCA(<span style='color: red'>G</span>CA);
        TGC(<span style='color: red'>T</span>GC) --> GCA(<span style='color: red'>G</span>CA);
        GCA(<span style='color: red'>G</span>CA) --> CAT(<span style='color: red'>C</span>AT);
        GCA(<span style='color: red'>G</span>CA) --> CAT(<span style='color: red'>C</span>AT);
        CAT(<span style='color: red'>CAT</span>) --> e;
</div>

# Extend **braids** using _Karlin and Altschul_$_1$ statistics

**Why**: Construct braids from non bubble segments, or segments where the bubbles have equal sides and the changes are not significant per the Karlin statistic
**How**: Use the start of any bubble with equal length sides to identify a braid end, and the end of the previous bubble that is not equal length to identify a braid start if bubbles withing the braid are equal length then apply the karlin statistic to determine if the bubble should be removed from the list of bubbles

![](images/examiner_meeting_Cell2.png)

<!-- _footer: "<sup>1</sup>[Karlin & Altschul, 1990  doi.org/10.1073/pnas.87.6.2264](https://doi.org/10.1073/pnas.87.6.2264)"-->

# compare longest **braids** with ungapped _Smith-Waterman_$_1$

**Why**: An ungapped Smith-Waterman function should return the longest local alignment in any pair of sequences, this should be the same as the longest braid in a de Bruijn graph

**Bonus**: we find all braids at the same time

<!-- _footer: "<sup>1</sup>[Smith & Waterman, 1981  doi.org/10.1016/0022-2836(81)90087-5](10.1016/0022-2836(81)90087-5)"-->

# contrast performance

**Why**: I can compare the results of the de Bruijn graph finding all local alignments with Smith-Waterman finding the optimal(longest) to see if the de Bruijn graph method is more efficient
**How**: TBD
**Result**: TBD
**Conclusion**: TBD

    
# Thanks

- Gavin Huttley

## ... and the Huttleylab

<img src="images/teadance.gif" style="margin: 10px; width: 30%; height: auto;" />
</div>

# Questions & Answers

# Citations
<!-- paginate: False -->
- [Needleman & Wunsch (1970), 'A general method applicable to the search for similarities in the amino acid sequence of two proteins'  doi.org/10.1016/0022-2836(70)90057-4, 2010](https://doi.org/10.1016/0022-2836(70)90057-4)



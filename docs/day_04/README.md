# Day 04 — Phylogenetics, IQ-TREE, cgMLST

## Overview

Day 04 focuses on two complementary approaches for comparing bacterial genomes in genomic surveillance:

1. **Core genome multilocus sequence typing (cgMLST) with chewBBACA**
2. **Phylogenetic tree inference with IQ-TREE**

You will learn how assembled genomes can be compared using both sequence-based and allele-based methods. IQ-TREE is used to infer maximum-likelihood phylogenetic trees from sequence alignments, while chewBBACA is used to perform gene-by-gene allele calling and generate cgMLST profiles from genome assemblies.

## 🎯 Learning goals

By the end of Day 04, you should be able to:

- explain why phylogenetics is useful in pathogen genomic surveillance
- distinguish between isolates, genome assemblies, loci, alleles, alignments, cgMLST profiles, and phylogenetic trees
- explain the difference between alignment-based phylogenetics and allele-based cgMLST analysis
- understand common phylogenetic tree terminology, including tips, nodes, branches, branch lengths, and support values
- prepare or identify a sequence alignment suitable for tree inference
- run `IQ-TREE` on a sequence alignment
- understand the purpose of substitution model selection using ModelFinder
- interpret common IQ-TREE output files, including `.treefile`, `.iqtree`, `.log`, and bootstrap/support values
- explain what a cgMLST schema is
- understand the difference between wgMLST and cgMLST
- run or interpret allele calling results produced by `chewBBACA`
- generate or interpret a cgMLST allele profile matrix
- understand how chewBBACA results can be used for clustering, outbreak investigation, and downstream tree-building
- compare the roles of IQ-TREE and chewBBACA in a bacterial genomic surveillance workflow

## 💻 Practicals

1. [cgMLST with chewBBACA](01_chewbbaca.md)
2. [Phylogenetic Tree Inference with IQ-TREE](02_iqtree.md)

## 👩‍🏫 Slides

- [Introduction to Molecular Phylogenetics](https://docs.google.com/presentation/d/1V85Ccei1zCsNBsOFiWi6TtxxlXaJ6czeIyqU4IouNg0)

## 📝 Evaluation

Please fill in this [daily feedback evaluation](https://survey.lamapoll.de/HSPA-Tunisia-Bioinformatics-Workshop-Daily-Feedback) at the end of the day.

---

[⬅ Back to main page](../README.md)

---

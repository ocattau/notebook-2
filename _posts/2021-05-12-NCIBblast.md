---
layout: post
title: blast locally 
date: '2021-05-12'
categories: blast
tags: test
tags: hemat_transciptome
---

# 2021 May 12th 

Today I finished blasting localy using NCIB blast and Jupyter notebooks


```
!{bldir}blastx \
-query ../oliviacattau/blasts/hemat_transcriptome_v1.5.fasta \
-db ../oliviacattau/blasts/uniprot_sprot_2021_02  \
-out ../oliviacattau/blasts/analysis/hemat_transcriptome-uniprot_blastx.tab \
-evalue 1E-20 \
-num_threads 4 \
-max_target_seqs 1 \
-outfmt 6
```

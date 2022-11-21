---
layout: post
title: Kallisto Index for P. generosa
date: '2021-08-27'
categories: index
tags: test
tags: transcriptome, p. generosa, index, kallisto
---
# using Roadrunner for bulding a Kallisto index

```{bash}
srlab@roadrunner.fish.washington.edu

srlab@roadrunner:~/anaconda2$ curl -O https://github.com/trinityrnaseq/trinityrnaseq/releases/download/v2.11.0/trinityrnaseq-v2.11.0.FULL.tar.gz --insecure -O https://gannet.fish.washington.edu/Atumefaciens/20191105_swoose_pgen_v074_renaming/Panopea-generosa-v1.0.fa

srlab@roadrunner:~/anaconda2$ kallisto

srlab@roadrunner:~/anaconda2$ !curl --insecure \
curl -O https://github.com/trinityrnaseq/trinityrnaseq/releases/download/v2.11.0/trinityrnaseq-v2.11.0.FULL.tar.gz --insecure -O https://gannet.fish.washington.edu/Atumefaciens/20191105_swoose_pgen_v074_renaming/Panopea-generosa-v1.0.fa  --insecure \
> -O https://owl.fish.washington.edu/halfshell/genomic-databank/Pgenerosa_transcriptome_v5.fasta

srlab@roadrunner:~/anaconda2$ mv Pgenerosa_transcriptome_v5.fasta ~/anaconda2/bin/Pgenerosa_transcriptome_v5.fasta

srlab@roadrunner:~/anaconda2$ mv Panopea-generosa-v1.0.fa ~/anaconda2/bin/Panopea-generosa-v1.0.fa

srlab@roadrunner:~/anaconda2/bin$ kallisto \index -i ../transcriptome_v5.idx Pgenerosa_transcriptome_v5.fasta
[build] loading fasta file Pgenerosa_transcriptome_v5.fasta
[build] k-mer length: 31
[build] warning: clipped off poly-A tail (longer than 10)
        from 5780 target sequences
[build] counting k-mers ... done.
[build] target de Bruijn graph has 7419044 contigs and contains 374041821 k-mers 

TRANS_DATA=~anaconda2

KALLISTO_DIR=~/kallisto_results/kallisto_example

mv transcriptome_v5.idx ~/anaconda2/bin

srlab@roadrunner:~/anaconda2$ curl --remote-name http://owl.fish.washington.edu/nightingales/P_generosa/Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L001_R1_001.fastq.gz

srlab@roadrunner:~/anaconda2$ curl --remote-name http://owl.fish.washington.edu/nightingales/P_generosa/Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L002_R2_001.fastq.gz

kallisto quant --plaintext -i transcriptome_v5.idx -o output_01 -b 100 Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L001_R1_001.fastq.gz Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L001_R2_001.fastq.gz

[quant] fragment length distribution will be estimated from the data
[index] k-mer length: 31
[index] number of targets: 1,363,959
[index] number of k-mers: 374,041,821
[index] number of equivalence classes: 4,408,869
[quant] running in paired-end mode
[quant] will process pair 1: Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L001_R1_001.fastq.gz
                             Trueseq-stranded-mRNA-libraries-GeoRNA8-H1-NR021_S5_L001_R2_001.fastq.gz
[quant] finding pseudoalignments for the reads ... done
[quant] processed 1 reads, 0 reads pseudoaligned
[~warn] no reads pseudoaligned.
[quant] estimated average fragment length: 0
[   em] quantifying the abundances ... done
[   em] the Expectation-Maximization algorithm ran for 52 rounds
[~warn] Warning, zero reads pseudoaligned check your input files and index

```




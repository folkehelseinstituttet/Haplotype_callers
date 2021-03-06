# Haplotype_callers

## Clique-SNV
**URL** : https://github.com/vtsyvina/CliqueSNV

**Input**: bam file + reference

**Output**: complete haplotype sequences with frequency (fasta and json).

*Work in progress to test this on HCV bait enrich sequences - sensitive to trimming - sensitive to choice of reference aligner (e.g. bowtie2 vs. tanoti) - highly sensitive to repeated reads - that should be removed (unless strict amplicons such as SC2*

## Freebayes
**URL** : https://github.com/freebayes/freebayes

**Paper**: https://arxiv.org/abs/1207.3907 (From 2012, so outdated. They added a bunch since then)

**Input**: Pileup (BAM) from BWA or similar. (I.e. alignment to reference) BAM needs to be sorted. Should remove duplicates if shotgun sequencing, but NOT amplicon! (Since most of your reads will be duplicates by definition)

**Output**: VCF file.

**Example commandline**:
```
freebayes -f reference.fasta --ploidy 1 --use-best-n-alleles 3 --min-alternate-count 20 --min-coverage 20 --limit-coverage 10000 --haplotype-length 150 mapped.sorted.bam > freebayes_call.vcf
```

Freebayes actually works and has only minimal bugs and installation issues. It is not the fastest program, but it can be parallelised (details on their github page). Also, you can set it to only find the top N haplotypes. (We will rarely need to find a huge number of haplotypes in a sample?) Also, setting minimum threshold on number of reads / fraction of reads to be considered as a variant and not just noise is importan for runtime. Freebayes CAN discover indels and complex type mutations. It is even fairly accurate for homopolymer detection. The VCF file will have a LOT of information about depth, quality, mapping statistics etc, and as always all flags are explained in the file header.

*KA: would be nice if we could translate the output vcf to individual fasta files with frequency like Clique-SNV. @Ola: suggestions?*


**Important parameters**:

`--ploidy N` : Although most of our organisms are haploid, it seems setting ploidy to 1 means you only get 1 ALT allele. I need to experiment more with this, but for now leave the ploidy at default.

`--pooled-continuous` : Can be turned on to do frequency-based haplotype calling rather than literal directly from the reads. This requires strict quality thresholds to be set.

`--use-best-n-alleles N` : How many different haplotypes should you allow? Noisy data means you can find many low-frequency, so this can be set for efficiency

`--haplotype-length N` : The length of haplotypes to be called. This is only 3 by default! To enable haplotype calling this should be massively increased. It can be set at least to 50% of the read length

`--min-alternate-fraction N` : Minimum fraction of alternate observations to consider. Default is 0.05.

`--min-alternate-count N` : Minimum number of reads to consider an alt allele. Default is only 2, so should likely be increased for most use cases.

`--min-coverage N` : Minimum coverage to consider a position at all. Default is 0.

`--limit-coverage N` : Does downsampling to N coverage. This might be useful for high-coverage samples from amplicon sequencing etc.


## Savage / Haploconduct

**URL**: https://github.com/HaploConduct/HaploConduct

De novo caller

*KA tested on HCV-bait seq - did it work?*

## Vg-flow

**URL**: https://scholar.harvard.edu/jbaaijens/software/vg-flow

**Paper**: https://www.biorxiv.org/content/10.1101/645721v3.full

## V-phaser 2

**URL**: https://www.broadinstitute.org/viral-genomics/v-phaser-2

OLA tested this previously. I don't recall much about it except that I had a bit of trouble and ended up not using the results. Need more information

## QuRe

**URL**: https://sourceforge.net/projects/qure/

Aligner that attempts to reconstruct viral quasispecies by looking at all variants. Not tested.

## HaploClique

Quasispecies assembler that claims to also find indels. Not tested.

**URL**: https://github.com/cbg-ethz/haploclique

*not maintained... since 2017*


## Pehaplo

**URL**: https://github.com/chjiao/PEHaplo

*Briefly tested by KA, do not incorporate Q-scores (flat fasta from reads), not maintained*

## Shorah

**URL**: http://cbg-ethz.github.io/shorah/

**Paper**: https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-12-119

OLA tested this and did not like it. First, the conda installation is a bit messed up and you will have to manually install additional packages like samtools. Secondly, I only tried the amplicon-type analysis and it is broken and only outputs empty files. I have posted about this in an issue on their github: (https://github.com/cbg-ethz/shorah/issues/83).

We can probably ignore this for now.

NOTE: The same authors seem to have previously made a program called PredictHaplo that we can probably also safely ignore: https://github.com/cbg-ethz/PredictHaplo

## PredictHaplo

Same team as Shorah

**Example commandline**:
```
predicthaplo --ref ${ICTVdir}3a_D17763.fa --sam bwa_alignedReads.sam --prefix predicthaplo_
```

*KA: not so sure, in this paper PredictHaplo comes out as the most accurate (perhaps with the exception of CliqueSNV): https://www.sciencedirect.com/science/article/pii/S1567134820301088 - I suggest we pull out PredictHaplo as a separate program and test it*

*KA: easy install, but likely some bug in program*

```
After parsing the reads in file bwa_alignedReads.sam: average read length= 261.017 26089
First read considered in the analysis starts at position 78. Last read ends at position 9900
There are 26089 reads
Median of read lengths: 257.000
Local window size: 179
Minimum overlap of reads to local analysis windows: 152
Segmentation fault (core dumped)
```
*also reported by others: http://seqanswers.com/forums/showthread.php?t=47783 and https://github.com/cbg-ethz/PredictHaplo/issues/1 and not solved by authors of program*

## Haphpipe

Complete pipeline built specifically for virus. Not a specific caller - but a wrapper of programs for haplotype reconstruction.

**URL**: https://gwcbi.github.io/haphpipe_docs/

*KA: briefly tested - not really maintained, integrated clique-snv in pipeline: https://gwcbi.github.io/haphpipe_docs/hp_haplotype/#cliquesnv - but also PredictHaplo - see under Shorah*

## Hansel / Gretel

**URL**: https://github.com/samstudio8/gretel

**Input**: BAM file, VCF file and contig name

*This is originally intended for metagenomic purposes, so it might not make sense to use it for viral quasispecies/haplotypes. However, the algorithm seems similar to CliqueSNV - Finding linked SNVs by graph traversal. It seems to be set in a more probabilistic framework rather than pure counting. Undergoing testing by OLA.*

## MetaSNV

Weird SNV caller to be used on metagenomic data...

**URL**: https://github.com/metasnv-tool/metaSNV


# Haplotype_callers

## Clique-SNV
https://github.com/vtsyvina/CliqueSNV

Work in progress to test this on HCV bait enrich sequences


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
De novo caller
KA tested on HCV-bait seq - did it work?

## Vg-flow


## Haphpipe


## Pehaplo

## Shorah

**URL**: http://cbg-ethz.github.io/shorah/

**Paper**: https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-12-119

OLA tested this and did not like it. First, the conda installation is a bit messed up and you will have to manually install additional packages like samtools. Secondly, I only tried the amplicon-type analysis and it is broken and only outputs empty files. I have posted about this in an issue on their github: (https://github.com/cbg-ethz/shorah/issues/83).

We can probably ignore this for now.

## V-phaser 2

**URL**: https://www.broadinstitute.org/viral-genomics/v-phaser-2

OLA tested this previously. I don't recall much about it except that I had a bit of trouble and ended up not using the results. Need more information

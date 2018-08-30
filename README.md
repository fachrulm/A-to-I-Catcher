# A-to-I-Catcher
A pipeline to identify A-to-I RNA editing sites using RNA-seq data.
This method was adapted from [this paper](https://www.nature.com/articles/nmeth.2330) by _Ramaswami et al._ (2013), following [GATK's most current best practices](https://software.broadinstitute.org/gatk/best-practices/).

## STEPS for VC:

1. Run 2-pass mapping using STAR (_VCmapSTAR.sh_, then _2pass_VCmapSTAR.sh_).

> NOTE: Check BAM file with Picard's ValidateSamFile (_validbam.sh_) each time a BAM is generated.

2. Add read group using Picard's AddOrReplaceReadGroups (_addReadGroup.sh_).
3. Identify and remove duplicate reads with Picard's MarkDuplicates (_picardup.sh_).
4. Filter reads with low MAPQ (<20) with samtools (_filtersam.sh_).
5. Index BAM file from the previous step (_index.sh_).
6. Split N Trim BAM file of N CIGAR reads using GATK's SpliNCigarReads (_splitncigar.sh_).
7. Base Score Recalibration with GATK's BaseRecalibrator (_base_recalibrator.sh_).
8. Apply base recalibration with GATK's applyBQSR (_applybqst.sh_), then run variant calling with GATK's HaplotypeCaller (_gvcf_haplotypeCaller.sh_).
9. Merge GVCF files into a single VCF file with GATK's GenotypeGVCFs (_genotypegvcfs.sh_).

> NOTE: Check VCF file with Picard's ValidateVCF (_validvcf.sh_).

10. Variant Score Recalibration with GATK's VariantRecalibrator (_variantRecalib.sh_), then applyVQRSR (_applyvqsr.sh_) to generate a variant-recalibrated VCF.
11. Select only variants from VCF (_snponly.sh_), then filter variants against known SNPs [avsnp138] and splicing junctions [dbscsnv11] with ANNOVAR (_inputannovar.sh_, _dbsnp_annovar.sh_ and _spl_annovar.sh_).
12. Filter only A-to-I editing sites (_AtoIFilter.sh_).
13. Separate variants in Alu and non-Alu regions (_alufilter.sh_).
14. For in Alu variants, directly annotate to UCSC's knownGene (_knownGene.sh_).

> The rest of the steps are meant for non Alu variants.
15. Remove simple repeats, annotation from UCSC's RepeatMasker (_bedfilter.sh_).
16. Remove variants in homopolymer regions (_homopolymer.sh_).
17. Ensure unique mapping using BLAT (_BLAT.sh_).
18. Separate variants into repetitive and non-repetitive non Alu variants (_repeatorno.sh_).
19. Annotate to UCSC's knownGene (_nonALU_knownGene.sh_).

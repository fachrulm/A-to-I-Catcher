# A-to-I-Catcher
A pipeline to identify A-to-I RNA editing sites using RNA-seq data.

#STEPS for VC

1. Run 2-pass mapping using STAR (VCmapSTAR.sh, then 2pass_VCmapSTAR.sh).
##NOTE: Check BAM file with Picard's ValidateSamFile (validbam.sh) each time a BAM is generated.
2. Add read group using Picard's AddOrReplaceReadGroups (addReadGroup.sh).
3. Identify and remove duplicate reads with Picard's MarkDuplicates (picardup.sh).
4. Filter reads with low MAPQ (<20) with samtools (filtersam.sh).
5. Index BAM file from the previous step (index.sh).
6. Split N Trim BAM file of N CIGAR reads using GATK's SpliNCigarReads (splitncigar.sh).
7. Base Score Recalibration with GATK's BaseRecalibrator (base_recalibrator.sh).
8. Apply base recalibration with GATK's applyBQSR (applybqst.sh), then run variant calling with GATK's HaplotypeCaller (gvcf_haplotypeCaller.sh).
9. Merge GVCF files into a single VCF file with GATK's GenotypeGVCFs (genotypegvcfs.sh).
##NOTE: Check VCF file with Picard's ValidateVCF (validvcf.sh).
10. Variant Score Recalibration with GATK's VariantRecalibrator (variantRecalib.sh), then applyVQRSR (applyvqsr.sh) to generate a variant-recalibrated VCF.
11. Select only variants from VCF (snponly.sh), then filter variants against known SNPs [avsnp138] and splicing junctions [dbscsnv11] with ANNOVAR (inputannovar.sh, dbsnp_annovar.sh and spl_annovar.sh).
12. Filter only A-to-I editing sites (AtoIFilter.sh).
13. Separate variants in Alu and non-Alu regions (alufilter.sh).
14. For in Alu variants, directly annotate to UCSC's knownGene (knownGene.sh).

--- The rest of the steps are meant for non Alu variants ---\n
15. Remove simple repeats, annotation from UCSC's RepeatMasker (bedfilter.sh).
16. Remove variants in homopolymer regions (homopolymer.sh).
17. Ensure unique mapping using BLAT (BLAT.sh).
18. Separate variants into repetitive and non-repetitive non Alu variants (repeatorno.sh).
19. Annotate to UCSC's knownGene (nonALU_knownGene.sh).

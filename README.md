# The repository for the paper titled: "Comparative analyses of responses to exogenous and endogenous antiherbivore elicitors enable a forward genetics approach to identify maize gene candidates mediating sensitivity to herbivore-associated molecular patterns"

https://onlinelibrary.wiley.com/doi/10.1111/tpj.15510

1. Generating the new IBM genetic map using RNA-seq data for 105 RILs


Generate the files that will be used to identify SNPs in gene CDS sequences
```
# Generate a file containing the CDS coordinates and gene IDs
head -100 Zm4.44.bed |
awk '$8 == "CDS" {
print $1 "\t" $2 "\t" $3 "\t" substr(a[1], 5)}' |
awk '!visited[$0]++'  > gene_ids_by_region.tsv

# Creates a file containing the CDS coordinates for mpileup
awk '$8 == "CDS" {print $1 "\t" $2 "\t" $3}' Zm4.44.bed > Zm4.44_CDS.bed  # Remove non-CDS columns
awk '!visited[$0]++' Zm4.44_CDS.bed > Zm4.44_CDS_deduped.bed              # Remove duplicated rows
```

Use bcftools mpileup for SNP calling just in CDS regions across the genome
```
bcftools mpileup --threads 32 -O v -I -a "DP" --annotate AD -f Zea_mays.B73_RefGen_v4.dna_rm.toplevel.fa -R "Zm4.44_CDS_deduped.bed" *.bam | bcftools call -O v --threads 32 -c -v | bcftools view --threads 32 -i 'INFO/DP>10' > IBM_DP10_AD.vcf
```

The generated raw VCF was further filtered as described in the notebook: "VCF_filtering.ipynb".
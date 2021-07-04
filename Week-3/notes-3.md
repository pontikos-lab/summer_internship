# Week 3

## Intro to Conda (Simon Cockell)
- `wget` URL
- `sha256sum` verify installer has
- `ls -a` see hidden files

Environments: by default when running conda, there is environment called base. 
Channels: where you get software from.

- `conda create -n msa python` to create new environment w/ python
- `conda activate msa` 
- `conda config --add channels defaults`
- `conda config --add channels bioconda`
- `conda config --add channels conda-forge`
- `conda install -c bioconda tabix`
- `conda install -c bioconda bcftools`

## VCF Files (notes)
- https://gatk.broadinstitute.org/hc/en-us/articles/360035531692-VCF-Variant-Call-Format
- http://bioboot.github.io/web-2015/class-material/day3-vcf-2.html for tabix
- https://samtools.github.io/bcftools/howtos/query.html for bcftools query

Marker info:
- The first 9 columns of the header line & data lines describe the variants:
1. **CHROM**	the chromosome.
2. **POS**	the genome coordinate of the first base in the variant. Within a chromosome, VCF records are sorted in order of increasing position.
3. **ID**	a semicolon-separated list of marker identifiers.
4. **REF**	the reference allele expressed as a sequence of one or more A/C/G/T nucleotides (e.g. "A" or "AAC")
5. **ALT**	the alternate allele expressed as a sequence of one or more A/C/G/T nucleotides (e.g. "A" or "AAC"). If there is more than one alternate alleles, the field should be a comma-separated list of alternate alleles.
6. **QUAL**	probability that the ALT allele is incorrectly specified, expressed on the the phred scale (-10log10(probability)).
7. **FILTER**	Either "PASS" or a semicolon-separated list of failed quality control filters.
8. **INFO**	additional information (no white space, tabs, or semi-colons permitted).
9. **FORMAT**	colon-separated list of data subfields reported for each sample. 

Sample data
- Remaining columns contain the sample identifier & the colon-separated data subfields for each individual. 
- Most common format subfield is **GT** (genotype) data:
```
- 0/0 : the sample is homozygous reference
- 0/1 : the sample is heterozygous, carrying 1 copy of each of the REF and ALT alleles
- 1/1 : the sample is homozygous alternate
```

Examples
- `zcat example.vcf.gz | egrep "^#"` to extract metadata and header
- `zcat example.vcf.gz | head -n100000 > example.first100krows.vcf` to subset rows
- `zcat example.vcf.gz | wc -l` count lines
- `zcat example.vcf.gz | awk '{OFS="\t"; if ($2 > 16092228 && $2 < 16093177){ print }}'`
- `zcat example.vcf.gz | egrep "^#[^#]"` get header
- `zcat example.vcf.gz | cut -f1-10,20 | less` pick selection of columns

## VCF Files (Issue 5)
Using example.vcf.gz:
1. extract the meta lines of the vcf file
`zcat example.vcf.gz | egrep "^##"`
2. extract all but the meta lines of the vcf file
`zcat example.vcf.gz | egrep -v "^##"`
3. how many variants are listed in this file?
`zcat example.vcf.gz | egrep -v "^##" | wc -l`
4. extract the first 5 variants without the meta lines using the first 10 columns
`zcat example.vcf.gz | egrep -v "^#" | head -n5 | cut -f1-10`
5. list the sample names (using bcftools query)
`bcftools query -l example.vcf.gz`
6. extract the CHROM, POS, and AC information, separated by tabs for the first three variants using bcftools query;
`bcftools query -f '%CHROM    %POS    %AC\n' example.vcf.gz | head -3`
7. save it in a file and add column names to it (CHROM, POS, AC)
`bcftools query -f '%CHROM %POS %AC\n' example.vcf.gz | head -3 > example1.csv`
`echo -e "CHROM\tPOS\tAC" | cat - example1.csv > example.csv | rm example1.csv`

Using the chr14_variants.vcf.gz file:
1. index this complete vcf file with tabix
`tabix -p vcf chr14_variants.vcf.gz`
2. how many variants are there on the 14th chromosome between position 20,000,000 and 21,000,000?
`tabix chr14_variants.vcf.gz 14:20,000,000-21,000,000 | wc -l`
3. what is the REF and ALT allele for the variant at chromosome 14 and position 18223657?
`tabix -h chr14_variants.vcf.gz 14:18,223,657-18,223,657 | bcftools query -f '%REF %ALT\n' -`

  




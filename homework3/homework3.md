# Homework3: Pipelines for generating genome and annotation summaries of _Drosophila melanogaster_ 

## Retrieve fasta and annotation files from the database (FlyBase)
- All chromosome genome file (FASTA_FILE): `data/homework3/dmel_all_chromosomes.fasta.gz`
- Annotation GTF file (GTF_FILE): `data/homework3/dmel_annotation.gtf.gz`

## Scripts needed for pipelines
- Genome summary script: `code/script/hw3_genome_summary.sh`
- Annotation summary script: `code/script/hw3_annotation_gtf.gz`

## Part I: Summarize Genome Assembly
### A. All chromosome genome file integrity (checksum)

Checksum file: 
- `result/homework3/dmel_all_chromosomes.fasta.gz.md5`

Command used:
```bash
md5sum data/homework3/dmel_all_chromosomes.fasta.gz
````

Output:
```text
ccb86e94117eb4eeaaf70efb6be1b6b9  data/homework3/dmel_all_chromosomes.fasta.gz
```

### B. Calculate summaries of the genome

Command used:
```bash
faSize data/homework3/dmel_all_chromosomes.fasta.gz
```

Output from `result/homework3/genome_faSize_summary.txt`:
- Total number of nucleotides: 143,726,002
- Total number of N's: 1,152,978
- Total number of sequences: 1,870 

## Part II: Summarize an Annotation File
### A. Annotation GTF file integrity (checksum)

Checksum file:
- `result/homework3/dmel_annotation.gtf.gz.md5`

Command used:
```bash
md5sum data/homework3/dmel_annotation.gtf.gz
```

Output:
```text
ea600dbb86f1779463f69082131753cd  data/homework3/dmel_annotation.gtf.gz
```

### B. Compile a report summarizing the annotation
#### Total number of feature counts by type (from most common to least common)

Command used:
```bash
bioawk -c gff '{print $feature}' data/homework3/dmel_annotation.gtf.gz \
 | sort \
 | uniq -c \
 | sort -nr
```
- `bioawk -c gff '{print $feature}' ...` extracts the feature type field (e.g., gene, exon, CDS) from each GTF record.
- `sort` groups identical feature names together.
- `uniq -c` counts the frequency of each feature type.
- `sort -nr` sorts counts numerically in descending order.

Output saved to: 
- `result/homework3/annotation_feature_type_counts.txt`

```text
 190176 exon
 163377 CDS
  46856 5UTR
  33778 3UTR
  30922 start_codon
  30862 stop_codon
  30836 mRNA
  17872 gene
   3059 ncRNA
    485 miRNA
    365 pseudogene
    312 tRNA
    270 snoRNA
    262 pre_miRNA
    115 rRNA
     32 snRNA
```

#### Total number of genes per chromosome arm (X, Y, 2L, 2R, 3L, 3R, 4)

Command used:
```bash
bioawk -c gff '$feature=="gene" {print $seqname}' data/homework3/dmel_annotation.gtf.gz \
  |grep -E '^(X|Y|2L|2R|3L|3R|4)$' \
  | sort \
  | uniq -c \
  | sort -nr
```
- `bioawk -c gff '$feature=="gene" {print $seqname}' ...` keeps only `gene` features and prints the chromosome arm name.
- `grep -E` pulls/restricts to only the required arms (X, Y, 2L, 2R, 3L, 3R, 4).
- `sort | uniq -c | sort -nr` counts genes per arm and sorts them from most to least.
 
Output saved to:
- `result/homework3/annotation_genes_per_arm.txt`

```text
   4226 3R
   3649 2R
   3508 2L
   3481 3L
   2704 X
    114 4
    113 Y
```


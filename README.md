# 2024-10-21_jb_phage-assembly

## Library prep

From Seqcenter:

```text
Illumina sequencing libraries were prepared using the tagmentation-based and PCR-based
Illumina DNA Prep kit and custom IDT 10bp unique dual indices (UDI) with a target insert size of
280 bp. No additional DNA fragmentation or size selection steps were performed. Illumina
sequencing was performed on an Illumina NovaSeq X Plus sequencer in one or more multiplexed
shared-flow-cell runs, producing 2x151bp paired-end reads. Demultiplexing, quality control and
adapter trimming was performed with bcl-convert1 (v4.2.4). Sequencing statistics are included in
the ‘DNA Sequencing Stats.xlsx’ file.
```

## Samples
/work/geisingerlab/SEQCENTER-SEQUENCINGREADS-BACKUP/20241013_IlluminaDNAReads_JBA420-421-JBphis

4 JBphi samples to work with:

JBphi20
JBphi21
JBphi36
JBphi44

Made links to these files in ./input/fastq_raw

```bash
cd ${BASE_DIR}/input/fastq_raw
find /work/geisingerlab/SEQCENTER-SEQUENCINGREADS-BACKUP/20241013_IlluminaDNAReads_JBA420-421-JBphis -name "JBphi*" >fastq_inputs.list

paste fastq_inputs.list | while read file;
do
ln -s $file ./
done

```

## fastqc
First run using script 0

## bbduk
Per assembly protocol from Eddie (provided by collaborators?), ran bbduk to remove phix.  This resulted in 0 dropped reads, so there are no contaminants.  Therefore, went back to original files and performed quality trimming per their recommendations.

Options:
ref=/bioinformatics/bbmap/resources/adapters.fa ktrim=r tpe tbo minlen=100
qtrim=rl trimq=28

## Subsampling?

```text
for file in ./*.fastq.gz; do
> echo $(zcat $file | wc -l )/4 | bc
> done

1846581
1846581
2253974
2253974
1999545
1999545
1682253
1682253
```

Per protocol, 
"Excessive coverage can be detrimental to genome assembly and result in the generation of spurious contigs, homopolymer and indel errors. Most de Bruijn assemblers work best between 60-100x coverage."

Number of reads = (Expected coverage * Expected genome size in bp) / Read length in bp

Read length = 151?
Use expected coverage = 100
Per EG, expect ~43 kb genome.

Num.reads = 100 coverage * 43,000 bp / 151 bp = 28477 reads

Ran following code:

```bash
module load seqtk/1.3
cd /work/geisingerlab/Mark/genome_assembly/2024-10-21_jb_phage-assembly/input/fastq_trimmed

mkdir -p ../fastq_subsamples

for file in ./*.fastq.gz; do
  filename=$(basename $file)
  seqtk sample -s 100 $file 28500 > ../fastq_subsamples/28-5k_subsample_${filename}
done

```


## Assembly

Assembled using SPAdes with suggested parameters:

spades.py --careful -t 8 -m 12 -k 55,77,99,127 -1 $r1 -2 $r2 -o ./spades_assembly/$name

Go into spades_assembly directory and count contigs and scaffolds:

```bash
for dir in ./*; do echo $dir; echo 'contigs:'; grep -c '>' $dir/contigs.fasta; echo 'scaffolds:'; grep -c '>' $dir/scaffolds.fasta; done
```

```text
./JBphi_20
contigs:
2
scaffolds:
2
./JBphi_21
contigs:
2
scaffolds:
2
./JBphi_36
contigs:
2
scaffolds:
2
./JBphi_44
contigs:
7
scaffolds:
7
```


```bash
# Recommendation for filtering contigs
seqkit fx2tab contigs.fasta | csvtk mutate -H -t -f 1 -p "cov_(.+)" | csvtk
mutate -H -t -f 1 -p "length_([0-9]+)" | awk -F "\t" '$4>=10 && $5>=500' | seqkit
tab2fx > filtered_contigs.fasta

```

## Mapping reads back to assembly


## Consider annotations - see BINF6308


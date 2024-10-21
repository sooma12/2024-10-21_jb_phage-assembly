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


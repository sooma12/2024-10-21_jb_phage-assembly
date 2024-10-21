# 2024-10-21_jb_phage-assembly

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


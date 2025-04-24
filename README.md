# MyGenome
This is a detailed outline of the genome assembly process of <em>Pyricularia oryzae</em>. <em>Pyricularia oryzae</em>, also known as Rice Blast, is a disease in rice. According to Ohio State University, Rice Blast causes an estimated $66 billion in worldwide crop damage. The sample of <em>Pyricularia oryzae</em> was found in St. Augustine grass (<em>Stenotaphrum secundatum</em>).
## Method
### Sequencing 
The sample was sequenced utilizing the Illumina NovaSeq X platform.
### Quality Checking
The quality of the sequences was checked using FastQC, a bioinformatics software.
```
fastqc Pg1108_1.fq.gz
fastqc Pg1108_2.fq.gz
```
<p><small><em><b>NOTE</b>: 1 is the forward reads and 2 is the reverse reads</em></small></p>

![screencapture-file-C-Users-josev-Documents-fastqc-report-html-2025-04-22-21_23_08](https://github.com/user-attachments/assets/acd9f243-c14d-4820-a1e6-f5ff9651c406)
The FastQC report showed that there was adaptor contamination that must be trimmed before assembly can start.
### Trimming
The sequences were trimmed using trimmomatic. The following code was used:
```
java -jar trimmomatic-0.38.jar  PE -threads 2 -phred33 -trimlog Pg1108_errorlog.txt Pg1108_1.fq Pg1108_2.fq Pg1108_1_paired.fq Pg1108_1_unpaired.fq Pg1108_2_paired.fq Pg1108_2_unpaired.fq ILLUMINACLIP:adaptors.fasta:2:30:10 CROP:280 SLIDINGWINDOW:20:20 MINLEN:150
```
<em><b>NOTE</b>: trimmomatic-0.38.jar can be found in Scripts </em>

By running FastQC again, the following shows that the adapters have been trimmed from the sequences.

<img width="706" alt="After_Adaptor_Content" src="https://github.com/user-attachments/assets/0c82b63c-f154-4a61-b1e1-e103b0a58aba" />
### Assembly
The velvet is the program that was used for assembly. However, velvet requires a k-mer value to base the assembly on. <a href = https://dna.med.monash.edu/~torsten/velvet_advisor/> Velvet Advisor</a> is used to find a suggested k-mer value as a starting point.

Velvet Optimiser will be used to find the best k-mer value in a 60 k-mer range of the suggested k-mer value by steps of 10. The following code was run:
```
sbatch velvetoptimiser_noclean.sh Pg1108 61 121 10
```
First Velvet Optimiser Results
<table>
  <tr>
    <th>K-mer</th>
    <th>Genome Size</th>
    <th># of contigs</th>
  </tr>
  <tr>
    <td>101</td>
    <td>44,101,912</td>
    <td>9,697</td>
  </tr>
  <tr>
</table>

<em><b>EX</b>: If the suggested K-mer value is 91, then Velvet optimiser will test k-mer values 61, 71, 81, 91, 101, 111, and 121.</em>

<em><b>NOTE</b>: velvetoptimser_noclean.sh can be found in Scripts </em>

Using the k-mer suggested by the Velvet Optimiser, it is run through the Velvet Optimiser once more. However, the difference is that it is a range of 20 k-mers by steps of 2. 
```
sbatch velvetoptimiser_noclean.sh Pg1108 91 111 2
```
<table>
  <tr>
    <th>K-mer</th>
    <th>Genome Size</th>
    <th># of contigs</th>
  </tr>
  <tr>
    <td>97</td>
    <td> 43.635,922</td>
    <td>8,078</td>
  </tr>
  <tr>
</table>

<em><b>EX</b>: If the suggested k-mer value is 101, then Velvet optimiser will test k-mer values 91, 93, 95, 97, 99, 101, 103, 105, 107, 109, and 111 </em>

<em><b>NOTE</b>: velvetoptimser_noclean.sh can be found in Scripts </em>

Finally, the genome can be assembled using the best k-mer values using the following code:
```
velveth Pg1108_97 97 -shortPaired -fastq -separate Pg1108_1_paired.fastq Pg1108_2_paired.fastq
velvetg Pg1108_97
```
<em><b>NOTE</b>: Pg1108_1_paired.fastq and Pg1108_2_paired.fastq our outputs from trimmomatic </em>
### Finalizing the Assembly
The headings for each contig are changed by using:
```
perl SimpleFastaHeaders.pl Pg1108.fasta
```
<em><b>NOTE</b>: SimpleFastaHeaders.pl can be found in Scripts </em>

Short contigs are removed using the following code:
```
perl CullShortContigs.pl Pg1108_nh.fasta
```
<em><b>NOTE</b>: CullShortContigs.pl can be found in Scripts </em>

Each contig length is measured using:
```
SeqLen.pl Pg1108_final.fasta
```
<em><b>NOTE</b>: SeqLen.pl can be found in Scripts </em>

The completeness of the genome is measured using BUSCO:
```
sbatch BuscoSingularity.sh Pg1108_final.fasta
```
<em><b>NOTE</b>: BuscoSingularity.sh can be found in Scripts </em>

<table>
  <tr>
    <th>BUSCO score (complete)</th>
    <th>BUSCO score (complete + fragmented)</th>
  </tr>
  <tr>
    <td>95.7%</td>
    <td>98.36%</td>
  </tr>
</table>

### BLAST
The first blast that was run to find all the mitochondrial sequences in the genome. The following code was used:
```
blastn -query MoMitochondrion.fasta -subject Pg1108_nh.fasta -evalue 1e-50 -max_target_seqs 20000 -outfmt '6 qseqid sseqid slen length qstart qend sstart send btop' -out MoMitochondrion.Pg1108.BLAST
awk '$4/$3 > 0.9 {print $2 ",mitochondrion"}' MoMitochondrion.Pg1108.BLAST > Pg1108_mitochondrion.csv
```
This will produce a csv file than contains the contigs that were found to be mitochondrial sequences.









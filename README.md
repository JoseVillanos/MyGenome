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
<em><b>NOTE</b>: 1 is the forward reads and 2 is the reverse reads</em>
![screencapture-file-C-Users-josev-Documents-fastqc-report-html-2025-04-22-21_23_08](https://github.com/user-attachments/assets/acd9f243-c14d-4820-a1e6-f5ff9651c406)
The FastQC report showed that there was adaptor contamination that must be trimmed before assembly can start.
### Trimming
The sequences were trimmed using trimmomatic. The following code was used:
```
java -jar trimmomatic-0.38.jar  PE -threads 2 -phred33 -trimlog Pg1108_errorlog.txt Pg1108_1.fq Pg1108_2.fq Pg1108_1_paired.fq Pg1108_1_unpaired.fq Pg1108_2_paired.fq Pg1108_2_unpaired.fq ILLUMINACLIP:adaptors.fasta:2:30:10 CROP:280 SLIDINGWINDOW:20:20 MINLEN:150
```
By running FastQC again, the following shows that the adapters have been trimmed from the sequences 
<img width="706" alt="After_Adaptor_Content" src="https://github.com/user-attachments/assets/0c82b63c-f154-4a61-b1e1-e103b0a58aba" />
### Assembly
The velvet is the program that was used for assembly. However, velvet requires a k-mer value to base the assembly on. <a href = https://dna.med.monash.edu/~torsten/velvet_advisor/> Velvet Advisor</a> is used to find a suggested k-mer value as a starting point.

Velvet Optimiser will be used to find the best k-mer value in a 60 k-mer range of the suggested k-mer value by steps of 10. The following code was ran:
```
sbatch velvetoptimiser_noclean.sh Pg1108 61 121 10
```
<em><b>EX</b>: If the suggested K-mer value is 101, then Velvet optimiser will test k-mer values 61, 71, 81, 91, 101, 111, and 121.</em>

<em><b>NOTE</b>: velvetoptimser_noclean.sh can be found in Scripts </em>







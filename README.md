# MyGenome
This detailed outline of the genome assembly process of <em>Pyricularia oryzae</em>
## Method
### Quality Checking
I used FastQC to check if the quality of the reads was good enough to start assembling. The adaptor content in my forward and reserve reads was too high, so trimming had to occur.

![screencapture-file-C-Users-josev-Documents-fastqc-report-html-2025-04-22-21_23_08](https://github.com/user-attachments/assets/acd9f243-c14d-4820-a1e6-f5ff9651c406)



### Trimming
For the trimming I used Trimmoatic which is somewhat frustrating to use nevertheless, I ran the following code
```
java -jar trimmomatic-0.38.jar  PE -threads 2 -phred33 -trimlog Pg1108_errorlog.txt Pg1108_1.fq Pg1108_2.fq Pg1108_1_paired.fq Pg1108_1_unpaired.fq Pg1108_2_paired.fq Pg1108_2_unpaired.fq ILLUMINACLIP:adaptors.fasta:2:30:10 CROP:280 SLIDINGWINDOW:20:20 MINLEN:150
```
<img width="706" alt="After_Adaptor_Content" src="https://github.com/user-attachments/assets/0c82b63c-f154-4a61-b1e1-e103b0a58aba" />

Now we are as good as new!

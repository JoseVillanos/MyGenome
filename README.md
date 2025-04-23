# MyGenome
## Method
### Quality Checking
I used fastqc to check if the quality of the reads was good enough to start assembling. The adaptor content in my forward and reserve reads was too high, so trimming had to occur.

<img width="719" alt="Before_Adaptor_Content" src="https://github.com/user-attachments/assets/4ca4217e-5a73-4c59-8415-3451f338a64b
" />


### Trimming
For the trimming I used Trimmoatic which is somewhat frustrating to use nevertheless, I ran the following code
```
java -jar trimmomatic-0.38.jar  PE -threads 2 -phred33 -trimlog Pg1108_errorlog.txt Pg1108_1.fq Pg1108_2.fq Pg1108_1_paired.fq Pg1108_1_unpaired.fq Pg1108_2_paired.fq Pg1108_2_unpaired.fq ILLUMINACLIP:adaptors.fasta:2:30:10 CROP:280 SLIDINGWINDOW:20:20 MINLEN:150
```
<img width="706" alt="After_Adaptor_Content" src="https://github.com/user-attachments/assets/0c82b63c-f154-4a61-b1e1-e103b0a58aba" />

Now we are as good as new!

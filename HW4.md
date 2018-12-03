## Calculate the following for all sequences ≤ 100kb and all sequences > 100kb:

   1. Total number of nucleotides
   2. Total number of Ns
   3. Total number of sequences

__Because the calculations will be for two genome partitions, there will be 6 total responses.__


For sequences __<= 100,000 bases__:

   1. Total number of nucleotides
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print $seq }' *fasta | tr -d '\n' | wc -m
   6178042
   ```
   2. Total number of Ns
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print $seq }' *fasta | tr -cd '[N]' | wc -m
   662593
   ```
   3. Total number of sequences
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print $seq }' *fasta | wc -l
   1863
   ```
   
For sequences __> 100,000 bases__:

   1. Total number of nucleotides
   ```
   bioawk -c fastx 'length($seq) > 100000 { print $seq }' *fasta | tr -d '\n' | wc -m
   137547960
   ```
   2. Total number of Ns
   ```
   bioawk -c fastx 'length($seq) > 100000 { print $seq }' *fasta | tr -cd '[N]' | wc -m
   490385
   ```
   3. Total number of sequences
   ```
   bioawk -c fastx 'length($seq) > 100000 { print $seq }' *fasta | wc -l
   7
   ```
   
## Plots of the following for the whole genome, for all sequences ≤ 100kb, and all sequences > 100kb:

   1. Sequence length distribution
   2. Sequence GC% distribution  
   3. Cumulative genome size sorted from largest to smallest sequences  
    
__Because the calculations will be for the whole genome and two genome partitions, there will be 9 total plots.__

For sequences __<= 100,000 bases__:

   1. Sequence lengh distribution
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print length($seq) }' *fasta | sort | uniq -c | column -t > seqlengthsunder100
   ```

For sequences __<= 100,000 bases__:

   1. Sequence lengh distribution
   ```
   bioawk -c fastx 'length($seq) > 100000 { print length($seq) }' *fasta | sort | uniq -c | column -t > seqlengthsover100
   ```
   
# Genome assembly
Note: This part of homework 4 is still being arranged. When this note is gone, it should be ready.

## Assemble a genome from MinION reads
Hint: Read up on miniasm here. We're using one of the simplest assembly approaches possible. This assembly can literally be accomplished with three lines of code. This will literally take only 3 command lines.

   1. Download the reads from here
   2. Use minimap to overlap reads
   3. Use miniasm to construct an assembly
   
To use minimap to overlap reads, queue into a node and run the following:
```
wget https://hpc.oit.uci.edu/~solarese/ee282/iso1_onp_a2_1kb.fastq.gz
qrsh -q free128 -pe openmp 32
gunzip iso1_onp_a2_1kb.fastq.gz
minimap -t 32 -Sw5 -L100 -m0 iso*.fastq iso*.fastq | gzip -1 > iso1_onp.paf.gz


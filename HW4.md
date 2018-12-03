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

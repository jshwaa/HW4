# Summarize partitions of a genome assembly
__We will be revisiting the Drosophila melanogaster genome. As with Homework 3, start at flybase.org. Go to the most current download genomes section and download the gzipped fasta file for all chromosomes.__

__Hint: The partitioning can be accomplished in many different ways. In my opinion, the easiest way is by using bioawk and faSize. The bioawk tool can be found in the module jje/jjeutils and the fa* utilities can be found in the module jje/kent.__

## Calculate the following for all sequences ≤ 100kb and all sequences > 100kb:

   1. Total number of nucleotides
   2. Total number of Ns
   3. Total number of sequences

__Because the calculations will be for two genome partitions, there will be 6 total responses.__

First, load the requisite modules and files for this assignment:
```
module load jje/jjeutils
module load rstudio
wget
```
   
   __1. Total number of nucleotides__
   For sequences __<=100kb__:
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print $seq }' *fasta | tr -d '\n' | wc -m
   6178042
   ```
   For sequences __>100kb:__
   ```
   bioawk -c fastx 'length($seq) > 100000 { print $seq }' *fasta | tr -d '\n' | wc -m
   137547960
   ```
   
   __2. Total number of Ns__
   For sequences __<=100kb__:
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print $seq }' *fasta | tr -cd '[N]' | wc -m
   662593
   ```
   For sequences __>100kb__:
   ```
   bioawk -c fastx 'length($seq) > 100000 { print $seq }' *fasta | tr -cd '[N]' | wc -m
   490385
   ```
   
   __3. Total number of sequences__
   For sequences __<=100kb__:
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print $seq }' *fasta | wc -l
   1863
   ```
   For sequences __>100kb__:
   ```
   bioawk -c fastx 'length($seq) > 100000 { print $seq }' *fasta | wc -l
   7
   ```
   
## Plots of the following for the whole genome, for all sequences ≤ 100kb, and all sequences > 100kb:

   1. Sequence length distribution
   2. Sequence GC% distribution  
   3. Cumulative genome size sorted from largest to smallest sequences  
    
__Because the calculations will be for the whole genome and two genome partitions, there will be 9 total plots.__

   __1. Sequence lengh distribution__
   For __whole genome__:
   ```
   bioawk -c fastx '{ print length($seq) }' *fasta | sort -n | uniq -c | column -t > seqlengthwhole
   ```
   For sequences __<=100kb__:
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print length($seq) }' *fasta | sort -n | uniq -c | column -t > seqlengthunder
   ```
   For sequences __>100kb__: 
   ```
   bioawk -c fastx 'length($seq) > 100000 { print length($seq) }' *fasta | sort -n | uniq -c | column -t > seqlengthover
   ```
   Following creation of these length counts, create the R script:
   ```
   
   

   __2. Sequence GC% distribution__
   For whole genome:
   ```
   bioawk -c fastx '{ print length($seq) }' *fasta | sort -n | uniq -c | column -t > seqlengthwhole
   ```
   For sequences <= 100kb:
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print length($seq) }' *fasta | sort -n | uniq -c | column -t > seqlengthunder
   ```
   For sequences >100kb: 
   ```
   bioawk -c fastx 'length($seq) > 100000 { print length($seq) }' *fasta | sort -n | uniq -c | column -t > seqlengthover
   ```
   
   __3. Cumulative genomze size sorted from largest to smallest sequences__
   For __whole genome__:
   ```
   mkfifo seqwhole_fifo

   bioawk -c fastx '{ print length($seq) }' *fasta \
   | sort -rn \
   | awk ' BEGIN { print "Assembly\tLength\nWhole_Genome\t0" } { print "Whole_Genome\t" $1 } ' \
   > seqwhole_fifo & 

   plotCDF2 seqwhole_fifo seqwholecdf.png

   rm seqwhole_fifo

   display seqwholecdf.png
   ```
   For sequences __<=100kb__:
   ```
   mkfifo sequnder_fifo

   bioawk -c fastx 'length($seq) <= 100000 { print length($seq) }' *fasta \
   | sort -rn \
   | awk ' BEGIN { print "Assembly\tLength\nSeqLength<=100kb\t0" } { print "SeqLength<=100kb\t" $1 } ' \
   > sequnder_fifo & 

   plotCDF2 sequnder_fifo sequnder.png

   rm sequnder_fifo

   display sequnder.png
   ```
   For sequences __>100kb__:
   ```
    mkfifo seqover_fifo

   bioawk -c fastx 'length($seq) > 100000 { print length($seq) }' *fasta \
   | sort -rn \
   | awk ' BEGIN { print "Assembly\tLength\nSeqLength>100kb\t0" } { print "SeqLength>100kb\t" $1 } ' \
   > seqover_fifo & 

   plotCDF2 seqover_fifo seqover.png

   rm seqover_fifo

   display seqover.png
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


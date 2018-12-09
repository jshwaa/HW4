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
mkdir ~/homework4
cd ~/homework4
module load jje/jjeutils
module load rstudio
wget ftp://ftp.flybase.net/genomes/Drosophila_melanogaster/current/fasta/dmel-all-chromosome-r6.24.fasta.gz
gunzip dmel-all-chromosome-r6.24.fasta.gz
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
   bioawk -c fastx 'length($seq) <= 100000 { print length($seq) }' *fasta | sort -n | uniq -c | column -t \
   > seqlengthunder
   ```
   For sequences __>100kb__: 
   ```
   bioawk -c fastx 'length($seq) > 100000 { print length($seq) }' *fasta | sort -n | uniq -c | column -t \
   > seqlengthover
   ```
   
   __2. Sequence GC% distribution__
   For whole genome:
   ```
   bioawk -c fastx '{ print gc($seq) }' *fasta | sort -n | uniq -c | column -t > seqgcwhole
   ```
   For sequences <= 100kb:
   ```
   bioawk -c fastx 'length($seq) <= 100000 { print gc($seq) }' *fasta | sort -n | uniq -c | column -t \
   > seqgcunder
   ```
   For sequences >100kb: 
   ```
   bioawk -c fastx 'length($seq) > 100000 { print gc($seq) }' *fasta | sort -n | uniq -c | column -t \
   > seqgcover
   ```
   
   After creation of these length counts, create and save the following R script as "seqdist.R":
   ```
   lengthwhole <-  read.table('seqlengthwhole')
   lengthunder <- read.table('seqlengthunder')
   lengthover <- read.table('seqlengthover')
   gcwhole <- read.table('seqgcwhole')
   gcunder <- read.table('seqgcunder')
   gcover <- read.table('seqgcover')

   lengthwhole$V2 <- lengthwhole$V2/1000
   lengthunder$V2 <- lengthunder$V2/1000
   lengthover$V2 <- lengthover$V2/1000

   gcwhole$V2 <- gcwhole$V2*100
   gcunder$V2 <- gcunder$V2*100
   gcover$V2 <- gcovere$V2*100


   png("seqlengthwhole.png",width=480,height=480)
   plot(x=lengthwhole$V2, y=lengthwhole$V1, type='h', xlab='Sequence Length (kb)', ylab='Count', main='Sequence Length Distribution        Whole Genome')
   dev.off()

   png("seqlengthunder.png",width=480,height=480)
   plot(x=lengthunder$V2, y=lengthunder$V1, type='h', xlab='Sequence Length (kb)', ylab='Count', main='Sequence Length Distribution        (<=100kb)')
   dev.off()

   png("seqlengthover.png",width=480,height=480)
   plot(x=lengthover$V2, y=lengthover$V1, type='h', xlab='Sequence Length (kb)', ylab='Count', main='Sequence Length Distribution          (>100kb)')
   dev.off()

   png("seqgcwhole.png",width=480,height=480)
   plot(x=gcwhole$V2, y=gcwhole$V1, type='h', xlab='GC%', ylab='Count', main='Whole Genome GC%')
   dev.off()

   png("seqgcunder.png",width=480,height=480)
   plot(x=gcunder$V2, y=gcunder$V1, type='h', xlab='GC%', ylab='Count', main='Sequence Length <=100kb GC%')
   dev.off()

   png("seqgcover.png",width=480,height=480)
   plot(x=gcover$V2, y=gcover$V1, type='h', xlab='GC%', ylab='Count', main='Sequence Length >100kb GC%')
   dev.off()
   ```
   And run it with `Rscript seqdist.R` to create png images of distribution plots of each partition.
   Finally, view the plots with:
   ```
   display seqlengthwhole.png
   display seqlengthunder.png
   display seqlengthover.png
   display seqgcwhole.png
   display seqgcunder.png
   display seqgcover.png
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
__Note: This part of homework 4 is still being arranged. When this note is gone, it should be ready.__

## Assemble a genome from MinION reads
__Hint: Read up on miniasm here. We're using one of the simplest assembly approaches possible. This assembly can literally be accomplished with three lines of code. This will literally take only 3 command lines.__

   __1. Download the reads from here__
   __2. Use minimap to overlap reads__
   __3. Use miniasm to construct an assembly__
   
To use miniasm, first download the reads, queue into a node and run minimap to perform all-vs-all read self-mapping with the following:
```
wget https://hpc.oit.uci.edu/~solarese/ee282/iso1_onp_a2_1kb.fastq.gz
qrsh -q free128 -pe openmp 32
gunzip iso1_onp_a2_1kb.fastq.gz
minimap -t 32 -Sw5 -L100 -m0 iso1_onp_a2_1kb.fastq iso1_onp_a2_1kb.fastq | gzip -1 > iso1_onp.paf.gz
```
Then, to use minimap for de novo assembly and the generation of a GFA assembly graph, enter the following at the command line:
```
$miniasm -f iso1_onp_a2_1kb.fastq iso1_onp.paf.gz > reads.gfa
```

# Assembly assessment
__Hint: For MUMmer, you should run nucmer, delta-filter, and mummerplot.__

   __1. Calculate the N50 of your assembly (this can be done with only faSize+awk+sort or with bioawk+awk+sort) and compare it to the           Drosophila community reference's contig N50 (here)__
        To calculate the N50, first convert the assembly file to fasta format for use with bioawk:
        ```
        awk ' $0 ~/^S/ { print ">" $2" \n" $3 } ' reads.gfa > reads.fa
        ```
        Then, use the following to determine the n50, the length at which 50% of the genome is contained in contig lengths of greater or         equal size:
        ```
        bioawk -c fastx ' { print length($seq); n=n+length($seq); } END { print n; } ' reads.fa \
        | sort -rn \
        | gawk ' NR == 1 { n = $1 }; NR > 1 { ni = $1 + ni; } ni/n > 0.5 { print $1; exit; } ' 
        
        4494246
        ```
        

   __2. Compare your assembly to the contig assembly (not the scaffold assembly!) from Drosophila melanogaster on FlyBase using a               dotplot constructed with MUMmer (Hint: use faSplitByN as demonstrated in class)

   __3. Compare your assembly to both the contig assembly and the scaffold assembly from the Drosophila melanogaster on FlyBase using a         contiguity plot (Hint: use plotCDF2 as demonstrated in class and see this example)

   __4. Calculate BUSCO scores of both assemblies and compare them

awk ' $0 ~/^S/ { print ">" $2" \n" $3 } ' $processed/reads.gfa \
| tee >(n50 /dev/stdin > $reports/n50.txt) \
| fold -w 60 \
> $processed/unitigs.fa

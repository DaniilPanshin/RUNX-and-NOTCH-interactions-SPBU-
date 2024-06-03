#### Raw data download

<pre><code>wget -nc ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR148/088/SRR14827288/SRR14827288_1.fastq.gz \
wget -nc ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR148/088/SRR14827288/SRR14827288_2.fastq.gz \
wget -nc ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR148/089/SRR14827289/SRR14827289_2.fastq.gz \
wget -nc ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR148/089/SRR14827289/SRR14827289_1.fastq.gz</code></pre>

#### Download indexes
  
<pre><code>wget ftp://ftp.ccb.jhu.edu/pub/data/bowtie2_indexes/GRCm39.zip</code></pre>

#### Aligment bowtie2
  
<pre><code>bowtie2 -q -p 4 -k 1 --local --no-unal -x GRCm39 -1 SRR14827288_1.fastq -2 SRR14827288_2.fastq > /home/dvoyashov/papka_for_danya_lox/results/bowtie/SRR14827288.sam \
bowtie2 -q -p 4 -k 1 --local --no-unal -x GRCm39 -1 SRR14827289_1.fastq -2 SRR14827289_2.fastq> /home/dvoyashov/papka_for_danya_lox/results/bowtie/Fibro_input.sam</code></pre>

#### Mitochondrial reads filtration

<pre><code>sed '/chrM/d;/random/d;/chrUn/d' $1.sam > $1_filtered.sam \
/mnt/StudentsWork/pdocshin/CHIP/prog/samtools-1.19.2/sam_view.c -S -b $1_filtered.sam > $1.bam \
/mnt/StudentsWork/pdocshin/CHIP/prog/samtools-1.19.2/sam_view.c -c $1.bam</code></pre>

#### BAM convertation

<pre><code>samtools view -S -b  Fibro_input_filtered.sam >  Fibro_input_filtered.bam /
samtools view -S -b  SRR14827288_filtered.sam >  SRR14827288_filtered.bam</code></pre>

#### Normalisation

<pre><code>samtools view -b -s 1.878 Fibro_input_filtered.bam > Fibro_input_filtered_norm.bam</code></pre>

#### MACS2
<pre><code>macs2 callpeak -t SRR14827288_filtered.bam -c Fibro_input_filtered_norm.bam -n /home/dvoyashov/papka_for_danya_lox/results/macs/second/Fibro_peaks -g 1.87e+9 --bdg -q 0.05 -f BAM</code></pre>

#### WIG convertation

<pre><code>perl /home/dvoyashov/papka_for_danya_lox/soft/bedgraph_to_wig.pl --bedgraph Fibro_peaks_treat_pileup.bdg --wig /home/dvoyashov/papka_for_danya_lox/results/wig/Fibro_peaks.wig --step 50 \
perl /home/dvoyashov/papka_for_danya_lox/soft/bedgraph_to_wig.pl --bedgraph Fibro_peaks_control_lambda.bdg --wig /home/dvoyashov/papka_for_danya_lox/results/wig/Fibro_peaks_input.wig --step 50</code></pre>

#### BIGWIG convertation

<pre><code>/home/dvoyashov/papka_for_danya_lox/soft/bedGraphToBigWig /home/dvoyashov/papka_for_danya_lox/results/macs/second/Fibro_peaks_control_lambda.bdg mm39.chrom.sizes Fibro_RUNX2_input2.bigwig</code></pre>

#### Deeptools

<pre><code>computeMatrix reference-point --referencePoint TSS \
-b 4000 -a 4000 \
-R /home/dvoyashov/papka_for_danya_lox/results/deepanal/Fibro_stat.bed \
-S /home/dvoyashov/papka_for_danya_lox/results/deepanal/Fibro_RUNX2.bigwig \
--skipZeros \
-o /home/dvoyashov/papka_for_danya_lox/results/deepanalwt_matrix.gz \
-p 6</code></pre>










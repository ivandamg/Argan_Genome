# Argan_Genome
Analysis of the argan genome

## 1. QC&cleaning of PB reads:

        fastqc & multiqc defaults

## 2. for all 3 PB runs: PB67_1,  PB67_2, PB109

        fastp -i pb67_2.fastq.gz -W 10 -5 -3 -M 10 -l 3000 -G -A -w 20 -o PB67_2_clean.fq

## 3. combine clean reads with cat

        cat *_clean.fq >> Combined_clean.fq 


##  4. HiC clean Illumina reads

        sbatch --partition=pibu_el8 --job-name=fastp --time=4-20:00:00 --mem-per-cpu=64G --ntasks=12 --cpus-per-task=1 --output=fastp.out --error=fastp.error --mail-type=END,FAIL --wrap "module load fastp;cd /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC; fastp -i Argon1_2_R1.fastq.gz -I Argon1_2_R2.fastq.gz -o Argon1_R1_trim.fastq.gz -O Argon1_R2_trim.fastq.gz -j Argan1_fastp.json -h Argan1_fastp.html --thread 16 --trim_poly_g -f 10 -F 10 -l 140"

## 5. Map reads to mitochondrion and recover unmapped reads

         sbatch --partition=pibu_el8 --job-name=mito --time=0-10:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=mito.out --error=mito.error --mail-type=END,FAIL --wrap "module load minimap2;module load SAMtools;module load BEDTools; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/02_Mitochondria; minimap2 -d Mitochondrion_Argan.mmi Argan_mitochondrion.fasta; minimap2 -ax map-pb Mitochondrion_Argan.mmi /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq.gz > Aligned_mitochondria.sam; samtools view -bS Aligned_mitochondria.sam > Aligned_mitochondria.bam; samtools sort Aligned_mitochondria.bam -o Aligned_mitochondria.sorted.bam; samtools index Aligned_mitochondria.sorted.bam; samtools view -b -f 4 Aligned_mitochondria.sorted.bam > unmapped_reads.bam; bedtools bamtofastq -i unmapped_reads.bam -fq unmapped_reads_mito.fq; gzip unmapped_reads_mito.fq"

## 6. Map reads to chloroplast and recover unmapped reads

         sbatch --partition=pibu_el8 --job-name=mito --time=0-10:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=mito.out --error=mito.error --mail-type=END,FAIL --wrap "module load minimap2;module load SAMtools; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast; minimap2 -d chloroplast_Argan.mmi Chloroplast_Argan.fasta; minimap2 -ax map-pb chloroplast_Argan.mmi /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/02_Mitochondria/unmapped_reads_mito.fq.gz > Aligned_chloroplast.sam; samtools view -bS Aligned_chloroplast.sam > Aligned_chloroplast.bam; samtools sort Aligned_chloroplast.bam -o Aligned_chloroplast.sorted.bam; samtools index Aligned_chloroplast.sorted.bam; samtools view -b -f 4 Aligned_chloroplast.sorted.bam > unmapped_reads_chloroplast.bam; bedtools bamtofastq -i unmapped_reads_chloroplast.bam -fq unmapped_reads_mitochloroplast.fq; gzip unmapped_reads_mitochloroplast.fq"

## 7. Jellyfish + GenomeScope

        sbatch --partition=pibu_el8 --job-name=jelly --time=0-02:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=jelly.out --error=jelly.error --mail-type=END,FAIL --wrap "module load Jellyfish; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast; jellyfish count -C -m 21 -s 1000000000 -t 10 unmapped_reads_mitochloroplast.fq.gz -o unmapped_reads_mitochloroplast.jf"

        sbatch --partition=pibu_el8 --job-name=jelly --time=0-02:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=jelly.out --error=jelly.error --mail-type=END,FAIL --wrap "module load Jellyfish; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast;jellyfish histo -t 10 unmapped_reads_mitochloroplast.jf > unmapped_reads_mitochloroplast.histo"

        GEnomeScope at http://genomescope.org/

## 7. hifiasm hifi reads


        sbatch --partition=pibu_el8 --job-name=hifiasm --time=1-20:00:00 --mem-per-cpu=64G --ntasks=12 --cpus-per-task=1 --output=hifiasm1.out --error=hifiasm1.error --mail-type=END,FAIL --wrap "module load hifiasm; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/04_hifiASM; hifiasm -o Assembly_v4 -t 32 --hom-cov 50 /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast/unmapped_reads_mitochloroplast.fq"

        #Convert to fasta
        awk '/^S/{print ">"$2;print $3}' Assembly_v4.bp.hap1.p_ctg.gfa > Assembly_v4.bp.hap1.p_ctg.fa; awk '/^S/{print ">"$2;print $3}' Assembly_v4.bp.hap2.p_ctg.gfa > Assembly_v4.bp.hap2.p_ctg.fa; awk '/^S/{print ">"$2;print $3}' Assembly_v4.bp.p_ctg.gfa > Assembly_v4.bp.p_ctg.fa

## 8. HiCUP: align the reads against the assembly and filter out artefacts


2. Create reference on hap1

        sbatch --partition=pibu_el8 --job-name=HiCUP --time=0-20:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=hicup1.out --error=hicup1.error --mail-type=END,FAIL --wrap "module load Bowtie2;module load R; bowtie2-build /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/04_hifiASM/ Hap1; /home/imateusgonzalez/00_Software/HiCUP-0.9.2/hicup_digester --genome Human_GRCh37 –re1 ^GATC,DpnII:G^ANTC,HinfI *.fa; 

hicup

## 9. YAHS:  Scaffolding, join gaps and re-orient sequences

         
## 5. hybrid assembly hifi + HiC


        sbatch --partition=pibu_el8 --job-name=hifiasm --time=4-20:00:00 --mem-per-cpu=128G --ntasks=12 --cpus-per-task=1 --output=hifiasm1.out --error=hifiasm1.error --mail-type=END,FAIL --wrap "module load hifiasm; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/03_hifiASM_hybrid; hifiasm -o Assembly_v3 -t 32 --h1 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_R1_trim.fastq.gz --h2 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_R2_trim.fastq.gz --hom-cov 60 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq.gz "

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


        sbatch --partition=pibu_el8 --job-name=hifiasm7 --time=1-20:00:00 --mem-per-cpu=64G --ntasks=12 --cpus-per-task=1 --output=hifiasm1.out --error=hifiasm1.error --mail-type=END,FAIL --wrap "module load hifiasm; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/07_hifiASMcov50; hifiasm -o Argan_v7cov50 -t 32 --hom-cov 50 Combined_clean.fq"


                 awk '/^S/{print ">"$2;print $3}' Argan_v7cov50.bp.hap1.p_ctg.gfa > Argan_v7cov50.bp.hap1.p_ctg.fa; awk '/^S/{print ">"$2;print $3}' Argan_v7cov50.bp.hap2.p_ctg.gfa > Argan_v7cov50.bp.hap2.p_ctg.fa; awk '/^S/{print ">"$2;print $3}' Argan_v7cov50.bp.p_ctg.gfa > Argan_v7cov50.bp.p_ctg.fa
                 
        #Convert to fasta
        awk '/^S/{print ">"$2;print $3}' Assembly_v4.bp.hap1.p_ctg.gfa > Assembly_v4.bp.hap1.p_ctg.fa; awk '/^S/{print ">"$2;print $3}' Assembly_v4.bp.hap2.p_ctg.gfa > Assembly_v4.bp.hap2.p_ctg.fa; awk '/^S/{print ">"$2;print $3}' Assembly_v4.bp.p_ctg.gfa > Assembly_v4.bp.p_ctg.fa


## 8. Evaluate assemblies with BUSCO

        sbatch --partition=pibu_el8 --job-name=hap1Busco --time=0-10:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=BuscoHap1.out --error=BuscoHap1.error --mail-type=END,FAIL --wrap "module load BUSCO; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/07_hifiASMcov50; busco -o BUSCOhap1 -i Assembly_v4.bp.hap1.p_ctg.fa -l eudicots_odb10 --cpu 12 -m genome -f"
        sbatch --partition=pibu_el8 --job-name=hap2Busco --time=0-10:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=BuscoHap2.out --error=BuscoHap2.error --mail-type=END,FAIL --wrap "module load BUSCO; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/07_hifiASMcov50; busco -o BUSCOhap2 -i Assembly_v4.bp.hap2.p_ctg.fa -l eudicots_odb10 --cpu 12 -m genome -f"

## 9. Evaluate assemblies with QUAST

                sbatch --partition=pibu_el8 --job-name=hap1QUAST --time=0-10:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=hap1QUAST.out --error=hap1QUAST.error --mail-type=END,FAIL --wrap "module load QUAST; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/07_hifiASMcov50; quast Argan_v7cov50_hap1.fa -o QUAST_hap1 ;quast Argan_v7cov50_hap2.fa -o QUAST_hap2"

## 8. HiCUP: align the reads against the assembly and filter out artefacts


2. Create reference on hap1

        sbatch --partition=pibu_el8 --job-name=HiCUP --time=0-03:00:00 --mem-per-cpu=12G --ntasks=12 --cpus-per-task=1 --output=hicup1.out --error=hicup1.error --mail-type=END,FAIL --wrap "module load Bowtie2;module load R; bowtie2-build /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/07_hifiASMcov50/Argan_v7cov50_hap1.fa Hap1; /home/imateusgonzalez/00_Software/HiCUP-0.9.2/hicup_digester --genome Hap1 --arima Argan_v7cov50_hap1.fa" 

        sbatch --partition=pibu_el8 --job-name=HiCUP2 --time=1-13:00:00 --mem-per-cpu=12G --ntasks=24 --cpus-per-task=1 --output=hicup2.out --error=hicup2.error --mail-type=END,FAIL --wrap "module load Bowtie2;module load R;module load SAMtools; /home/imateusgonzalez/00_Software/HiCUP-0.9.2/hicup --config /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/07_hifiASMcov50/HiCUP/HiCUP_Conf.txt --threads 24"

## 9. YAHS:  Scaffolding, join gaps and re-orient sequences

         #/home/imateusgonzalez/00_Software/yahs/yahs contigs.fa hic-to-contigs.bam -o scaffolds.fa --min-contig-len 1000 --min-link 5 --gap-size 500 -t 16

        srun -p pibu_el8 --mem=50G --cpus-per-task=24 --time=24:00:00 ./yahs-1.1/yahs Assembly_v5.bp.hap1.p_ctg.fa  Argon1_R1_2_trim.hicup.bam 

        srun -p pibu_el8 --mem=50G --cpus-per-task=24 --time=24:00:00 ./yahs-1.1/juicer pre -a -o out_JBAT yahs.out.bin yahs.out_scaffolds_final.agp                  Assembly_v5.bp.hap1.p_ctg.fa.fai>out_JBAT.log 2>&1 cat out_JBAT.log  | grep PRE_C_SIZE | awk '{print $2" "$3}' > out_JBAT_chrom.size

        srun -p pibu_el8 --mem=50G --cpus-per-task=24 --time=24:00:00 /home/imateusgonzalez/00_Software/yahs/yahs ../Argan_v7cov50_hap1.fa  Argon1_R1_2_trim.hicup.bam -o Hap1 --min-contig-len 1000 --min-link 5 --gap-size 500 -t 24

        srun -p pibu_el8 --mem=50G --cpus-per-task=24 --time=24:00:00 java -Xms512m -Xmx2048m -jar /home/imateusgonzalez/00_Software/juicer_tools_1.22.01.jar pre -a -o out_JBAT yahs.out.bin yahs.out_scaffolds_final.agp Assembly_v5.bp.hap1.p_ctg.fa.fai>out_JBAT.log 2>&1 cat out_JBAT.log  | grep PRE_C_SIZE | awk '{print $2" "$3}' > out_JBAT_chrom.size

        sbatch --partition=pibu_el8 --job-name=YAHSv5 --time=1-13:00:00 --mem-per-cpu=12G --ntasks=16 --cpus-per-task=1 --output=YAHSv5.out --error=YAHSv5.error --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/05_hifiASMs/02_YAHS; java -Xms512m -Xmx2048m -jar /home/imateusgonzalez/00_Software/juicer_tools_1.22.01.jar pre out_JBAT.txt out_JBAT.hic.part out_JBAT_chrom.size && mv out_JBAT.hic.part out_JBAT.hic"


        sbatch --partition=pibu_el8 --job-name=YAHSv5b --time=1-13:00:00 --mem-per-cpu=512G --ntasks=16 --cpus-per-task=1 --output=YAHSv5b.out --error=YAHSv5b.error --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/05_hifiASMs/02_YAHS; java -Xms512m -Xmx2048m -jar /home/imateusgonzalez/00_Software/juicer_tools_1.22.01.jar post -o out_JBAT out_JBAT.review.assembly out_JBAT.liftover.agp Assembly_v5.bp.hap1.p_ctg.fa"

         
         
## 5. hybrid assembly hifi + HiC


        sbatch --partition=pibu_el8 --job-name=hifiasm --time=4-20:00:00 --mem-per-cpu=128G --ntasks=12 --cpus-per-task=1 --output=hifiasm1.out --error=hifiasm1.error --mail-type=END,FAIL --wrap "module load hifiasm; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/03_hifiASM_hybrid; hifiasm -o Assembly_v3 -t 32 --h1 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_R1_trim.fastq.gz --h2 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_R2_trim.fastq.gz --hom-cov 60 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq.gz "

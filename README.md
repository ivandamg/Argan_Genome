# Argan_Genome
Analysis of the argan genome

## QC&cleaning of PB reads:

        fastqc & multiqc defaults

## for all 3 PB runs: PB67_1,  PB67_2, PB109

        fastp -i pb67_2.fastq.gz -W 10 -5 -3 -M 10 -l 3000 -G -A -w 20 -o PB67_2_clean.fq

## combine clean reads with cat

        cat *_clean.fq >> Combined_clean.fq 


##  HiC clean Illumina reads

        sbatch --partition=pibu_el8 --job-name=fastp --time=4-20:00:00 --mem-per-cpu=64G --ntasks=12 --cpus-per-task=1 --output=fastp.out --error=fastp.error --mail-type=END,FAIL --wrap "module load fastp;cd /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC; fastp -i Argon1_2_R1.fastq.gz -I Argon1_2_R2.fastq.gz -o Argon1_R1_trim.fastq.gz -O Argon1_R2_trim.fastq.gz -j Argan1_fastp.json -h Argan1_fastp.html --thread 16 --trim_poly_g -f 10 -F 10 -l 140"


## hybrid assembly hifi + HiC


        sbatch --partition=pibu_el8 --job-name=hifiasm --time=4-20:00:00 --mem-per-cpu=128G --ntasks=12 --cpus-per-task=1 --output=hifiasm1.out --error=hifiasm1.error --mail-type=END,FAIL --wrap "module load hifiasm; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/03_hifiASM_hybrid; hifiasm -o Assembly_v3 -t 32 --h1 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_2_R1.fastq.gz --h2 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_2_R2.fastq.gz --hom-cov 60 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq.gz "

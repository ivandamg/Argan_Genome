# Argan_Genome
Analysis of the argan genome


## hybrid assembly hifi + HiC


sbatch --partition=pibu_el8 --job-name=minimap1 --time=4-20:00:00 --mem-per-cpu=128G --ntasks=12 --cpus-per-task=1 --output=hifiasm1.out --error=hifiasm1.error --mail-type=END,FAIL --wrap "module load hifiasm; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/03_hifiASM_hybrid; hifiasm -o Assembly_v3 -t 32 --h1 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_2_R1.fastq.gz --h2 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/02_HiC/Argon1_2_R2.fastq.gz --hom-cov 60 /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq.gz "

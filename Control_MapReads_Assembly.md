# Map reads to assembly 

## Map reads to control how reads mapped to different regions

1. map reads with minimap2
2. Sort and compress to bam with samtools

Haplotig 1

        sbatch --partition=pibu_el8 --job-name=minimap1 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=minimap1.out --error=minimap1.error --mail-type=END,FAIL --wrap "module load minimap2; cd /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/04_CleanReads_VPGassembly; minimap2 -ax map-hifi Hap1_Galaxy233.fasta /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq > CombinedClean_VGP_hap1.sam;module load SAMtools;samtools sort -o CombinedClean_VGP_hap1.bam CombinedClean_VGP_hap1.sam; samtools index CombinedClean_VGP_hap1.bam"
Haplotig 2
  
        sbatch --partition=pibu_el8 --job-name=minimap1 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=minimap1.out --error=minimap1.error --mail-type=END,FAIL --wrap "module load minimap2; cd /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/04_CleanReads_VPGassembly; minimap2 -ax map-hifi Hap2_Galaxy308.fasta /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq > CombinedClean_VGP_hap2.sam;module load SAMtools;samtools sort -o CombinedClean_VGP_hap2.bam CombinedClean_VGP_hap2.sam; samtools index CombinedClean_VGP_hap2.bam"
        
Then we can download and view with IGVview

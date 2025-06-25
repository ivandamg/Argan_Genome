# Map reads to assembly 

## Map reads to control how reads mapped to different regions

1. map reads with minimap2
2. Sort and compress to bam with samtools

Haplotig 1

        sbatch --partition=pibu_el8 --job-name=minimap1 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=minimap1.out --error=minimap1.error --mail-type=END,FAIL --wrap "module load minimap2/2.20-GCCcore-10.3.0; cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/06_RemapReads/Hap1; minimap2 -ax map-hifi /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/01_Assembly/01_hap1/S_spinosum_hap1.fa /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/01_Assembly/Combined_clean.fq > CombinedClean_hap1.sam; module load SAMtools;samtools sort -o CombinedClean_hap1.bam CombinedClean_hap1.sam; samtools index CombinedClean_hap1.bam"

        
Haplotig 2
  
        sbatch --partition=pibu_el8 --job-name=minimap2 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=minimap2.out --error=minimap2.error --mail-type=END,FAIL --wrap "module load minimap2/2.20-GCCcore-10.3.0; cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/06_RemapReads/Hap2; minimap2 -ax map-hifi /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/01_Assembly/02_hap2/S_spinosum_hap2.fa /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/01_Assembly/Combined_clean.fq > CombinedClean_hap2.sam; module load SAMtools;samtools sort -o CombinedClean_hap2.bam CombinedClean_hap2.sam; samtools index CombinedClean_hap2.bam"
        
Then we can download and view with IGVview

## 2. Make coverage file with bed tools

sbatch --partition=pibu_el8 --job-name=Bedtools --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=Bedtools.out --error=Bedtools.error --mail-type=END,FAIL --wrap "module load BEDTools/2.30.0-GCC-10.3.0;cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/06_RemapReads/Hap1; bedtools coverage -a InvChr2_RegionsInterest.bed -b CombinedClean_hap1.bam > Cov_InvChr2_RegionsInterest.txt"


sbatch --partition=pibu_el8 --job-name=samtools --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools.out --error=samtools.error --mail-type=END,FAIL --wrap "module load SAMtools/1.13-GCC-10.3.0; cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/06_RemapReads/Hap1; samtools depth CombinedClean_hap1.bam -b InvChr2_v2.bed > Coverage_InvChr2_Inversion.txt"

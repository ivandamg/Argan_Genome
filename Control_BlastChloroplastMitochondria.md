# Chloroplast and mitochondria missassemblies

## Use blast to identify if mitochondria or chloroplast genes are present in the assembly.

1. make database with genome assemblies
2. blast chloroplast and protein gene sequences
3. evaluate high match % identity, missmatch, length, etc.


Haplotig 1

        sbatch --partition=pibu_el8 --job-name=Samtools1 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools1.out --error=samtools1.error --mail-type=END,FAIL --wrap "module load SAMtools; module load BEDTools; cd /data/projects/p782_RNA_seq_Argania_spinosa/23_CoverageAnalysis_Haplotigs; samtools faidx Aspinosa_hap1.fa; bedtools makewindows -g Aspinosa_hap1.fa.fai -w 100000 > windows_Aspinosa_hap1.bed; bedtools coverage -a windows_Aspinosa_hap1.bed -b /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/03_CleanReads_Haplotigs/CombinedClean_Aspinosa_hap1.bam > coverage_Windows_Aspinosa_hap1.txt"

Haplotig 2
  
        sbatch --partition=pibu_el8 --job-name=Samtool2 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools2.out --error=samtools2.error --mail-type=END,FAIL --wrap "module load SAMtools; module load BEDTools; cd /data/projects/p782_RNA_seq_Argania_spinosa/23_CoverageAnalysis_Haplotigs; samtools faidx Aspinosa_hap2.fa; bedtools makewindows -g Aspinosa_hap2.fa.fai -w 100000 > windows_Aspinosa_hap2.bed; bedtools coverage -a windows_Aspinosa_hap2.bed -b /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/03_CleanReads_Haplotigs/CombinedClean_Aspinosa_hap2.bam > coverage_Windows_Aspinosa_hap2.txt"


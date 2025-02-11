# Coverage analysis

## Divide the genome in windows to identify if some segments display unexpectedly high coverage

1. index the genome assembly with samtools faidx
2. create a bed file dividing the genome assembly in different windows bedtools makewindows -w 100000
3. coverage per window bedtools coverage -a
4. Select the windows with the highgest coverage with awk.
5.  evaluate with IGView

Haplotig 1

        sbatch --partition=pibu_el8 --job-name=Samtools1 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools1.out --error=samtools1.error --mail-type=END,FAIL --wrap "module load SAMtools; module load BEDTools; cd /data/projects/p782_RNA_seq_Argania_spinosa/23_CoverageAnalysis_Haplotigs; samtools faidx Aspinosa_hap1.fa; bedtools makewindows -g Aspinosa_hap1.fa.fai -w 100000 > windows_Aspinosa_hap1.bed; bedtools coverage -a windows_Aspinosa_hap1.bed -b /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/03_CleanReads_Haplotigs/CombinedClean_Aspinosa_hap1.bam > coverage_Windows_Aspinosa_hap1.txt"

Haplotig 2
  
        sbatch --partition=pibu_el8 --job-name=Samtool2 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools2.out --error=samtools2.error --mail-type=END,FAIL --wrap "module load SAMtools; module load BEDTools; cd /data/projects/p782_RNA_seq_Argania_spinosa/23_CoverageAnalysis_Haplotigs; samtools faidx Aspinosa_hap2.fa; bedtools makewindows -g Aspinosa_hap2.fa.fai -w 100000 > windows_Aspinosa_hap2.bed; bedtools coverage -a windows_Aspinosa_hap2.bed -b /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/03_CleanReads_Haplotigs/CombinedClean_Aspinosa_hap2.bam > coverage_Windows_Aspinosa_hap2.txt"


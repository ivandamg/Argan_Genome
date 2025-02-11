# Read depth analysis

## Divide the genome in windows to identify if some segments display unexpectedly high read depth

1. index the genome assembly with samtools faidx
2. create a bed file dividing the genome assembly in different windows bedtools makewindows -w 100000
3. coverage per window bedtools coverage -a
4. Select the windows with the highgest coverage with awk.
5.  evaluate with IGView

Haplotig 1

        sbatch --partition=pibu_el8 --job-name=Samtools1 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools1.out --error=samtools1.error --mail-type=END,FAIL --wrap "module load SAMtools; module load BEDTools; cd /data/projects/p782_RNA_seq_Argania_spinosa/23_CoverageAnalysis_Haplotigs; samtools faidx Aspinosa_hap1.fa; bedtools makewindows -g Aspinosa_hap1.fa.fai -w 100000 > windows_Aspinosa_hap1.bed; bedtools coverage -a windows_Aspinosa_hap1.bed -b /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/03_CleanReads_Haplotigs/CombinedClean_Aspinosa_hap1.bam > coverage_Windows_Aspinosa_hap1.txt"

Haplotig 2
  
        sbatch --partition=pibu_el8 --job-name=Samtool2 --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools2.out --error=samtools2.error --mail-type=END,FAIL --wrap "module load SAMtools; module load BEDTools; cd /data/projects/p782_RNA_seq_Argania_spinosa/23_CoverageAnalysis_Haplotigs; samtools faidx Aspinosa_hap2.fa; bedtools makewindows -g Aspinosa_hap2.fa.fai -w 100000 > windows_Aspinosa_hap2.bed; bedtools coverage -a windows_Aspinosa_hap2.bed -b /data/projects/p782_RNA_seq_Argania_spinosa/22_MapingLongReads/03_CleanReads_Haplotigs/CombinedClean_Aspinosa_hap2.bam > coverage_Windows_Aspinosa_hap2.txt"

This results in a 6 column file, where the 4th columns represent the number of reads that are overlapping the given window. It shows if a window have unexpectedly high number of reads.

# Results Hap1

                scaffold_1      59000000        59100000        10258   100000  100000  1.0000000
                scaffold_1      59100000        59200000        24170   100000  100000  1.0000000
                scaffold_1      59200000        59300000        51444   100000  100000  1.0000000
                scaffold_1      59300000        59400000        35552   100000  100000  1.0000000
                scaffold_1      107400000       107500000       10989   100000  100000  1.0000000
                scaffold_2      1800000         1900000         14282   100000  100000  1.0000000
                scaffold_2      26600000        26700000        27141   100000  100000  1.0000000
                scaffold_2      26700000        26800000        25870   100000  100000  1.0000000
                scaffold_2      59700000        59800000        33064   100000  100000  1.0000000
                scaffold_3      37100000        37200000        12305   100000  100000  1.0000000
                scaffold_3      37200000        37300000        13655   100000  100000  1.0000000
                scaffold_3      43800000        43900000        15142   100000  100000  1.0000000
                scaffold_3      43900000        44000000        26427   100000  100000  1.0000000
                scaffold_3      44000000        44100000        19504   99800   100000  0.9980000
                scaffold_3      44100000        44200000        16602   100000  100000  1.0000000
                scaffold_3      46600000        46700000        11956   99800   100000  0.9980000
                scaffold_3      46700000        46800000        50953   100000  100000  1.0000000
                scaffold_3      46800000        46900000        43628   100000  100000  1.0000000
                scaffold_3      58000000        58100000        11879   100000  100000  1.0000000
                scaffold_4      25600000        25700000        11513   100000  100000  1.0000000
                scaffold_4      27200000        27300000        13165   100000  100000  1.0000000
                scaffold_5      19400000        19500000        11447   100000  100000  1.0000000
                scaffold_6      1200000         1300000         13477   100000  100000  1.0000000
                scaffold_6      6500000         6600000         12232   100000  100000  1.0000000
                scaffold_6      24000000        24100000        23121   100000  100000  1.0000000
                scaffold_6      24100000        24200000        210264  100000  100000  1.0000000
                scaffold_6      24200000        24300000        49360   99794   100000  0.9979400
                scaffold_7      11800000        11900000        27369   100000  100000  1.0000000
                scaffold_7      21600000        21700000        23156   100000  100000  1.0000000
                scaffold_8      1700000         1800000         15185   100000  100000  1.0000000
                scaffold_8      4900000         5000000         16617   100000  100000  1.0000000
                scaffold_9      18400000        18500000        13204   100000  100000  1.0000000
                scaffold_11     17900000        18000000        10385   100000  100000  1.0000000
                scaffold_11     18000000        18100000        11557   100000  100000  1.0000000
                scaffold_13     0               100000          17641   100000  100000  1.0000000
                scaffold_13     200000          300000          28733   100000  100000  1.0000000
                scaffold_13     400000          500000          13541   100000  100000  1.0000000

# Conclusion

The analysis shows that 36 windows display an unusual high number of reads (>10000). This could mean that these regions contain overrepresented sequences that could be from chloroplast or mitochondria.


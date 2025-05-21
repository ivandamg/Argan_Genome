# BUsco eudicotyledons v12

## hap1
sbatch --partition=pibu_el8 --job-name=hap1Busco --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=BuscoHap1.out --error=BuscoHap1.error --mail-type=END,FAIL --wrap "module load BUSCO; cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/05_BUSCO/01_hap1; busco -o OUTPUT -i S_spinosum_hap1.fa -l eudicots_odb10 --cpu 12 -m genome -f"


## hap2
sbatch --partition=pibu_el8 --job-name=hap2Busco --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=BuscoHap2.out --error=BuscoHap2.error --mail-type=END,FAIL --wrap "module load BUSCO; cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/05_BUSCO/01_hap1; busco -o OUTPUT -i S_spinosum_hap2.fa -l eudicots_odb10 --cpu 12 -m genome -f"

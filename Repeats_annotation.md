# TE-tools

pull the image


      apptainer shell docker://dfam/tetools:latest


Now inside the container, can work on TE-tools


BuildDatabase -name hap1_DB -engine ncbi hap1.fasta 
RepeatModeler -database hap1_DB -engine ncbi -pa 12


or in cluster

1. Make db

              sbatch --partition=pibu_el8 --job-name=TEtools1 --time=0-01:00:00 --mem-per-cpu=10G --ntasks=12 --cpus-per-task=1 --output=hap1db.out --error=hap1db.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/30_FinalAssemblyPaper/03_RepeatMasker/01_hap1; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest BuildDatabase -name hap1_DB -engine ncbi Aspinosa_hap1.fa"


2. Repeat modeler

              sbatch --partition=pibu_el8 --job-name=TEtools --time=7-10:00:00 --mem-per-cpu=10G --ntasks=48 --cpus-per-task=1 --output=hap1RepeatMod.out --error=hap1RepeatMod.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/30_FinalAssemblyPaper/03_RepeatMasker/01_hap1; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatModeler -database hap1_DB -engine ncbi -threads 48 -LTRStruct "

2b. continue Repeat modeler

              sbatch --partition=pibu_el8 --job-name=TEtools --time=7-10:00:00 --mem-per-cpu=10G --ntasks=12 --cpus-per-task=1 --output=hap1RepeatMod.out --error=hap1RepeatMod.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/21_GenomeAnnotation/01_RepeatMasker/; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatModeler -database hap1_DB -engine ncbi -threads 12 -recoverDir /data/projects/p782_RNA_seq_Argania_spinosa/21_GenomeAnnotation/01_RepeatMasker/RM_133275.MonApr151121242024 -LTRStruct"

if need to dot the same for hap2 the folder is /data/projects/p782_RNA_seq_Argania_spinosa/21_GenomeAnnotation/01_RepeatMasker/RM_173348.MonApr151121572024


3. Repeat masker

              sbatch --partition=pibu_el8 --job-name=RMaskHap1 --time=0-10:00:00 --mem-per-cpu=10G --ntasks=48 --cpus-per-task=1 --output=hap1RepeatMask.out --error=hap1RepeatMask.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/30_FinalAssemblyPaper/03_RepeatMasker/01_hap1; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatMasker -lib hap1_DB-families.fa -xsmall -pa 48 -gff -dir MaskerOutput Aspinosa_hap1.fa"

                sbatch --partition=pibu_el8 --job-name=RMaskHap2 --time=0-10:00:00 --mem-per-cpu=10G --ntasks=48 --cpus-per-task=1 --output=hap2RepeatMask.out --error=hap2RepeatMask.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/30_FinalAssemblyPaper/03_RepeatMasker/02_hap2; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatMasker -lib hap2_DB-families.fa -xsmall -pa 48 -gff -dir MaskerOutput Aspinosa_hap2.fa"


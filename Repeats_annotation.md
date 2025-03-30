# TE-tools

pull the image


      apptainer shell docker://dfam/tetools:latest


Now inside the container, can work on TE-tools


BuildDatabase -name hap1_DB -engine ncbi hap1.fasta 
RepeatModeler -database hap1_DB -engine ncbi -pa 12


or in cluster

1. Make db

              sbatch --partition=pibu_el8 --job-name=TEtools1 --time=0-01:00:00 --mem-per-cpu=10G --ntasks=12 --cpus-per-task=1 --output=hap1db.out --error=hap1db.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/02_RepeatAnnotation/01_hap1; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest BuildDatabase -name hap1_DB S_spinosum_hap1.fa"

                  sbatch --partition=pibu_el8 --job-name=TEtools2 --time=0-01:00:00 --mem-per-cpu=10G --ntasks=12 --cpus-per-task=1 --output=hap2db.out --error=hap2db.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/02_RepeatAnnotation/02_hap2; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest BuildDatabase -name hap2_DB S_spinosum_hap2.fa"


2. Repeat modeler

              sbatch --partition=pibu_el8 --job-name=TEtools --time=7-10:00:00 --mem-per-cpu=10G --ntasks=48 --cpus-per-task=1 --output=hap1RepeatMod.out --error=hap1RepeatMod.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/02_RepeatAnnotation/01_hap1; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatModeler -database hap1_DB -threads 48 -LTRStruct"

               sbatch --partition=pibu_el8 --job-name=TEtools --time=7-10:00:00 --mem-per-cpu=10G --ntasks=48 --cpus-per-task=1 --output=hap2RepeatMod.out --error=hap2RepeatMod.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/02_RepeatAnnotation/02_hap2; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatModeler -database hap2_DB -threads 48 -LTRStruct"

2b. continue Repeat modeler

              sbatch --partition=pibu_el8 --job-name=TEtools --time=7-10:00:00 --mem-per-cpu=10G --ntasks=12 --cpus-per-task=1 --output=hap1RepeatMod.out --error=hap1RepeatMod.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/21_GenomeAnnotation/01_RepeatMasker/; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatModeler -database hap1_DB -engine ncbi -threads 12 -recoverDir /data/projects/p782_RNA_seq_Argania_spinosa/21_GenomeAnnotation/01_RepeatMasker/RM_133275.MonApr151121242024 -LTRStruct"


3. Repeat masker

              sbatch --partition=pibu_el8 --job-name=RMaskHap1 --time=0-10:00:00 --mem-per-cpu=10G --ntasks=48 --cpus-per-task=1 --output=hap1RepeatMask.out --error=hap1RepeatMask.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/02_RepeatAnnotation/01_hap1; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatMasker -lib hap1_DB-families.fa -xsmall -pa 48 -gff -dir MaskerOutput S_spinosum_hap1.fa"

                sbatch --partition=pibu_el8 --job-name=RMaskHap2 --time=1-10:00:00 --mem-per-cpu=10G --ntasks=48 --cpus-per-task=1 --output=hap2RepeatMask.out --error=hap2RepeatMask.err --mail-type=END,FAIL --wrap "cd /data/projects/p782_RNA_seq_Argania_spinosa/40_S_spinosum_FinalFinal/02_RepeatAnnotation/02_hap2; export APPTAINER_TMPDIR=/data/users/imateusgonzalez; export APPTAINER_CACHEDIR=/data/users/imateusgonzalez; export APPTAINER_BIND=/data ; apptainer run docker://dfam/tetools:latest RepeatMasker -lib hap2_DB-families.fa -xsmall -pa 48 -gff -dir RepeatMaskerOutput S_spinosum_hap2.fa"


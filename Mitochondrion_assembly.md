# Mitochondria assembly


1. Map reads to Argan mitochondria
2. Recover reads
3. use hifiASM in organelle mode to make the assembly


1. Map to a mitochondria reference:

        sbatch --partition=pibu_el8 --job-name=minimap --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=minimap2.out --error=minimap2.error --mail-type=END,FAIL --wrap "module load minimap2; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/02_Mitochondria; minimap2 -ax asm20 Mitochondrion_Argan.fasta /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq.gz > mitochondria.sam"

2. Extract unmapped vs. mapped reads

        sbatch --partition=pibu_el8 --job-name=samtools --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools.out --error=samtools.error --mail-type=END,FAIL --wrap "module load SAMtools;cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/02_Mitochondria; samtools view -b -F 4 mitochondria.sam > mitochondria_mapped.bam; samtools fastq mitochondria_mapped.bam > mitochondria_hifi.fq"


3b. Assemble chloroplast with getorganelle (in local)

1. Create a Conda Environment for GetOrganel

       module load Anaconda3; conda create -p $HOME/getorganelle_env -c bioconda -c conda-forge getorganelle

2. assemble chloroplast

           conda create -n getorganelle_env; conda activate getorganelle_env; module load Anaconda3; conda install -c bioconda getorganelle;

        get_organelle_from_reads.py -1 cp_hifi_R1.fq.gz -o cp_output -R 10 -k 21,45,65,85,105 -F embplant_pt

   module load BLAST+; module load Bowtie2;module load SPAdes; get_organelle_config.py -a all

   sbatch --partition=pibu_el8 --job-name=mitochondria --time=2-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=mitochondria.out --error=mitochondria.error --mail-type=END,FAIL --wrap "module load BLAST+; module load Bowtie2;module load SPAdes; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast/GetOrganelle; get_organelle_from_reads.py -u /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/02_Mitochondria/mitochondria_hifi.fq -o /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/02_Mitochondria/mitochondria_output -R 10 -k 21,45,65,85,105 -t 12 -F embplant_mt --continue"


4. assemble mitochondria

        source activate $HOME/getorganelle_env; get_organelle_from_reads.py -1 mt_hifi_R1.fq.gz -2 mt_hifi_R2.fq.gz -o mt_output -R 10 -k 21,45,65,85,105 -F embplant_mt

# Mitochondria assembly


1. Map reads to Argan chloroplast 
2. Recover reads
3. use hifiASM in organelle mode to make the assembly


1. Map to a chloroplast reference:

        sbatch --partition=pibu_el8 --job-name=minimap --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=minimap2.out --error=minimap2.error --mail-type=END,FAIL --wrap "module load minimap2; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast; minimap2 -ax asm20 Chloroplast_Argan.fasta /data/projects/p782_RNA_seq_Argania_spinosa/03_PreviousGenomeAssemblyAttemps/20_GenomeAssembly/01_Hifi/Combined_clean.fq.gz > chloroplast.sam"

2. Extract unmapped vs. mapped reads

        sbatch --partition=pibu_el8 --job-name=samtools --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=samtools.out --error=samtools.error --mail-type=END,FAIL --wrap "module load SAMtools;cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast; samtools view -b -F 4 chloroplast.sam > chloroplast_mapped.bam; samtools fastq chloroplast_mapped.bam > chloroplast_hifi.fq.gz"

3. assemble chloroplast with hifiASM

       sbatch --partition=pibu_el8 --job-name=hifiasmcp --time=0-10:00:00 --mem-per-cpu=50G --ntasks=32 --cpus-per-task=1 --output=hifiasmcp.out --error=hifiasmcp.error --mail-type=END,FAIL --wrap "module load hifiasm; hifiasm -o chloroplast_assembly -t 32 chloroplast_hifi.fq"


3b. Assemble chloroplast with getorganelle (in local)

1. Create a Conda Environment for GetOrganel

       module load Anaconda3; conda create -p $HOME/getorganelle_env -c bioconda -c conda-forge getorganelle

2. assemble chloroplast

           conda create -n getorganelle_env; conda activate getorganelle_env; module load Anaconda3; conda install -c bioconda getorganelle;

        get_organelle_from_reads.py -1 cp_hifi_R1.fq.gz -o cp_output -R 10 -k 21,45,65,85,105 -F embplant_pt

   module load BLAST+; module load Bowtie2;module load SPAdes; get_organelle_config.py -a all

   sbatch --partition=pibu_el8 --job-name=getorganelle --time=0-10:00:00 --mem-per-cpu=50G --ntasks=12 --cpus-per-task=1 --output=getorganelle.out --error=getorganelle.error --mail-type=END,FAIL --wrap "module load BLAST+; module load Bowtie2;module load SPAdes; cd /data/projects/p782_RNA_seq_Argania_spinosa/200_v3Assembly/01_Chloroplast/GetOrganelle; get_organelle_from_reads.py -u ../chloroplast_hifi.fq -o chloroplast_output -R 10 -k 21,45,65,85,105 -F embplant_pt --continue"


4. assemble mitochondria

        source activate $HOME/getorganelle_env; get_organelle_from_reads.py -1 mt_hifi_R1.fq.gz -2 mt_hifi_R2.fq.gz -o mt_output -R 10 -k 21,45,65,85,105 -F embplant_mt

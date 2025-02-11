# Chloroplast and mitochondria missassemblies

## Use blast to identify if mitochondria or chloroplast genes are present in the assembly.

1. make database with genome assemblies
2. blast chloroplast and protein gene sequences
3. evaluate high match % identity, missmatch, length, etc.

command: tblastn -db db/ArganHap2_db -outfmt 6 -evalue 1e-100 -show_gis -num_alignments 10 -max_hsps 20 -num_threads 8 -out Blast_ChloroplastArgan_ArganHap2.xml -query ArganChloroplast_MK533159_aa.fa


Compare the results of Hap1 and Hap2 to the shea trea and miracle fruit tree


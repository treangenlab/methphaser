# Meth Phaser

Requirement: 
1. pysam>=0.20.0
2. tqdm
3. Need to put the executable `methphasing` into bashrc for `meth_phaser_parallel` to call it. exact command: export "PATH=/path/to/methphaser/:$PATH"
4. pandas
5. scipy
6. matplotlib

Known Issue:
1. Does not phase reads before the first phased block and after the last phased block
2. Multiprocessing threads should be smaller than the total block number on the chromosome. e.g., MethPhaser cannot use 3 threads to phase 3 blocks, since the number of the gaps among 3 blocks is 2.  
3. MethPhaser sometimes ignore some very short reads (about 0.1% reads). Probably because of pysam. 


## Example on HLA-E, HLA-C region: 
Step 1: Run MethPhaser to get block relationship (will take about 5 minutes)

        ./meth_phaser_parallel -b test_data/HLA.R10.haplotagged.bam -r test_data/GCA_000001405.15_GRCh38_no_alt_analysis_set.chr6.fna -g test_data/LSK.filtered.gtf -vc test_data/HLA.R10.phased.vcf.gz  -o test_data/work -k -1 -ml -2

Step 2: Run post processing script to get modified reads and vcf file (will take about 2 minutes on an SSD)

        ./meth_phaser_post_processing -ib test_data/HLA.R10.haplotagged.bam -if test_data/work/ -ov test_data/output.vcf -ob test_data/output -vc test_data/HLA.R10.phased.vcf.gz 

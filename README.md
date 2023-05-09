# MethPhaser

Requirement: 
 check methphaser.yaml
 
 
        usage: meth_phaser_parallel [-h] -b  -r  -g  -vc  [-vt] [-t] [-ml] [-c] [-a] [-o] [-k]

        methphaser: phase reads based on methlytion informaiton

        optional arguments:
          -h, --help            show this help message and exit
          -vt , --vcf_truth     Truth vcf file for benchmarking
          -t , --threads        threads
          -ml , --max_len       maximum homozygous region length for phasing, default: -1 (ignore the largest homozygous
                                region, centromere), input -2 for not skipping anything
          -c , --cut_off        the minimum percentage of vote to determine a read's haplotype
          -a , --assignment_min
                                minimum assigned read number for ranksum test
          -o , --output_dir     output_directory
          -k , --k_iterations   use at most k iterations, use -1 for unlimited iterations.

        Required arguments:
          -b , --bam_file       input methylation annotated bam file
          -r , --reference      reference genome
          -g , --gtf            gtf file from whatshap visualization
          -vc , --vcf_called    called vcf file from HapCUT2
          
Recomanded phasing flow: 

   ![flowchart drawio](https://github.com/treangenlab/methphaser/assets/13065758/c74e5a1d-1c24-49c0-abe3-5b125f43f7eb)


## Example 
Step 1: Run MethPhaser to get block relationship 

        ./meth_phaser_parallel -b test_data/HLA.R10.haplotagged.bam -r test_data/GCA_000001405.15_GRCh38_no_alt_analysis_set.chr6.fna -g test_data/LSK.filtered.gtf -vc test_data/HLA.R10.phased.vcf.gz  -o test_data/work -k -1 -ml -2

Step 2: Run post processing script to get modified reads and vcf file

        ./meth_phaser_post_processing -ib test_data/HLA.R10.haplotagged.bam -if test_data/work/ -ov test_data/output.vcf -ob test_data/output -vc test_data/HLA.R10.phased.vcf.gz 
        
    
Known Issue:
1. Does not phase reads before the first phased block and after the last phased block
2. Multiprocessing threads should be smaller than the total block number on the chromosome. e.g., MethPhaser cannot use 3 threads to phase 3 blocks, because the number of the gaps among 3 blocks is 2.  
3. MethPhaser sometimes ignore some very short reads (about 0.1% reads). Probably because of pysam. 




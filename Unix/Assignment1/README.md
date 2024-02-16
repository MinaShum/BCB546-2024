## Files in this folder

* `fang_et_al_genotypes.txt`: The input data that contains “Sample_ID”, “JG_OTU”, “Group”, and 983 columns with chromosome names in the header. (only 984 columns of chromosomes and groups are needed for this analysis)
* `snp_position.txt`: Second input dataframe that we only need columns 1, 3, and 4 from. They represent “SNP_ID”, “Chromosome”, and “Position” respectively.
* `transpose.awk`: Used to transpose data structures.
* `maize` and `teosinte` directories contain the final 22 files of sorted and filtered chromosomes.  
* Rest are intermediate files that are all mentioned in the codes of the previous README file.

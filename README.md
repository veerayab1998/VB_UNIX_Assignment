# Directory in the folder
*This read me only contains details on file directory* **Please refer to VB_Unix_Keys.md to read the workflow of the assignment**

## Overview

This repository contains:

- VB_Unix_Keys: a process of describing workflow for both data inspection and processing
- For the purpose of the BCB546 spring 2026 class assignment
- All folder details are listed below

    ### 00_data_files - this folder includes all .txt files or working files provided from the class assignment
    * the **intermediate** folder contains working file of 00_data_file 
        * The output files were cut and sort (fang file and snp file), and pattern matching to separate maize and neosinte genotype files

    ### 01_class_templates - assignment details and .awk command related to the work
    * this folder contains provided transponse.awk script and original UNIX_assignment.md file

    ### 02_transponse_files - this folder includes transponse output files
    * this folder contains orginal genotype files and used transponse.awk
    * the **intermediate** folder contains a output transponsed files of maize and teosinte as well as header files of both
    * the **sort_out** folder contains output files after transponse.awk command

    ### 03_maize_files - this folder includes joined maize, maize header, multiple position, and unknown position files
    * the **increasing** folder contains a output from chr1-10 in increasing order and ? for ?/?
    * the **decreasing** folder contains a output from chr1-10 in decreasing order and - for ?/?

    ### 04_teosinte_files - this folder includes joined teosinte, teosinte header, multiple position, and unknown position files
    * the **increasing** folder contains a output from chr1-10 in increasing order and ? for ?/?
    * the **decreasing** folder contains a output from chr1-10 in decreasing order and - for ?/?

    ### 05_join_files - this folder includes pre-joined files from teosinte, maize, and sorted snp files
    * the **output** folder contains output files after joining the SNP and both the teosinte and maize files
    * _both file 1 and file 2 for each maize and teosinte file in **output** folder are the outcome of 
        * join maize snp > file1
        * join snp maize > file2
        * join teosinte snp > file1
        * join snp teosinte > file2
    * **The process has used file 2 only**

    Thank you,
    Veeraya Bamrung



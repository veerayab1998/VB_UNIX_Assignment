# Unix Assignment - Veeraya Bamrung

## Overview

This document contains a process of describing workflow for both data inspection and processing of **Veeraya Bamrung**:

## _Data Inspection_


* fist, I `mkdir` VB_UNIX_Assignment/00_data_files and  go to the directory and put assisnment's data files in there 

```
$ cd /Users/fan/Downloads/VB_UNIX_Assignment/00_data_files
```
* I observe a detail of the file inside using `ls` and `du`

```
$ ls -lh
$ du -h snp_position.txt fang_et_al_genotypes.txt
```
The result
total 43352
-rw-r--r--@ 1 fan  staff    11M Feb 18 11:26 fang_et_al_genotypes.txt
-rw-r--r--@ 1 fan  staff    81K Feb 18 11:26 snp_position.txt
-rw-r--r--@ 1 fan  staff    11M Feb 18 11:26 transposed_genotypes.txt

* Next, I use the commands below to see how many lines and how large of each files so I can decided on a command to inspect the content in each files

```
$ wc -l snp_position.txt fang_et_al_genotypes.txt
$ wc snp_position.txt fang_et_al_genotypes.txt
```
for wc -l
    984 snp_position.txt
    2783 fang_et_al_genotypes.txt
    3767 total
 for wc   
    984 13198   82763 snp_position.txt
    2783 2744038 11051939 fang_et_al_genotypes.txt
    3767 2757236 11134702 total

* I also inspect how many column is in each file using `awk`
```
$ awk -F $'\t' '{print FILENAME "\t" NF; exit}' snp_position.txt
$ awk -F $'\t' '{print FILENAME "\t" NF; exit}' fang_et_al_genotypes.txt 
```
snp_position.txt	15
fang_et_al_genotypes.txt	986

* since the file has many lines and columns, I use head and tail to inspect the file

```
$ head -n3 snp_position.txt
  tail -n3 snp_position.txt
$ head -n2 fang_et_al_genotypes.txt
  tail -n2 fang_et_al_genotypes.txt
```
For snp file, the contents start at row 3
SNP_ID	cdv_marker_id	Chromosome	Position	alt_pos	mult_positions	amplicon	cdv_map_feature.name	gene	candidate/random	Genaissance_daa_id	Sequenom_daa_id	count_amplicons	count_cmf	count_gene
abph1.20	5976	2	27403404			abph1	AB042260	abph1	candidate	8393	10474	1	1	1
abph1.22	5978	2	27403892			abph1	AB042260	abph1	candidate	8394	10475	0	0	0
zen1.4	3521	unknown	unknown			zen1	CF649098	zen1	candidate	8437	11827	0	0	0
zfl2.6	6463	2	12543294			zfl2	zfl2	zfl2	candidate	8438	11828	1	1	1
zmm3.4	3527	9	16966348			zmm3	Y09301	zmm3	candidate	10104	11829	1	0	1

For fang file
the number of cloumns are huge. so I will not include the result here. However, the file contain ID, Group, JG, chromosome name on the header and the contents are SNPs details, starting at row 2

* I decided not to inspect the middle of the data, however I use "cut" to see only specific header from fang et al

```
$ cut -f1-4 fang_et_al_genotypes.txt | head -n5
```
Sample_ID	JG_OTU	Group	abph1.20
SL-15	T-aust-1	TRIPS	?/?
SL-16	T-aust-2	TRIPS	?/?
SL-11	T-brav-1	TRIPS	?/?
SL-12	T-brav-2	TRIPS	?/?

## _Data Processing_


* first, I make a directory, and I cd .. back to my UNIX main directory

```
$ mkdir -p 01_data_files/intermediate 02_transponse_files 03_maze_files 04_teosinte_files 05_join_files
```
### _Clean the files_
* Since snp content starts at row 2, I use `tail` to specify only content part, cleaned the snp files, and `cut` to only have 3 column that assignment asked, I also `sort` them from column 1 as well
```
$ tail -n +2 snp_position.txt \
  | cut -f1,3,4 \
  | sort -t $'\t' -k1,1 \
  > intermediate/snp_pos_sorted.txt
```
* I cd to intermediate file and inspect if the `sort` command work using `head`
```
$ head -n 10 snp_pos_sorted.txt
```
Fea2.1	4	132735271
Fea2.5	4	132734849
PZA00003.11	6	121839592
PZA00004.2	5	13542371
PZA00005.8	4	243003350
PZA00005.9	4	243003494
PZA00006.13	6	85125129
PZA00006.14	6	85125156
PZA00008.1	2	11924901
PZA00010.5	1	119703101

### _Pattern match for maize and teosinte_
* I `cd ..` to go back to the fang file and I extracted maize and teosinte groups to another output file as well as inspecting the output files 
(The result contents are too long, even with head -n1 so I did not include the inspection result) 
```
$ awk -F $'\t' 'NR==1 || $3 ~ /^(ZMMIL|ZMMLR|ZMMMR)$/' fang_et_al_genotypes.txt > intermediate/maize_genotypes.txt
$ awk -F $'\t' 'NR==1 || $3 ~ /^(ZMPBA|ZMPIL|ZMPJA)$/' fang_et_al_genotypes.txt > intermediate/teosinte_genotypes.txt
```

### _Transponse the maize and teosinte file_
* I go back to my UNIX main directory and copy maize_genotypes.txt and teosinte_genotypes.txt to transpose folder
```
$ cp ./00_data_files/intermediate/* ./02_transponse_files
```

* I go to transponse directory and move snp that comes with previous folder to the join folder
```
$ mv snp_pos_sorted.txt ./05_join_files
```

* I transpose the two files and send it to the intermediate folder inside transpose folder
```
$ awk -f transpose.awk maize_genotypes.txt > intermediate/maize_transposed.txt 
$ awk -f transpose.awk teosinte_genotypes.txt > intermediate/teosinte_transposed.txt
```

* I go to the intermediate folder and inspect the file, and the chromosome starts at row 4 
* So I clean the files by removing the head and sorted the two files
  - in _02_tranponse_files directory
```
$ tail -n +4 intermediate/maize_transposed.txt | sort -t $'\t' -k1,1 > sort_output/maize_snps_sorted.txt
$ tail -n +4 ointermediate/teosinte_transposed.txt | sort -t $'\t' -k1,1 > sort_output/teosinte_snps_sorted.txt
```

* I copy both sort-nonheader files to the join folder
    - I cd to the main directory of the assignment folder
```
$ cp ./02_transponse_files/intermediate/* ./05_join_files
```
### _Join snp and genotype files_
* I go to join the directory, make an output folder, and merge the two files, given that the second file should be maize and teosinte
    _at fist I changed the order making teosinte and maize file come up first, because we choose from pattern in those two, and the order of the file in `join` matters, I did switch between order in this case the file is the same from `wc` command_
```
$ mkdir output
$ join -t $'\t' -1 1 -2 1 snp_pos_sorted.txt teosinte_snps_sorted.txt  > output/teosinte_joined_noheader2.txt
$ join -t $'\t' -1 1 -2 1 snp_pos_sorted.txt maize_snps_sorted.txt  > output/maize_joined_noheader2.txt
```

* I go back to my main directory and copy the join file to the maize and teosinte folder
```
$ cp ./05_join_files/output/maize_joined_noheader2.txt ./03_maze_files 
$ cp ./05_join_files/output/teosinte_joined_noheader2.txt ./04_teosinte_files
```

### since post-join files have no header so I decided to create a header file for both teosinte and maize

* I go to my *intermediate* output from **02_transpone_files** before I cut the header
```
$ cd /Users/fan/Downloads/VB_UNIX_Assignment/02_transponse_files/intermediate
```
* I create a header file by type headers for snp file and cutting header from the maize and teosinte files
```
$ {
  printf "SNP_ID\tChromosome\tPosition\t"
  head -n 1 teosinte_transposed.txt | cut -f2-
} > teosinte_header.txt
```
```
$ {
  printf "SNP_ID\tChromosome\tPosition\t"
  head -n 1 maize_transposed.txt | cut -f2-
} > maize_header.txt
```
* I then copied both header to the 03_maize_files and 04_teosinte_files folders

### I process 03_maize_files folder first

* `cd` to 03_maize_files folder
* increasing order with replacement of ?/? with ?
```
$ for chr in {1..10}; do
  { cat maize_header.txt
    awk -F $'\t' -v c="$chr" '$2==c' maize_joined_noheader2.txt \
      | sort -t $'\t' -k3,3n \
      | sed 's#?/?#?#g'
  } > chr${chr}_increasing.txt
done
```
* decreasing order with replacement of ?/? with -

```
$ for chr in {1..10}; do
  { cat maize_header.txt
    awk -F $'\t' -v c="$chr" '$2==c' maize_joined_noheader2.txt \
      | sort -t $'\t' -k3,3nr \
      | sed 's#?/?#-#g'
  } > chr${chr}_decreasing.txt
done
```
* for unknown position
```
$ { cat maize_header.txt
  awk -F $'\t' '$2=="unknown" || $3=="unknown"' maize_joined_noheader2.txt
} > unknown_positions.txt
```
* for multiple positions

```
$ { cat maize_header.txt
  awk -F $'\t' '
  ( $2=="multiple" || $3=="multiple" )
' maize_joined_noheader2.txt } > multiple_positions.txt
```
* I put increasing files and decreasing files in the separate folder (I am still in the maize folder)

```
$ mkdir increasing decreasing
$ mv chr*_increasing.txt increasing
$ mv chr*_decreasing.txt decreasing
```
_lastly, I inspected a file using cat -te to see if it is not continuous and I also see the file in excel as well_

### After, I process the 04_teosinte_files folder
* `cd` to 04_teosinte_files folder
* increasing order with replacement of ?/? with ?
```
for chr in {1..10}; do
  { cat teosinte_header.txt
    awk -F $'\t' -v c="$chr" '$2==c' teosinte_joined_noheader2.txt \
      | sort -t $'\t' -k3,3n \
      | sed 's#?/?#?#g'
  } > chr${chr}_increasing.txt
done
```
* decreasing order with replacement of ?/? with -

```
for chr in {1..10}; do
  { cat teosinte_header.txt
    awk -F $'\t' -v c="$chr" '$2==c' teosinte_joined_noheader2.txt \
      | sort -t $'\t' -k3,3nr \
      | sed 's#?/?#-#g'
  } > chr${chr}_decreasing.txt
done
```
* for unknown position
```
{ cat teosinte_header.txt
  awk -F $'\t' '$2=="unknown" || $3=="unknown"' teosinte_joined_noheader2.txt
} > unknown_positions.txt
```
* for multiple positions
```
{ cat teosinte_header.txt
  awk -F $'\t' '
  ( $2=="multiple" || $3=="multiple" )
' teosinte_joined_noheader2.txt } > multiple_positions.txt
```

```
mkdir increasing decreasing
mv chr*_increasing.txt increasing
mv chr*_decreasing.txt decreasing
```
_lastly, I inspected a file using cat -te to see if it is not continuous and I also see the file in Excel as well_
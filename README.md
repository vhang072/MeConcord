
# MeConcord
* _MeConcord_ is a method used to investigate local read-level DNA methylation patterns for intermediately methylated regions with bisulfite sequencing data.
* Intermediately methylated regions occupy a significant fraction of the whole genome and are markedly associated with epigenetic regulations or cell-type deconvolution of bulk data. However, these regions show distinct methylation patterns corresponding to different biological mechanisms. Although there have been some metrics developed for investigating these regions, the poor perfor-mance in antagonizing noises limits the utility for distinguishing distinct methylation patterns.
* We proposed a method, _MeConcord_, with two metrics measuring local methylation concordance across reads and CpGs, respectively, with Hamming distance. MeConcord showed the most robust performance in distinguishing distinct methylation patterns (identical, uniform, and disor-dered) compared with other metrics. 

# Installation
* MeConcord is implemented by Python and compatible with both Python 2 and Python 3. 
* Modules of python are required:`pysam`(if the input is .bam files), `pandas`,`numpy`, `scipy`,`multiprocessing`,`matplotlib`(if you use visualization.py).
* The scripts could be downloaded and used directly with command `python *.py -i ....`

# Contact
if there is any questions, please contact Xianglin Zhang (zhangxl.2015@tsinghua.org.cn) or Xiaowo Wang (xwwang@tsinghua.edu.cn)

# Citation
Xianglin Zhang and Xiaowo Wang, MeConcord: a new metric to quantitatively characterize DNA methylation heterogeneity across reads and CpG sites, is accepted in ISMB 2022 and will be published in Bioinformatics

# Usage

## Input
MeConcord currently only accept the output(.bam or converted to .sam) of [Bismark](https://github.com/FelixKrueger/Bismark)

## Run
### 1.Obtaining CpG positions across genome
Usage: `python pre_cpg_pos.py -i hg38.fa -o ./cpg_pos/`
* `i`,  The path to reference sequences (.fa);
* `o`,  The path that you want to deposit the positions of CpG sites, each chromosome has a seperate file;
* `h`,  Help information

### 2.Converting mapped Bam, Sam, Sam.gz files from Bismark to methylation recordings read-by-read
Usage: `python s1_bamToMeRecord.py -i test.bam -o test -c 0`
* `i`,  The path to input files (.bam or .sam or .sam.gz);
* `o`,  Output prefix;
* `c`,  Clipping read ends with such base number (defalut 0); can be used when sequencing quality of read ends is not good. such as -c 5 to remove 5 bases from the both ends of the reads.
* `h`,  Help information

### 3.Spliting the big MeRecord files into small files of each chromosome to reduce memory requirements in the next step
Usage: `python s2_RecordSplit.py -i ./test_ReadsMethyAndMuts.txt -o ./test -g chr1,chr2,chr3,chr4,chr5`
* `i`,  The path to `s1_bamToMeRecord.py` output. ( end with _ReadsMethyAndMuts.txt);
* `o`,  Output prefix;
* `g`,  Chromosomes used; (default chromsome 1-22); chromosomes shoud be seperated by comma;
* `h`,  Help information

### 4. Calculating concordance metrics (NRC, NCC and P-values)
Usage: `python s3_RecordToMeConcord.py -p 4 -i ./test -o ./test -r ./region.bed -c ./cpgpos/ -b 150 -m 600 -z 0 -g chr1,chr2,chr3`
* `i`,  The path to `s2_RecordSplit.py` output, with prefixed file name;
* `p`,  Threads used for parallel computation; default is 4;
* `o`,  Output prefix;
* `r`,  The files with genomic regions for computation, chrom, start, end seperated by tab;
* `c`,  Cpg position folder, output of `pre_cpg_pos.py`;
* `b`,  Bin size (default 150bp);
* `z`,  Whether is the genomic file based on 0; 0 (default) or 1; output is same to input bins; if -r is a bed file, -z should be 1;
* `g`,  Chromosomes used; (default chromsome 1-22); chromosomes shoud be seperated by comma;
* `m`,  Maximum of fragement length in sequencing library(default 600bp for paired-end reads). if there are single-end reads,m should be set as the length of reads, if not sure, default will work for most cases;

### 5. Methylation recordings to methylation matrix (optional)
Usage: `python s4_RecordToMeMatrix.py -i ./test -o ./test -r ./p1.bed -c ./cpgpos/ -m 600 -z 0 -g chr1,chr2`
* `i`,  The path to `s2_RecordSplit.py` output, with prefixed file name;
* `o`,  Output prefix;
* `r`,  The files with genomic regions for computation, chrom, start, end seperated by tab;
* `c`,  Cpg position folder, output of `pre_cpg_pos.py`;
* `z`,  Whether is the genomic file based on 0; 0 (default) or 1; output is same to input bins; if -r is a bed file, -z should be 1;
* `g`,  Chromosomes used; (default chromsome 1-22); chromosomes shoud be seperated by comma;
* `m`,  Maximum of reads length (default 600bp for paired-end reads). if there are single-end reads,m should be set length of reads, if not sure, default will work for most cases;

### 6. Visualization of methylation matrix (optional, both `visualization_Matlab.m` and `visualization.py` scripts are used for visualization dependent on your choices)
#### choice 1
Usage:`python visualization.py -i ./test/test_chr1_1287967_1288117 -o ./test/test -c ./cpgpos/ -z 0`
* `i`,  The prefix path to recordMat (prefix before _me.txt or _unme.txt);
* `o`,  The prefix path that you want to deposit pdf files;
* `c`,  Cpg position folder, output of `pre_cpg_pos.py`;
* `z`,  Whether is the genomic file based on 0; 0 (default) or 1; output is same to input bins; if -r is a bed file, -z should be 1;
* Output: two lollipop plots in .PDF format, one without considering distance between CpGs, one considering distance between CpGs.
	* unmethylated CpGs are labeled as light blue
	* CpGs without signal are labeled as grey
	* methylated CpGs are labeled as dark red
* <img src="https://github.com/vhang072/MeConcord/blob/main/pics/lollipop plot_1.PNG" width="450" height="400">
* <img src="https://github.com/vhang072/MeConcord/blob/main/pics/lollipop plot_2.PNG" width="250" height="400">



#### choice 2
Usage: `visualization_Matlab.m`
* Open this script and edit
	* `path_to_matrix` as the path you deposit the MeMatrix of `s4_RecordToMeMatrix.py` output;
	* `path_to_cpgPos` as the path you deposit CpG positions of the genome, which is the result of pre_cpg_pos.py;
	* `name` as the name of MeMatrix, for example 'test_chr1_1287967_1288117';

* Output: two lollipop plots, one without considering distance between CpGs, one considering distance between CpGs.
	* unmethylated CpGs are labeled as light blue
	* CpGs without signal are labeled as grey
	* methylated CpGs are labeled as dark red

## CPU time and memory
* <img src="https://github.com/vhang072/MeConcord/blob/main/pics/CPU.PNG" width="600" height="150">

## Test for an example
* *STEP 1* `python s1_bamToMeRecord.py -i ./test/GM12878_chr1_1286017_1294783.bam -o ./test/test -c 2` or `python s1_bamToMeRecord.py -i ./test/GM12878_chr1_1286017_1294783.sam -o ./test/test -c 2` if there is no pysam module on Windows

	* The error that `Could not retrieve index file for './test/GM12878_chr1_1286017_1294783.bam'` doesn't affect the results.
	* Please check if there is an output in test folder, `test_ReadsMethyAndMuts.txt`. If yes, it works.
* *STEP 2* `python s2_RecordSplit.py -i ./test/test_ReadsMethyAndMuts.txt -o ./test/test -g chr1`

	* Please check if there is an output in test folder, `test_ReadsMethyAndMuts_chr1.txt`. If yes, it works.
* *STEP 3* `python s3_RecordToMeConcord.py -p 1 -i ./test/test -o ./test/test -r ./test/tmp1.bed -c ./test/ -b 150 -m 600 -z 1 -g chr1`

	* Please check if there is an output in test folder, `test_MeConcord.txt`. If yes, it works.
* *STEP 4* `python s4_RecordToMeMatrix.py -i ./test/test -o ./test/test -r ./test/tmp2.bed -c ./test/ -m 600 -z 1 -g chr1`

	* Please check if there is two output files in test folder, `test_chr1_1287967_1288117_me.txt`; `test_chr1_1287967_1288117_unme.txt`. If yes, it works.

* *STEP 5* `python visualization.py -i ./test/test_chr1_1287967_1288117 -o ./test/test -c ./test/ -z 0`

	* Please check if there is two output files in test folder, `test_chr1_1287967_1288117_cpgnum.pdf`; `test_chr1_1287967_1288117_cpgpos.pdf`. If yes, it works.
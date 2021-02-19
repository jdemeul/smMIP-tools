# smMIP-tools: a computational toolset for processing and analysis of single molecule molecular-inversion-probes derived data

Here we present an example of how to use smMIP-tools to process and analyze sequencing data obtained from targeted smMIP panels. 
The pipeline covers the following topics:
1) Read processing and quality control steps, construction of read-smMIP linkages and retrieval of molecular tags.
2) smMIP-level base calls summary.
3) smMIP panel annotation
4) Mutation calling using a error-aware variant caller capable of detecting both single nucleotide variants and short insertion and deletions.

## Quick Start
The supplied example data contain sequencing information in the form of aligned bam files. The data derived from a single smMIP that covers a recurrent mutation of which we will attemp to identify.
  
smMIP-tools code can be executed from the terminal. There is no need for installation. Copy the [code files](https://github.com/BioSoft/smMIP-tools/tree/main/R) to your folder of choice.

## Dependencies
This pipeline requires the following software and packages:
| Program | Packages                                       |
| ------------------------|------------------------- |
| R (https://www.r-project.org) | optparse, data.table, parallel, Rsamtools, dplyr, IRanges, cellbaseR |               

*You will be prompt to install/update other packages when installing the above packages.
   

## Read Processing
### Description
map_smMIPs_extract_UMIs.R takes as an input a paired-end read alignment bam file and a smMIP design file containing information about each probe and its targeted genomic loci. It applys a set of filters on the input bam file to discard hard clipped reads, reads with low mapping quality, paired reads with an unexpected insert size or improper alignment orientations. To validate the proper structure of reads, and to identify corrupted Unique Molecular Identifier (UMI) sequences read-smMIP linkages are being conducted. The final output contains a couple of quality control summary files concerning raw and consensus reads as well as a BAM file containing only high-quality reads. UMIs sequences and smMIPs-of-origin will be included in each read’s header. If you are not familiar with the concept of consensus base calls you may read our Nucleic Acid Research paper ([PMID: 31127310](https://academic.oup.com/nar/article/47/15/e87/5498633)).

### Configuration 
1) Make sure to have a [smMIP-design file](https://github.com/BioSoft/smMIP-tools/blob/main/Example/supplemental_files/Target_MIPgen.txt). We used [MIPgen](http://shendurelab.github.io/MIPGEN) to design the smMIP used in this example. If you designed your smMIPs differently, make sure that your file contain the following columns (case sensitve) and their relavant information: chr, ext_probe_start, ext_probe_stop, ext_probe_sequence, lig_probe_start, lig_probe_stop, lig_probe_sequence, mip_scan_start_position, mip_scan_stop_position, scan_target_sequence, mip_sequence ('N' represent UMIs), probe_strand, mip_name
2) Please generate a single folder and copy into it all the bam files that you want to analyse. We provide [bam files](https://github.com/BioSoft/smMIP-tools/tree/main/Example/bams) that can be used with this manual.  

### Running the code
Run map_smMIPs_extract_UMIs.R with the required input parameters:
```
-b, --bam.file. Path to bam file. [MANDATORY]
-p, --panel.file. Path to smMIP design file. [MANDATORY]
-s, --sample.name. Sample ID that will be used to name the output bam. [MANDATORY]
-o, --output, Path for output files. A new folder which is named based on the -s parameter will be generated by default. If the path was not supplied, the sample folder will be generated within the folder that contain the bam files
-c, --code, Path to smMIP tools source functions, smMIPs_Function.R file. If not supplied, it assume that smMIPs_Function.R share the same folder with this code (map_smMIPs_extract_UMIs.R)
-f, --filtered.reads,  default="n", options="y" or "n". Output a sam file that contain the filtered reads. A sam file for the non-filtered reads will also be generated
-t, --threads, default=1. Specify the number of threads to use for parallel processing
-O, --OVERLAP, default=0.95. Fine-tuning the overlap between reads and smMIPs. Used in the map.smip_to_site function
-M, --MAPQ, default=50. MAPQ cut-off. Used in the filter.on.mappingscore function
  ```
\*map_smMIPs_extract_UMIs.R was built to process one bam file at a time. An example shell script to assign jobs to multiple HPC cluster cores is provided [here](https://github.com/BioSoft/smMIP-tools/blob/main/R/map_smMIPs_extract_UMIs_MULTIPLE_BAMS.sh)

```
Rscript /.mounts/example_github/R/map_smMIPs_extract_UMIs.R -b /.mounts/example_github/Example/bams/control1.bam -p /.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt -s control1
  ```  
```
Loading required package: GenomeInfoDb
Loading required package: BiocGenerics
Loading required package: parallel

Attaching package: ‘BiocGenerics’

The following objects are masked from ‘package:parallel’:

    clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
    clusterExport, clusterMap, parApply, parCapply, parLapply,
    parLapplyLB, parRapply, parSapply, parSapplyLB

The following objects are masked from ‘package:stats’:

    IQR, mad, sd, var, xtabs

The following objects are masked from ‘package:base’:

    anyDuplicated, append, as.data.frame, basename, cbind, colnames,
    dirname, do.call, duplicated, eval, evalq, Filter, Find, get, grep,
    grepl, intersect, is.unsorted, lapply, Map, mapply, match, mget,
    order, paste, pmax, pmax.int, pmin, pmin.int, Position, rank,
    rbind, Reduce, rownames, sapply, setdiff, sort, table, tapply,
    union, unique, unsplit, which, which.max, which.min

Loading required package: S4Vectors
Loading required package: stats4

Attaching package: ‘S4Vectors’

The following object is masked from ‘package:base’:

    expand.grid

Loading required package: IRanges
Loading required package: GenomicRanges
Loading required package: Biostrings
Loading required package: XVector

Attaching package: ‘Biostrings’

The following object is masked from ‘package:base’:

    strsplit


Attaching package: ‘dplyr’

The following objects are masked from ‘package:Biostrings’:

    collapse, intersect, setdiff, setequal, union

The following object is masked from ‘package:XVector’:

    slice

The following objects are masked from ‘package:GenomicRanges’:

    intersect, setdiff, union

The following object is masked from ‘package:GenomeInfoDb’:

    intersect

The following objects are masked from ‘package:IRanges’:

    collapse, desc, intersect, setdiff, slice, union

The following objects are masked from ‘package:S4Vectors’:

    first, intersect, rename, setdiff, setequal, union

The following objects are masked from ‘package:BiocGenerics’:

    combine, intersect, setdiff, union

The following objects are masked from ‘package:stats’:

    filter, lag

The following objects are masked from ‘package:base’:

    intersect, setdiff, setequal, union


Attaching package: ‘data.table’

The following objects are masked from ‘package:dplyr’:

    between, first, last

The following object is masked from ‘package:GenomicRanges’:

    shift

The following object is masked from ‘package:IRanges’:

    shift

The following objects are masked from ‘package:S4Vectors’:

    first, second

###############################
        Run Parameters
###############################
$bam.file
[1] "/.mounts/example_github/Example/bams/control1.bam"

$panel.file
[1] "/.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt"

$sample.name
[1] "control1"

$code
[1] "/.mounts/example_github/R"

$filtered.reads
[1] "n"

$threads
[1] 1

$OVERLAP
[1] 0.95

$MAPQ
[1] 50

$help
[1] FALSE

$output
[1] "/.mounts/example_github/Example/bams"

###############################
            Running...
###############################
Loading bam
Filtering reads based on low mapping score
Filtering reads based on bad sam flag
Filtering reads based on mate distance
Filtering hard clipped reads
Filtering reads with missing mates
Mapping smMIPs to reads. Considering overlap and distance measurements :  100%      
Verifying mapping by local smMIP arms alignment : 100%%     
Extracting UMIs
Writing coverage per smMIP and filtered reads summary files
Adding smMIPs names and UMI sequences to the reads names
Writing UMIs per smmip summary
Writing the clean bam file
[1] "/.mounts/example_github/Example/bams/control1/control1_clean.bam"

###############################
             DONE
###############################
  ```
The output:

```
samtools view /.mounts/example_github/Example/bams/control1/control1_clean.bam | head
  ```
```
A00827:148:HJ7MJDRXX:1:2102:14570:4586||JAK2_001:TATA_ACGC	163	chr9	5073708	60	151M	=	5073735	178	ACGCNGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FFFF#FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
A00827:148:HJ7MJDRXX:1:2103:25201:25504||JAK2_001:GTTT_AAGA	163	chr9	5073708	60	151M	=	5073735	174	AAGANGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FFFF#FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFF,FFF:FF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFF:FFFF:FF,FFF:FFFFF,FFFFFFF,FFFF:FFFF,FF:,FF:F,,::F::FFF:,
A00827:148:HJ7MJDRXX:1:2104:5674:18082||JAK2_001:GTTT_AAGA	163	chr9	5073708	60	151M	=	5073735	174	AAGAAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FF,FFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFF:FF:FFFFFFFFFFFFFFFFFFFFFFFFFF
A00827:148:HJ7MJDRXX:1:2108:2094:10441||JAK2_001:GTTC_AAGA	163	chr9	5073708	60	151M	=	5073735	174	AAGAAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	,FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:F:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF,FFFF:FFFF:,F:FFFF:F:,FFFFFFFFFF
A00827:148:HJ7MJDRXX:1:2109:5150:35430||JAK2_001:TTTA_AAGG	163	chr9	5073708	60	151M	=	5073735	175	AAGGAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FFF:FFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFF:FFFFFF,FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFF,FFFF:FFF:FFFFFFFFFFFF:FFFFFFF
A00827:148:HJ7MJDRXX:1:2111:18258:27696||JAK2_001:GTTT_AAGA	163	chr9	5073708	60	151M	=	5073735	174	AAGAAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTGTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTACAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	:FFFFFFFF,FFFFFFFFFFFFFFFFF:FFFFFFFFF,:FFF,,:FFFFFFFFFFFFFF,FFFFFFFFFFFFFFFFFFFFFFFFF,FFFFFFFFFFF,FFFFFFF,FF::FFFFFFFFFFFFFFF:F:FF,F:F:FF,FFFF:FF:FFFF,
A00827:148:HJ7MJDRXX:1:2114:6506:19680||JAK2_001:GTTC_AAGA	163	chr9	5073708	60	151M	=	5073735	174	AAGAAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FF::FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF,FFFFFFFFFFF:FFFFFFF:FFFFFFFFFFFFFF:FF,FFFFFFFF:FFFFFFFFFFF,FF:FFFFFFF,FFFFFF:FFFFFF
A00827:148:HJ7MJDRXX:1:2127:14705:11240||JAK2_001:TATA_ACGC	163	chr9	5073708	60	151M	=	5073735	178	ACGCAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF,FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
A00827:148:HJ7MJDRXX:1:2128:9588:3192||JAK2_001:GTTT_AAGA	163	chr9	5073708	60	151M	=	5073735	174	AAGAAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FFF:FFFFFFFFFFFFF:FFFFFFFFFFFFFF:FFFFF:FFF,FFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFF,FFFFFFFFFFF:FFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFF:F,FFF::F,FFFFF,:FFFFF,FFFFF
A00827:148:HJ7MJDRXX:1:2129:8250:25520||JAK2_001:TTTA_AAGG	163	chr9	5073708	60	151M	=	5073735	175	AAGGAGCAAGTATGATGAGCAAGCTTTCTCACAAGCATTTGGTTTTAAATTATGGAGTATGTGTCTGTGGAGACGAGAGTAAGTAAAACTACAGGCTTTCTAATGCCTTTCTCAGAGCATCTGTTTTTGTTTATATAGAAAATTCAGTTTC	FFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFF,:FFFF:FFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFF:FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF:FFFFFFFFF:FFFFFFFFFF
  ```

## smMIP Level Base Calls Summary
### Description
smMIP_level_raw_and_consensus_pileups.R takes as an input the clean bam file generated by map_smMIPs_extract_UMIs.R and a smMIP design file to summarize the base calls targeted by each smMIP. As compared with conventional Pileup summaries, smMIP-level summaries provides an additional way to identify errors and differentiate them from real mutations. If your smMIPs include unique molecular identifers, consensus base calls summaries will also be generated. 

### Configuration 
Make sure to have a proper smMIP design file. It is being used to infer the length of UMIs, remove summaries of reference alleles to reduce file size and to focus the query only on the targeted loci of each smMIP. The porpose of the latter is to withhold analyis of genomic loci associated with smMIP's arms hybridization. 

### Running the code
Run smMIP_level_raw_and_consensus_pileups.R with the required input parameters:

```
-b, --bam.file. Path to the filtered bam file output by map_smMIPs_extract_UMIs.R (sample_clean.bam) [MENDATORY]
-p, --panel.file. Path to smMIP design file [MENDATORY]
-s, --sample.name. Sample ID that will be used to name the output file(s). If not provided the name of the folder containing the bam file is assumed to be the sample name
-o, --output. Path for the output pileup file(s). If not supplied, the output will be saved within the folder that contain the bam file
-c, --code. Path to smMIP tools source functions, smMIPs_Function.R file. If not supplied, it assume that smMIPs_Function.R share the same folder with this code (smMIP_specific_raw_and_consensus_pileups.R)
-d, --mnd, default=1. Minimum depth to consider in the pileup
-m, --mmq, default=50. Minimum mapping quality to consider in the pileup
-q, --mbq"),default=10. Minimum base quality to consider in the pileup
-r, --rank, default="F". Options are 'F' or 'T'. Reporting all the possible alleles (A,C,T,G,-,+) if they observed. When 'T', only the allele with the most read support (other than the reference) at each genomic postion will be reported. This option can help to remove many errors from the data yet it comes with the risk of missing real mutations.
-u, --umi, default="T". Options are 'T' or 'F'. Reporting the UMI sequences that are associated with each allele will be reported. Provides with the data needed to estimate read-to-sample misassignment
-f, --family.size, default=0. The minimum read-family size to consider in single strand consensus pileups
-v, --consensus.cutoff", default=0.7. The percentage of reads that must have the same base to form a consensus
-t, --threads, default=1. Specify the number of threads to use"
  ```

\*smMIP_level_raw_and_consensus_pileups.R was built to process one bam file at a time. An example shell script to assign jobs to multiple HPC cluster cores is provided [here](https://github.com/BioSoft/smMIP-tools/blob/main/R/smMIP_level_raw_and_consensus_pileups_MULTIPLE_BAMS.sh)

```
Rscript smMIP_level_raw_and_consensus_pileups.R -b /.mounts/example_github/Example/bams/control1/control1_clean.bam -p /.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt -o /.mounts/example_github/Example/pileup/
  ```
```  
Loading required package: GenomeInfoDb
Loading required package: BiocGenerics
Loading required package: parallel

Attaching package: ‘BiocGenerics’

The following objects are masked from ‘package:parallel’:

    clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
    clusterExport, clusterMap, parApply, parCapply, parLapply,
    parLapplyLB, parRapply, parSapply, parSapplyLB

The following objects are masked from ‘package:stats’:

    IQR, mad, sd, var, xtabs

The following objects are masked from ‘package:base’:

    anyDuplicated, append, as.data.frame, basename, cbind, colnames,
    dirname, do.call, duplicated, eval, evalq, Filter, Find, get, grep,
    grepl, intersect, is.unsorted, lapply, Map, mapply, match, mget,
    order, paste, pmax, pmax.int, pmin, pmin.int, Position, rank,
    rbind, Reduce, rownames, sapply, setdiff, sort, table, tapply,
    union, unique, unsplit, which, which.max, which.min

Loading required package: S4Vectors
Loading required package: stats4

Attaching package: ‘S4Vectors’

The following object is masked from ‘package:base’:

    expand.grid

Loading required package: IRanges
Loading required package: GenomicRanges
Loading required package: Biostrings
Loading required package: XVector

Attaching package: ‘Biostrings’

The following object is masked from ‘package:base’:

    strsplit


Attaching package: ‘data.table’

The following object is masked from ‘package:GenomicRanges’:

    shift

The following object is masked from ‘package:IRanges’:

    shift

The following objects are masked from ‘package:S4Vectors’:

    first, second

###############################
        Run Parameters
###############################
$bam.file
[1] "/.mounts/example_github/Example/bams//control1/control1_clean.bam"

$panel.file
[1] "/.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt"

$output
[1] "/.mounts/example_github/Example/pileup/"

$code
[1] "/.mounts/example_github/R"

$mnd
[1] 1

$mmq
[1] 50

$mbq
[1] 10

$rank
[1] "F"

$umi
[1] "T"

$family.size
[1] 0

$consensus.cutoff
[1] 0.7

$threads
[1] 1

$help
[1] FALSE

$sample.name
[1] "control1"

###############################
            Running...
###############################
Loading bam
Creating smMIP-level raw pileups. Please notice, temporary files will be written in /.mounts/example_github/Example/pileup/
Creating smMIP based pileups :  100%         
Writing raw pileup file to disk
Creating smMIP and UMI level pileups. Please notice, temporary files will be written in /.mounts/example_github/Example/pileup/
Creating smMIP-UMI consensus pileups :  100%     
Writing SSCS pileup file
###############################
             DONE
###############################
  ```

The output:

```
head /.mounts/example_github/Example/pileup/control1_raw_pileup.txt
  ```
```
chr	pos	strand	nucleotide	count	coverage_at_position	VAF	smMIP
chr9	5073732	+	A	9	7001	0.00128553063848022	JAK2_001
chr9	5073732	+	G	2	7001	0.000285673475217826	JAK2_001
chr9	5073733	+	A	10	7001	0.00142836737608913	JAK2_001
chr9	5073733	+	C	1	7001	0.000142836737608913	JAK2_001
chr9	5073733	+	G	3	7001	0.000428510212826739	JAK2_001
chr9	5073734	+	A	12	7001	0.00171404085130696	JAK2_001
chr9	5073734	+	C	1	7001	0.000142836737608913	JAK2_001
chr9	5073735	+	A	4	7001	0.000571346950435652	JAK2_001
chr9	5073735	-	A	20	6936	0.00288350634371396	JAK2_001
  ```
```
head /.mounts/example_github/Example/pileup/control1_sscs_pileup.txt
  
chr	pos	strand	nucleotide	count	coverage_at_position	VAF	smMIP	UMI	family_sizes	VAF_in_families
chr9	5073844	+	G	1	318	0.00314465408805031	JAK2_001	TTAA_AATC	1	1
chr9	5073844	-	G	1	320	0.003125	JAK2_001	TTAA_AATC	1	1
chr9	5073775	+	G	1	320	0.003125	JAK2_001	TCAT_AAGT	1	1
chr9	5073789	+	C	1	320	0.003125	JAK2_001	TCAT_AAGT	1	1
chr9	5073845	+	T	10	316	0.0316455696202532	JAK2_001	TTGT_CCTC,CAAT_ATTC,TTAT_AACA,AATT_TTAT,TTAT_TAAT,TATC_TTAT,TTTT_TCAA,AATG_TGTT,TTTT_TACT,CTAT_ACAT	1,1,1,1,1,1,1,1,1,1	1,1,1,1,1,1,1,1,1,1
chr9	5073741	-	C	1	317	0.00315457413249211	JAK2_001	TTTA_GTAC	1	1
chr9	5073753	-	A	2	320	0.00625	JAK2_001	TTTA_GTAC,TCAA_ATTT	1,1	1,1
chr9	5073759	-	C	2	319	0.00626959247648903	JAK2_001	TTTA_GTAC,TCAA_TATT	1,1	1,1
chr9	5073828	+	G	2	318	0.00628930817610063	JAK2_001	TTTA_GTAC,GTTT_AATA	1,1	1,1
  ```

## smMIP Target Panel Annotation
### Description
Annotate_SNVs.R can take two types of files as an input: 1) A [smMIP-design file](https://github.com/BioSoft/smMIP-tools/blob/main/Example/supplemental_files/Target_MIPgen.txt) or 2) a tab delimited file with the columns "chr", "pos", "ref", "alt" . The code uses the  "cellbaseR" package to summarize gene ID, amino acid changes, Cosmic, Minor allele Frequency (MAF), Variant type (synonymous, missense, etc..) and deleteriousness (CADD score) annotations. If the input is a smMIP-design file, all the possible SNVs that might be detected using each smMIP in your target smMIP panel will be annotated. 

### Configuration 
cellbaseR makes use of a web service API. Make sure to run it on a processor(s) that is connected to the internet. If the connection to the server is interupted the code will continue to run until it reaches the last variant. A column named "annotated" will be generated to mark variant that were successfully annotated and those that were not. If this column exists in your output you can try to run Annotate_SNVs.R once more using this output and the -i parameter. If the "annotated" column does not exits it means that all the variants were successfully annotated.

### Running the code
Run Annotate_SNVs.R with the required input parameters:

```
-p, --panel.file. "Path to smMIP design file [MENDATORY if -i was not supplied]
-i, --input. Path to a tab delimited table with the following columns: chr, pos, ref, alt. [MENDATORY if -p was not supplied]
-o, --output. Path for the output annotated panel. If not supplied, the output will be saved within the folder that contain the smMIP design file/input file
-c, --code. Path to smMIP tools source functions, smMIPs_Function.R file. If not supplied, it assume that smMIPs_Function.R share the same folder with this code (Annotate_SNVs.R)
-t, --threads, default=1. Specify the number of threads to use for parallel processing
-g, --genome, default="GRCh37". Specify the genome assembly to be queried. For all the possible options please refer to the cellbaseR package documentation
-s, --species, default="hsapiens". Specify the species to be queried. For all the possible options please refer to the cellbaseR package documentation
 ```
    
```
Rscript Annotate_SNVs.R -p /.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt
  ```
```
Loading required package: BiocGenerics
Loading required package: parallel

Attaching package: ‘BiocGenerics’

The following objects are masked from ‘package:parallel’:

    clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
    clusterExport, clusterMap, parApply, parCapply, parLapply,
    parLapplyLB, parRapply, parSapply, parSapplyLB

The following objects are masked from ‘package:stats’:

    IQR, mad, sd, var, xtabs

The following objects are masked from ‘package:base’:

    anyDuplicated, append, as.data.frame, basename, cbind, colnames,
    dirname, do.call, duplicated, eval, evalq, Filter, Find, get, grep,
    grepl, intersect, is.unsorted, lapply, Map, mapply, match, mget,
    order, paste, pmax, pmax.int, pmin, pmin.int, Position, rank,
    rbind, Reduce, rownames, sapply, setdiff, sort, table, tapply,
    union, unique, unsplit, which, which.max, which.min

Loading required package: S4Vectors
Loading required package: stats4

Attaching package: ‘S4Vectors’

The following objects are masked from ‘package:data.table’:

    first, second

The following object is masked from ‘package:base’:

    expand.grid


Attaching package: ‘IRanges’

The following object is masked from ‘package:data.table’:

    shift

###############################
        Run Parameters
###############################
$panel.file
[1] "/.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt"

$code
[1] "/.mounts/example_github/R"

$threads
[1] 1

$genome
[1] "GRCh37"

$species
[1] "hsapiens"

$help
[1] FALSE

$output
[1] "/.mounts/example_github/Example/supplemental_files"

###############################
            Running...
###############################
[1] Annotating. Please wait...
Downloading SNV annotations :  100%     
Writing the annotated table to disk

###############################
             DONE
###############################
  ```
  
The output:

```
head /.mounts/example_github/Example/supplemental_files/annotated_Target_MIPgen.txt
  ```
```
smMIP	chr	pos	ref	alt	gene	protein	cosmic	maf	variant_type	cadd_scaled
JAK2_001	chr9	5073732	T	A	JAK2	ENST00000539801:L604H; ENST00000381652:L604H; ENST00000544510:L455H	NA	NA	missense_variant	31
JAK2_001	chr9	5073733	T	A	JAK2	ENST00000539801:L604L; ENST00000381652:L604L; ENST00000544510:L455L	NA	NA	synonymous_variant	9.98999977111816
JAK2_001	chr9	5073734	T	A	JAK2	ENST00000539801:S605T; ENST00000381652:S605T; ENST00000544510:S456T	NA	NA	missense_variant	23.7999992370605
JAK2_001	chr9	5073735	C	A	JAK2	ENST00000539801:S605Y; ENST00000381652:S605Y; ENST00000544510:S456Y	NA	NA	missense_variant	33
JAK2_001	chr9	5073736	T	A	JAK2	ENST00000539801:S605S; ENST00000381652:S605S; ENST00000544510:S456S	NA	NA	synonymous_variant	8.86999988555908
JAK2_001	chr9	5073737	C	A	JAK2	ENST00000539801:H606N; ENST00000381652:H606N; ENST00000544510:H457N	NA	NA	missense_variant	27.2999992370605
JAK2_001	chr9	5073738	A	A	JAK2	ENST00000539801:H606H; ENST00000381652:H606H; ENST00000544510:H457H	NA	NA	synonymous_variant	0
JAK2_001	chr9	5073739	C	A	JAK2	ENST00000539801:H606Q; ENST00000381652:H606Q; ENST00000544510:H457Q	COSM29116,haematopoietic and lymphoid tissue	0.000144591296666667	missense_variant	26.8999996185303
JAK2_001	chr9	5073740	A	A	JAK2	ENST00000539801:K607K; ENST00000381652:K607K; ENST00000544510:K458K	NA	NA	synonymous_variant	0
  ```
  
## Calling Mutations 
### Description
calling_mutations.R takes as an input the annotated target panel generated by Annotate_SNVs.R and and a tab delimited file that descibe the configuration of the assay. To call mutations, smMIP-tools uses binomial models to conduct allele-specific frequency comparisons between the sample of interest and either a single or multiple control samples. When controls are not available, the experimental cohort can be used for error rates estimation.
The final output includes comprehensive allele specific information including key annotations, consensus read support, and sequencing batch summaries to further prioritize the called variants. 

### Configuration 
1) Create a assay configuration file. The file need to include the sample names. It need to descibe which of the samples are cases and which are the controls that will be used for error modeling. If you dont have controls that is fine, the code also works for cases only. Lastly, you will need to indicate whether your samples were run in technical replicates (highly recommended, has a major impact on removing false positives). Leave the column named 'replicate' empty if you dont have replicates. Otherwise this column should be filled with unique incrementing numbers such as that replicate1 and replicate2 from sample1 will both get the value of '1'. replicate1 and replicate2 from sample2 will get '2' and so forth. A assay configuration file that will work with the supplied example data is provided [here](https://github.com/BioSoft/smMIP-tools/blob/main/Example/supplemental_files/configuration.txt). 

2) 
### Running the code
Run calling_mutations.R with the required input parameters:  

```
-s, --summary. Path to the folder that contain the base calls summaries generated by smMIP_level_raw_and_consensus_pileups.R [Mendatory]
-f, --file. Path to the input file that descibes the configuration of the assay [Mendatory]
-a, --alleles. Annotated panel file [Mendatory]
-c, --code, Path to smMIP tools source functions, smMIPs_Function.R file. If not supplied, it assume that smMIPs_Function.R share the same folder with this code (calling_mutations.R)
-b --binomial. Options are 'sum' or 'max'. Indicates whether allele-specific error rates will be defined by the sum of alternative bases divided by the sum of reference bases across all the samples used for error modeling, or by the observed maximum
-g, --overlap.coverage. default=10. Determine the minimum coverage at read1 and read2 to consider during P-value calculations
-m, --maf. default=0.001. Minor allele frequency cut-off. Being used to remove known SNPs from being included in the error models.
-v, --vaf. default=0.05. Variant allele frequency cut-off. Being used to remove personal SNPs and reduce the presence of high VAF, authentic SNVs in the error models
-p, --pval. default=0.05. P-value cut-off to call mutations. If set to '1' all the alleles with p-value<=0.05 prior to Bonferroni correction will be reported
-t, --threads. default=1. Specify the number of threads to use for parallel processing
-o, --output, Path for the output file. If not supplied, the output will be saved within the folder that contain the input configuration file
   ```
```  
Rscript calling_mutations.R -s /.mounts/example_github/Example/pileup -f /.mounts/example_github/Example/supplemental_files/configuration.txt -a /.mounts/example_github/Example/supplemental_files/annotated_Target_MIPgen.txt 
  ```
```
###############################
        Run Parameters
###############################
$summary
[1] "/.mounts/example_github/Example/pileup"

$file
[1] "/.mounts/example_github/Example/supplemental_files/configuration.txt"

$alleles
[1] "/.mounts/example_github/Example/supplemental_files/annotated_Target_MIPgen.txt"

$code
[1] "/.mounts/example_github/R"

$binomial
[1] "sum"

$overlap.coverage
[1] 10

$maf
[1] 0.001

$vaf
[1] 0.05

$pval
[1] 0.05

$threads
[1] 1

$help
[1] FALSE

$output
[1] "/.mounts/supplemental_files"

$overlap.coverage2
[1] 20

###############################
            Running...
###############################
Loading pileup data :  100%     
Leveraging data from Cosmic and information concerning known polymorphisms to increase sensitivity in those genomic positions... DONE!
Estimating background error levels and calculating P-values :  100%     
Ajusting P-values to account for the plus and minus replicated DNA strands... DONE!
Ajusting P-values to account for overlapping smMIPs... DONE!
Ajusting P-values to to account for technical replicates... DONE!
Calling mutations. Applying user-define P-value cut-off... DONE!
Adding consensus read information :  100%     
Allele frequencies are now calulated based on both the plus and minus replicated DNA strands
Writing batch related information (part1)... DONE!
Adding UMI information concerning potential index-hopping :  100%     
Allele frequencies are now calulated based on overlapping smMIPs
Allele frequencies are now calulated based on sample replicates
Writing batch related information (part2) :  100%     
[1] "A table reporting the identified mutations (called_mutations.txt) was written to /.mounts/example_github/Example/supplemental_files"
  ```  
 
 sample_ID	smMIP	chr	pos	ref	alt	gene	protein	cosmic	maf	variant_type	cadd_scaled	P-value	P-value.Bonferroni	num.pval.pass	pass.pval.in.pairs	non.ref.counts	total.depth	allele.frequency	samples.with.higher.vaf	higher.vaf	lower.vaf	SSCS.non.ref.counts	SSCS.total.depth	SSCS.family.size	SSCS.in.family.non.ref.vaf	SSCS.allele.frequency	SSCS.overlap	flags
case1_rep1,case1_rep2	JAK2_001	chr9	5073770	G	T	JAK2	ENST00000539801:V617F; ENST00000381652:V617F; ENST00000544510:V468F	COSM12600,haematopoietic and lymphoid tissue,lung,central nervous system	0.000499393	missense_variant	33	8.12E-89	6.14E-86	6	3	575	18098	0.031771466	1	0.046512	0.029589,0.002016,0.002016	25	888	38,32,33,15,1,1,1,1:D:38,32,33,15,1,1:R:55,12,17,6,22,1:D:55,12,17,6,22	0.973684210526316,1,1,1,1,1,1,1:D:0.973684210526316,1,1,1,1,1:R:1,1,1,1,1,1:D:1,1,1,1,1	0.028153153	12.5,16.67	
case2_rep1,case2_rep2	JAK2_001	chr9	5073770	G	T	JAK2	ENST00000539801:V617F; ENST00000381652:V617F; ENST00000544510:V468F	COSM12600,haematopoietic and lymphoid tissue,lung,central nervous system	0.000499393	missense_variant	33	8.39E-104	6.34E-101	6	3	731	24705	0.029589152	2	0.031771,0.046512	0.002016,0.002016,0.001827	43	1073	1,37,37,29,13,2,1,2,1:D:1,37,37,29,13,2,1,2,1:R:35,36,31,1,33,1,14,18,2,1,3,1:D:35,36,31,1,33,1,1,14,18,2,1,3,1	1,1,1,0.96551724137931,0.923076923076923,1,1,1,1:D:1,0.972972972972973,1,1,0.923076923076923,1,1,1,1:R:1,1,1,1,1,1,1,1,1,1,1,1:D:1,1,1,1,1,1,1,1,1,1,1,1,1	0.040074557	11.11,7.69	
case3_rep1,case3_rep2	JAK2_001	chr9	5073770	G	T	JAK2	ENST00000539801:V617F; ENST00000381652:V617F; ENST00000544510:V468F	COSM12600,haematopoietic and lymphoid tissue,lung,central nervous system	0.000499393	missense_variant	33	8.87E-10	6.70E-07	6	3	32	688	0.046511628	0	NA	0.031771,0.029589,0.002016	32	180	1,1,1,1,1,1,1:D:1,1,1,1,1,1,1:R:1,1,1,1,1,1,1,1,1:D:1,1,1,1,1,1,1,1,1	1,1,1,1,1,1,1:D:1,1,1,1,1,1,1:R:1,1,1,1,1,1,1,1,1:D:1,1,1,1,1,1,1,1,1	0.177777778	14.29,0	
case3_rep1,case3_rep2	JAK2_001	chr9	5073769	T	G	JAK2	ENST00000539801:C616W; ENST00000381652:C616W; ENST00000544510:C467W	NA	NA	missense_variant	32	5.93E-05	0.044824976	1	1	2	221	0.009049774	0	NA	0.000283,0.00021,0.000203	2	50	NA:D:NA:R:1,1:D:NA	NA:D:NA:R:1,1:D:NA	0.04	0,0	Cannot be supported by both tehcnical replicates. (Low coverage in sample:case3_rep1), Cannot be supported by both Read1 and Read2 generated from smMIP: JAK2_001. (Low coverage in sample: case3_rep2), No SSCS support in one of the replicates
  
  
Who to contact: sagi.abelson@gmail.com

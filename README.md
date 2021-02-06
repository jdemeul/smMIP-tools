# smMIP-tools: a computational toolset for read processing and mutation detection from molecular-inversion-probes derived data

Here we present an example of how to use smMIP-tools two main features:
The first is a read processing tool that performs quality control steps, generates read-smMIP linkages and retrieval of molecular tags.
The second is an error-aware variant caller capable of detecting both single nucleotide variants and short insertion and deletions.
The example data contain sequencing information obtained by a single smMIP that covers a recurrent mutation of which we will attemp to identify.

## Quick Start
smMIP-tools can be executed from the terminal. There is no need for installation. Copy the [code files](https://github.com/BioSoft/smMIP-tools/tree/main/R) to your folder of choice.

## Dependencies
This pipeline requires the following software and packages:
| Program | Packages                                       |
| ------------------------|------------------------- |
| R (https://www.r-project.org) | optparse, data.table, parallel, Rsamtools, dplyr, IRanges, cellbaseR |               

*You will be prompt to install/update other packages when installing the above packages.
   

## Read Processing
### Description
map_smMIPs_extract_UMIs.R takes as an input a paired-end read alignment bam file and a smMIP design file containing information about each probe and its targeted sequenced. It applys a set of filters on the input bam file to discard hard clipped reads, reads with low mapping quality, paired reads with an unexpected insert size or improper alignment orientations. To validate the proper structure of reads, and to identify corrupted UMI sequences read-smMIP linkages are being conducted. The final output contains a couple of quality control summary files concerning raw and consensus reads as well as a BAM file containing only high-quality reads. UMIs sequences and smMIPs-of-origin will be included in each read’s header.

### Configuration 
1) Make sure to have a [smMIP-design file](https://github.com/BioSoft/smMIP-tools/blob/main/Example/supplemental_files/Target_MIPgen.txt). We used [MIPgen](http://shendurelab.github.io/MIPGEN) to design the smMIP used in this example. If you designed your smMIPs differently make sure that your file contain the following columns (case sensitve) and their relavant information: chr, ext_probe_start, ext_probe_stop, ext_probe_sequence, lig_probe_start, lig_probe_stop, lig_probe_sequence, mip_scan_start_position, mip_scan_stop_position, scan_target_sequence, mip_sequence, probe_strand, mip_name
2) Please generate a single folder and copy into it all the bam files that you want to analyse. We provide [bam files](https://github.com/BioSoft/smMIP-tools/tree/main/Example/bams) that can be used with this manual.  

### Running the code
Run map_smMIPs_extract_UMIs.R with the required input parameters:
```
-b, --bam.file. Path to bam file. [MANDATORY]
-p, --panel.file. Path to smMIP design file. [MANDATORY]
-s, --sample.name. Sample ID that will be used to name the output bam. [MANDATORY]
-o, --output, Path for output files.  Path for output files. A new folder which is named based on the -s parameter will be generated by default. If not supplied, the sample folder will be generated within the folder that contain the bam files.
-c, --code, Path to smMIP tools source functions, smMIPs_Function.R file. If not supplied, it assume the code share the same folder as this code folder with this code (map_smMIPs_extract_UMIs.R).
-f, --filtered.reads,  default="n", options="y" or "n". Output a sam file that contain the filtered reads. A sam file for the non-filtered reads will also be generated.  
-t, --threads, default=1. Specify the number of threads to use for parallel processing. 
-O, --OVERLAP, default=0.95. Fine-tuning the overlap between reads and smMIPs. Used in the map.smip_to_site function
-M, --MAPQ, default=50. MAPQ cut-off. Used in the filter.on.mappingscore function
  ```
\*map_smMIPs_extract_UMIs.R was built to process one bam file at a time. An example shell script to assign jobs to multiple HPC cluster cores is provided [here](https://github.com/BioSoft/smMIP-tools/blob/main/R/map_smMIPs_extract_UMIs_MULTIPLE_BAMS.sh)

```
Rscript /.mounts/example_github/R/map_smMIPs_extract_UMIs.R -b /.mounts/example_github/Example/bams/control1.bam -p /.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt -s control1

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
  
## smMIP Target Panel Annotation
### Description
Annotate_smMIP_panel.R takes as an input a [smMIP-design file](https://github.com/BioSoft/smMIP-tools/blob/main/Example/supplemental_files/Target_MIPgen.txt). It uses the  "cellbaseR" package to summarize gene ID, amino acid changes, Cosmic, Minor allele Frequency (MAF), Variant (synonymous, missense, etc..) and deleteriousness (CADD score) annotations for all the possible SNVs that might be detected using your target smMIP panel. 

### Configuration 
cellbaseR makes use of a web service API. Make sure to run it on a processor(s) that is connected to the internet.

### Running the code
Run Annotate_smMIP_panel.R with the required input parameters:

```
-p, --panel.file. Path to smMIP design file. [MANDATORY]
-c, --code, Path to smMIP tools source functions, smMIPs_Function.R file. If not supplied, it assume the code share the same folder as this code folder with this code (Annotate_smMIP_panel.R).
-o, --output. Path for the output annotated panel. If not supplied, the output will be saved within the folder that contain the smMIP design file.
-t, --threads, default=1. Specify the number of threads to use for parallel processing. 
-g, --genome, default="GRCh37". Specify the genome assembly to be queried. For all the possible options please refer to the cellbaseR package documentation. 
-s, --species, default="hsapiens". Specify the species to be queried. For all the possible options please refer to the cellbaseR package documentation.
 ```
   
 Rscript Annotate_smMIP_panel.R -p /.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt
 
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
[1] Annotating all the possible SNVs in the target space. Please wait...
Downloading SNV annotations :  100%     
Writing the annotated table to disk

###############################
             DONE
###############################
```

head /.mounts/example_github/Example/supplemental_files/Target_MIPgen.txt

```
smMIP	chr	pos	ref	alt	gene	protein	cosmic	maf	variant_type	annotated	cadd_scaled
JAK2_001	chr9	5073732	T	A	JAK2	ENST00000539801:L604H; ENST00000381652:L604H; ENST00000544510:L455H	NA	NA	missense_variant	1	31
JAK2_001	chr9	5073733	T	A	JAK2	ENST00000539801:L604L; ENST00000381652:L604L; ENST00000544510:L455L	NA	NA	synonymous_variant	1	9.98999977111816
JAK2_001	chr9	5073734	T	A	JAK2	ENST00000539801:S605T; ENST00000381652:S605T; ENST00000544510:S456T	NA	NA	missense_variant	1	23.7999992370605
JAK2_001	chr9	5073735	C	A	JAK2	ENST00000539801:S605Y; ENST00000381652:S605Y; ENST00000544510:S456Y	NA	NA	missense_variant	1	33
JAK2_001	chr9	5073736	T	A	JAK2	ENST00000539801:S605S; ENST00000381652:S605S; ENST00000544510:S456S	NA	NA	synonymous_variant	1	8.86999988555908
JAK2_001	chr9	5073737	C	A	JAK2	ENST00000539801:H606N; ENST00000381652:H606N; ENST00000544510:H457N	NA	NA	missense_variant	1	27.2999992370605
JAK2_001	chr9	5073739	C	A	JAK2	ENST00000539801:H606Q; ENST00000381652:H606Q; ENST00000544510:H457Q	COSM29116,haematopoietic and lymphoid tissue	0.000144591296666667	missense_variant	1	26.8999996185303
JAK2_001	chr9	5073742	G	A	JAK2	ENST00000539801:K607K; ENST00000381652:K607K; ENST00000544510:K458K	NA	NA	synonymous_variant	1	13.039999961853
JAK2_001	chr9	5073743	C	A	JAK2	ENST00000539801:H608N; ENST00000381652:H608N; ENST00000544510:H459N	NA	NA	missense_variant	1	29.8999996185303
  ```




Who to contact: sagi.abelson@gmail.com

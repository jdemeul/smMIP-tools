# smMIP-tools: a computational toolset for read processing and mutation detection from molecular-inversion-probes derived data

Here we present an example of how to use smMIP-tools main two features:
The first is a read processing tool that performs quality control steps, generates read-smMIP linkages and retrieval of molecular tags.
The second is an error-aware variant caller capable of detecting both single nucleotide variants and short insertion and deletions.

## Quick Start
smMIP-tools can be executed from the terminal. There is no need for installation.

## Dependencies
This pipeline requires the following software and packages:
| Program |
| ------------------------------------------------- |
| R (https://www.r-project.org) | Run smMIP tools |

| Packages                                       |
| ------------------------------------------------ |
| optparse, data.table, parallel, Rsamtools, dplyr |   




## Read Processing
### Description
map_smMIPs_extract_UMIs.R takes as an input a paired-end read alignment bam file and a [smMIP-design file](https://www.python.org/) containing information about each probe and its targeted sequenced. It applys a set of filters on the input bam file to discard hard clipped reads, reads with low mapping quality, paired reads with an unexpected insert size or improper alignment orientations. To validate the proper structure of reads, and to identify corrupted UMI sequences read-smMIP linkages are being conducted. The final output contains a couple of quality control summary files concerning raw and consensus reads as well as a BAM file containing only high-quality reads. UMIs sequences and smMIPs-of-origin will be included in each read’s header.

### Configuration 
Please generate a single folder and copy into it all the bam files that you want to analyse.
map_smMIPs_extract_UMIs.R was built to process one bam file at a time

 Filtered reads as well as the number of reads that were filtered at each filtering step are outlined and written into separate files.

Who to contact: sagi.abelson@gmail.com

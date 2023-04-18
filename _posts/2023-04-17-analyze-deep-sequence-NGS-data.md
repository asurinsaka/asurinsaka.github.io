---
layout: post
title: How to use ReCo to get gRNA read counts from fastq data
tags: [deep sequencing, NGS, ReCo, fastq, gRNA]
---

[ReCo!](https://github.com/MaWeffm/ReCo) is a new library to process deep sequencing fastq data and generates gRNA read counts. This post will talk about how to use the ReCo program.

# Pre-installation

## Install Cutadapt
[Cutadapt](https://cutadapt.readthedocs.io/en/stable/) could be installed with `sudo apt install cutadapt` on ubuntu. The README of ReCo said they were using version 2.8 but the 3.5 that came with Ubuntu 22 seems working fine.

## Install bowtie2 
Linux version of [bowtie2](https://bowtie-bio.sourceforge.net/bowtie2/index.shtml) 2.3.0 could be downloaded. After decompress the file, make sure add the root folder to the PATH.

## Install Python3 

# Installation

1. Git Clone or download the source code from github https://github.com/MaWeffm/ReCo. 
2. Create a virtual environment with `pytohn3 -m venv venv`
3. Activate the virtual environment with `source venv/bin/activate`
3. Install the python dependencies with `pip install -r requirements.txt`

# Prepare data
First of all, we need the fastq data from deep sequencing. The fastq data could be in text format or gz format. 

Other than the fastq data from deep sequencing, we need 2 other files. 

1. A txt gRNA library in tsv format with all the unique names and sequence for gRNA. The file should not have headers. 

    | <!-- --> | <!-- --> |
    |---------|---------------------|
    | Zglp1-0 | CCTTCTCAATACCCATTCG |
    | Zglp1-1 | CTTCTCAATACCCATTCGC |
    | Zglp1-2 | GACCTGGGTGGCCGGCGAA |
    | Zglp1-3 | ACCTGGGTGGCCGGCGAAG |
    | Zglp1-4 | CGCGCCACGCACCTGATAT |
    | Mdn1-0  | ATGCGAACCCAGTGCGCTA |
    | ...     | ...                 |

2. A sample sheet contains all the information for the samples. 

| Sample name             | Sample type | Vector | FastQ read 1                            | FastQ read 2 | Lib 1            | Lib 2 | Expected reads |
|-------------------------|-------------|--------|-----------------------------------------|--------------|------------------|-------|----------------|
| TG-Li-CRISPR-Control 1  | single      |        | TG-Li-CRISPR-Control_S1_R1_001.fastq.gz |              | primary_lib1.txt |       | 11,834,515     |
| TG-Li-CRISPR-Control 2  | single      |        | TG-Li-CRISPR-Control_S1_R2_001.fastq.gz |              | primary_lib1.txt |       | 11,834,515     |
| TG-Li-CRISPR-Enriched 1 | single      |        | TG-Li-CRISPR-Enrich_S2_R1_001.fastq.gz  |              | primary_lib1.txt |       | 11,000,108     |
| TG-Li-CRISPR-Enriched 2 | single      |        | TG-Li-CRISPR-Enrich_S2_R2_001.fastq.gz  |              | primary_lib1.txt |       | 11,000,108     |

# Run Reco!

To run the program use the following command:

```
PATH="/path/to/bowtie2-2.3.0:$PATH" /path/to/python /path/to/reco/ReCo/ReCo.py cli -s /path/to/NGS_all.xlsx --o /path/to/output/ -j 8 -r
```

The counts will be in file with suffix `final_guidecounts.csv`. 


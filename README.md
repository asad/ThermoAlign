ThermoAlign:
================================================
ThermoAlign is a genome-aware oligonucleotide design algorithm embedded within a distributable tool (i.e. consolidating analysis of thermodynamics and sequence alignment). ThermoAlign determines the priming specificity of oligonucleotides based on genome-wide analysis of the thermodynamics of hybridization for full-length and relevant primer-template interactions in the presence of mismatches, to design and select template specific oligonucleotides for any species with a reference genome sequence. It is also capable of using prior information on genomic variants during the selection of candidate oligonucleotides. In addition to the design of primer pairs for standard, single locus PCR amplification, ThermoAlign uses a directed graph analysis approach to design multiplex sets of tiled primer pairs for targeted re-sequencing applications.


Running ThermoAlign:
================================================
ThermoAlign is released under a GNU GPLv3 open source license. The [source code](https://github.com/drmaize/ThermoAlign/tree/master/TA_codes) can be found [here.](https://github.com/drmaize/ThermoAlign/tree/master/TA_codes)

The required parameters need to be used in the [parameters.py](https://github.com/drmaize/ThermoAlign/blob/master/TA_codes/parameters.py) file.

The shell script [pipeline.sh](https://github.com/drmaize/ThermoAlign/blob/master/TA_codes/pipeline.sh) would run all the required ThermoAlign components.

Alternatively, individual components of ThermoAlign may also be separately run in the following order:
1) TRS.py; 2) UOD.py 3) PSE.py 4) PPS.py



Requirements for directly running the source code
================================================
* Linux/Unix
* [Python  2.7](http://python.org/)
* [NumPy   1.9.2](http://www.numpy.org/)
* [pandas  0.18.1](http://pandas.pydata.org/)
* [Cython  0.24](http://cython.org/)
* [primer3-py  0.5.1](https://pypi.python.org/pypi/primer3-py)
* [BLAST   2.2.31+](http://blast.ncbi.nlm.nih.gov/Blast.cgi)
* [MultiPLX    2.0](http://bioinfo.ut.ee/download/dl.php?file=24)
* [networkx    1.11](https://networkx.github.io/)


ThermoAlign Docker images:
================================================
Beyond the source code, the following Docker images are made available: 
* (i) TA_1.0.0_s is a sample run version containing a small set of sample files that can be used to test ThermoAlign
* (ii) TA_1.0.0_d is a general distributable version which requires user supplied files
* (iii) TA_1.0.0_Zm3 is a maize ready version containing all components required for running ThermoAlign as described in this study

    
These docker images help automate deployment of ThermoAlign inside docker containers. It is an efficient way to port ThermoAlign across systems and operating systems.

For optimum performance with large and highly repetitive genomes such as the maize genome, it is recommended that the source codes be run natively, with the required dependencies installed on your local machine/cluster. 

Installing Docker:
================================================
Docker installation can be done by visiting the [official Docker installation page](https://docs.docker.com/engine/installation/) and following the instructions tailored for your operating system.

Basic [docker commands](https://goo.gl/TfU9AY) to run software containers such as ThermoAlign can be found [here](https://goo.gl/TfU9AY) or on [docs.docker.com](https://docs.docker.com/).


To run a docker image as a container that can be accessed via bash:

    docker run -t -i drmaize/thermoalign:TA_1.0.0_s /bin/bash           (container with a sample genome and sample polymorphism file, for quick testing of ThermoAlign)
    
                            OR
                            
    docker run -t -i drmaize/thermoalign:TA_1.0.0_d /bin/bash           (a container without any reference genome or polymorphism file)
    
                            OR
                            
    docker run -t -i drmaize/thermoalign:TA_1.0.0_Zm3 /bin/bash         (a maize ready version; for large genomes like maize its preferred that )

This will pull the latest ThermoAlign images from docker hub and generate a new ThermoAlign container in your local machine. 
If the particular image is already present in your local system, this would simply run a container based on that image. 
These docker images include a vim text editor so that users may modify the ThermoAlign parameters.


    cd TA_codes/                    # move to TA_codes directory
    python vcf_conversion.py        # one time preprocessing of vcf files in "../sample_vcf" directory
    vim parameters.py               # to input the desired parameters
    ./pipeline.sh                   # run TA scripts
    
    
After exiting from a container, the output files may be copied from the container to host.

    docker cp <containerId>:/file/path/within/container /host/path/target
    
    example:
    
    docker cp 024894d25e19:/TA_codes/TA_2016-08-08T15_23_29_531468/ ./
    

    
These docker containers may be run on a cluster in interactive mode:
    
    qsub -I -V -N intrctv -l nodes=biomix17:ppn=5
    
and then,
    
    docker run -t -i drmaize/thermoalign:TA_1.0.0_s /bin/bash
    

Please be aware that the available memory for running these docker containers should be greater than the combined size of the whole genome and variant files.


The provided docker containers work best for smaller genomes, with user defined regions of < 10 kb sizes, at narrow primer size, Tm and GC ranges.
  
   
Format for external whole genome and variant files:
================================================

### _Input chromosme_

The chromosome files should be named as             :   chr1.fasta, chr2.fasta etc

The fasta header should be of the following format  :   >chromosome:assembly_ver:chr#:start_pos:end_pos:#sequences

Example                                             :   >chromosome:AGPv3:13:1:7261561:1


###  _Input variant vcf file_ 

The variant vcf files should be named as            :   chr1.vcf, chr2.vcf etc

vcf format (1000 genomes project format)            :   A vcf file (v4.0 or v4.1) based on the same coordinate system of the reference genome sequence may be optionally used for polymorphism-aware primer design


Output files:
================================================
All output files will be saved in a directory named based on the timestamp of TA_year-month-date-time format (for example :TA_2016-09-13T09_05_00_545957). 

Each output file name will start with the same timestamp. 

* The TRS module:

TA_2016-09-13T09_05_00_545957_run_summary.txt                       :   An output file with detailed summary statistics from the run

TA_2016-09-13T09_05_00_545957_3_33000_2001_VariantMasked.fasta      :   Variant masked locus sequence in fasta format

* The UOD module:

TA_2016-09-13T09_05_00_545957_UOD_out1_1.fasta                      :   All possible primers (+ & -strands) for the given UOD paramters (.fasta format).

TA_2016-09-13T09_05_00_545957_UOD_out1_2.txt                        :   All possible primers (+ & -strands) for the given UOD paramters (.txt format). This includes the occurence of each primer in the target locus.

TA_2016-09-13T09_05_00_545957_UOD_out2_1.fasta                      :   .fasta file of + strand primers

TA_2016-09-13T09_05_00_545957_UOD_out2_2.fasta                      :   .fasta file of - strand primers

TA_2016-09-13T09_05_00_545957_UOD_out3_1.fasta                      :   .fasta file of + strand primers, with location, primer length and Tm information

TA_2016-09-13T09_05_00_545957_UOD_out3_2.fasta                      :   .fasta file of - strand primers, with location, primer length and Tm information

TA_2016-09-13T09_05_00_545957_UOD_out4_1.fasta                      :   .fasta file of + & - strand primers



   
                                    ##### END OF README #####    


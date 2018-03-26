# phASE-Extender
**Extender** for the readbackphased haplotype blocks
a python program that takes in ReadBack phased haplotypes states across several samples and returns extended haplotype blocks for the sample of interest.

# ReadBackPhased Haplotypes
Check these links for details on readbackphasing
- https://software.broadinstitute.org/gatk/documentation/tooldocs/3.8-0/org_broadinstitute_gatk_tools_walkers_phasing_ReadBackedPhasing.php
- https://github.com/secastel/phaser/tree/master/phaser

phASE-Extender can be used with the output from GATK pipeline and several other tools that produce readbackphased haplotype blocks. But, the vcf data produced from `phaser` that consists of `PI` and `PI` values in `FORMAT` field is most directly applicable.

Developed by [Bishwa K. Giri](mailto:bkgiri@uncg.edu, kirannbishwa01@gmail.com) in the [Remington Lab](website?) at the University of North Carolina at Greensboro, Biology department.

Runs on Python 3.x and has the following dependencies:
- [pandas] (http://pandas.pydata.org/)
argparse
collections
csv
decimal
functools
itertools
io
multiprocessing
numpy
resource
time
io
matplotlib

# Citation
Giri, B. K., Remington D. L. Haplotype phase extension and preparation of diploid genome using phase-Extender. biorxiv (2018) [not uploaded yet].


# BACKGROUND
Haplotype phasing is a second go to problem in bioinformatics after read alignment. The importance of haplotype phasing directly applies to analyses of ASE (allele specific expression), preparation of extended haplotype block for EHH (extended haplotype homozygosity) test, preparation of dipolid genome which will soon be a gold standard in bioinformatics in coming years. The necessity for haplotype phasing increases with increase in heterozygosity in the genome, because higher hetetogeneity leads to larger alignment bias and complicates the variants that are called using that alignment data.

For haplotype phasing tools like Beagle, ShapeIT, impute are dominantly used. These tools make use of the variants (SNPs, InDels) along the length of the genome but treat each variants as single observations independent of variant in the consecutive position. So, for a chain of variants at "n" different sites we have "2n" possible haplotype block thus increasing computational burden. With increase in the size of PE reads from illumina and availability of longer sequences from PacBio the issue with "2n" problem can be overcome by preparing several RBphased haplotype blocks. *If we have a pool of several samples that have RBphased haplotypes we can trasfer the phase state information across the samples to solve the state of the consecutive haplotype block in a sample, one at a time.* The proper phase state of two consecutive haplotype blocks (in any one sample) can be solved to prepare the extended haplotype by using haplotype blocks from other samples that bridges that breakpoint. In **phASE-Extender** we use first order markov chain to study and compute the relationship between two consecutive haplotype blocks by preparing first order transition matrix from all the nucleotides of former haplotype block to all the nucelotides in the later haplotype block. We then compute cumulative likelyhood estimate of haplotype states for all possible configuration. But, for two consecutive haplotype blocks there are only two possible configuration. *Therefore the problem comes down the solveing only two haplotype states at once. It is thus possible to extend futher haplotype blocks along the genome. phASE extender actually capitalizes on this later part and uses first order transition probabilities between the consecutive haplotype blocks to calculate the likelhood of one strand from the former block being properly phased with another strand in later block.*

ReadBackphasing is slowly emerging as a replacement for prepration of long range haplotypes; see tools [WhatsHap] (https://whatshap.readthedocs.io/en/latest/), [hapCut] (https://github.com/vibansal/hapcut), [phaser] (https://github.com/secastel/phaser/blob/master/phaser/README.md). The issue remains though, that these tools prepare the RBPhased haplotype blocks based on multiple input "BAM" and "VCF" files for a single sample and/or trios. Also, the increase on RBphase states depends upon multiple BAM files, or multiple sets of longer reads from same individual.For specimen that don't have multiple genomic and rna sources, reference haplotype panel, these method is only possible to yield short range haplotypes. Further it is not possible to transfer phase information across population of samples using these tools there for limiting long range and genome wide haplotype in emerging research models.  phASE-Extender overcomes these limitations by using the short range RBphased haplotype blocks from several samples that have several haplotype break points. But, usually the haplotype break point in one sample is bridged by RBphased haplotype blocks in several other samples in that pool. Thus it is possible to solve the haplotype breakpoints by transfering the phase information between closely and distantly related samples. 

**phASE-Extender** actually starts with already prepared RBphased haplotype blocks of several individuals at once and joins the two RBphased blocks for a single sample at once using overlapping phase states in other samples of the pool. It exclusively uses the `Phased Genotype i.e PG` and `Phase Block Index i.e PI` in the VCF (variant call format) generated by the program `pHASER` https://github.com/secastel/phaser , https://github.com/secastel/phaser/tree/master/phaser . PI represents the unique haplotype block index (or key) in the given VCF files and PG represents the phased genotypes within that PI block. So, it is crucial to run **phaser** on your VCF of interest before running phase extension or prepare the "haplotype file" in appropriate format.


# Algorithm
For the **mcve** regarding the algorithm see this issue on **stackoverflow** https://codereview.stackexchange.com/questions/186396/solve-the-phase-state-between-two-haplotype-blocks-using-markov-transition-proba . 
**A neat diagrammatic version will be soon included in my wordpress account **



# Tutorial

# pHASE-Extender
## Introduction
**a python program to extend the ReadBackPhased haplotype blocks using first order transition probabilities.**



# Setup
Before phASER can be run the read variant mapper module must be compiled. This requires [Cython](http://cython.org) and can be performed with the following command: "python2.7 setup.py build_ext --inplace". **NOTE** Cython requires the package python-devel to be installed. This can be installed using a package manager using e.g. "yum install python-devel.x86_64".

# Usage
Requires a VCF and BAM, produces a VCF with computed haplotype phases and result files containing haplotype details, statistics, and read counts. By default only sites with the "PASS" flag in the VCF will be considered, however this behavior can be changed using the "--pass_only 0" argument.

**Test case**








# Some Q/A on phase-extender: 

  **1) How is phase Extender different than other gw (genome wide), cw (chromosome wide) haplotype phasing program ?**
  
    The very first important thing in genomics and transcriptomics is alignment. While there has been quite some improvements 
    in alinment algorithms and which still continues, another field of genomics that has utmost importance is haplotype phasing - 
    which includes organizing the called variants accurately among each other. So, phasing represents another era of improvements
    in genomics techonology and application. 
    Most, of the haplotype phasing tools are mostly built with human genome (modern and ancient) in mind,but they do benefit 
    other ogranism which have similar level of genomic resources. They rely moslty on huge amount of genotype data and available 
    haplotype reference panel which are not readily available for other organisms. Also, these tools mainly aim for genome wide 
    haplotype, which is of least concern for emerging models. Rather solving the complete phase state within a gene, 
    transcriptome or region of interest is more crucial. 
    Most phasing tools solve the haplotype state problem by taking in SNPs data from multiple samples, then it tries to solve the 
    phase state for one sample by preparing a haplotype that can possibly give rise to all other possible haplotype 
    configurations. Recent advancement in haplotype phasing has started involving "ReadBackPhased" haplotypes. But, most of the
    tools are geared toward connecting the phase state by sequencing different tissues from sample (citation ... ?). However, 
    it is possible to solve the phase state between two haplotype blocks if the position they are broken at is covered by 
    RBPhased haplotyes in other related samples. 
    
    
  **2) What kind of algorithm does phase-extender use ?**
  
    phase-extender uses first-order-transition probabilities from each level of genotypes from former haplotype 
    block to each level of genotypes to later haplotype block. At the moment phase-extender only uses **forward-1stOrder-markov chains** transition probabilities. I soon plan to improve the algorithm by adding **backward-1stOrder-markov chains** 
    transition probabilities. These may follow improvements by adding markov-chains of higher order.
    
    
   **3) What is the advantage of using phase-extender ?**
   
    We generally need accurate phase state with in a gene/transcript level while doing ASE, dissecting maternal-paternal effects.  
    Long haplotypes are mostly important while preparing diploid genome, long range haplotype for testing selective sweeps, 
    within a QTL regions etc. 
    For emerging organism systems where CW,GW haplotypes are more difficult to solve, it is more important to solve haplotypes 
    within short gene regions. 
    But, most of RBphased Haplotype rarely cover full gene regions due to coverage issues, or due to short read sequence that 
    cannot cover whole gene, etc. In that situation, if we rather have short haplotype blocks from severals samples it can be 
    used to solve a full gene length haplotypes for each sample (one at a time). phase-extender comes handy at this stage; that 
    it takes the information from several haplotype blocks from other samples that are not broken at that
    point. So, we can solve haplotype configuration for SOI (sample of interest) pretty much easily. ***Also, with paired-end 
    read size getting increased in Illumina sequence data we can have more reliable full gene-level haplotypes for SOI.***
    
   **4) Does phase-extender phase InDels ?**
    Yes, but it is conditional. The InDels should already be phased to a reliable haplotype block. That way when the haplotype is
    being extended for those SNPs, InDels too get extended.
    
   **5) What is the minimal size of the haplotype block that is required? **  contd ....
    The bigger the two haplotypes, the better is the likelyhood test of which haplotype is phased with which. By, default
    I have kept this number to 3 variants (SNPs exclusive) per haplotype block that needs extension.
    
   Does phase-extender do GW (genome wide) or CW (chromosome wide) haplotype phasing?
    There are certain situation when phase-extender is able to do GW or CW haplotype phasing.
    A) If you have lots of samples where the haplotypes overlap between sample covers almost all chromosome length. In this
       case we can run phase-extender for each sample there by extending the haplotype. After this phase-extender can be 
       applied recursively on the updated data each time, there by extending the haplotypes for each sample to full chromosome
       length and possibly to to full genome wide length. This is going to be more and more possible due to samples being
       sequenced at higher coverage, increase in the size of paired-end sequence length, availability of large sequence reads
       like pac-bio reads. The method for doing is explained in this link/website ..... ??
       
    B) Another situation when GW/CW phase extension might be possible is when you have at least few samples which have haplotype 
       resolved at GW/CW level. These can include fully phased data like genome matrix file, fully phased VCF data, fully phased
       haplotype blocks. For this the fully phased sample should be provided as one single blocks in the group of sample that is
       fed to phase-extender. Here is an example.... ??
       
   Does phase-extender phase the SNPs that are not phased to any other SNPs?
    For, now phase-extender doesn't have algorithm built into it to do so. I am hoping to add that ASAP.
      
   Does phase-extender impute missing genotypes?
    No, it does not. I have not developed any algorithm so far to impute missing genotypes. Should anyone be interested in 
    developing an algorithm, and have data to do so; I would be more than happy to.  
    
   Does phase-extender use haplotype reference panel?
    No, until now. Though it should be possible in near future.
   
   Does phase-extender use recombination into account?
    No and possibly these feature will be of least importance in phase-extender. Phase-Extender mostly relies on already       
    phased haplotype block from other samples. These haplotype blocks which were phased in other sample but in SOI were used 
    to build transition probabilities. There is strong assumption that variation in other samples are not the result of 
    recombination but only mutation.
    
   Does phase-extender phase rare genotypes?
    Yes, it does. But, the rare genotype should be the part (phased to) of phased haplotype blocks. This is one of the good 
    thing about phase-extender compared to other tools when it come to rare genotype. When several SNPs are phased together 
    to extend the haplotype, rare genotypes are really hard to phase accurately - the reason being the statistical 
    significance of the rare genotype belonging to either two phase state is highly ambigous. But, if the rare genotype is 
    attached to a haplotye block supported by read-back phasing, this makes phasing of rare genotypes most accurate, since 
    the likely hoods are provided by other SNPs that are not rare.
    
   How fast is phase-extender?
    phase-extender is written in python-3, so it is slower than other tools that are built on the top of C, C++ or java.   
    Coming from a pure biology background, learning python was one of the most enduring task I have taking and then 
    building this tool was a big part of my PhD. I have optimized the part of calling VCF file using cyvcf2 (which is on 
    average 4 times faster than old pyVCF module). phase-extender is also optimized for being able to run on multiple 
    threads/process. But, if you are running phase-extender on big genome data and have very large number of samples, and    
    running on laptop I suggest running on one thread, which may be time consuming but will reduce memory burden.
    
   Who helped me during the preparation of this program?
    I have not been very fortunate to surround myself or at least get face to face help from savvy computer programmers.
    But, my heart is very thankful to people behind the web who have made me capable of working this problem out - Thanks 
    to many people on biostars, stackoverflow, seqanswer and google web searches.
    
   Does phase-extender do trio based phase extension?
   
   
   What should be the relatedness of my samples?
   
   
   Has phase-extender been tested?
   
   
   What is differece between phase extender and phase stitcher?
   
   
   
   Should I prepare my haplotype block file onlyusing phaser?
   
   
   
   
## Note:
- Basically this tool takes the paritally phased haplotypes
    - segregates them to maternal vs. paternal population haplotypes (using markov model)
    - stitches the haplotypes to create a genome wide haplotype
    
This program is exclusively designed to phase haplotypes in F1 hybrid for now and should work equally with data generated from genome reseq, RNAseq, WES or RAD given the individual is a hybrid derived from divergent strains and/or populations, but may be used to equally extended to phase F2 hybrids (work on progress).

# Prerequisites:
Python packages and modules
- Python3 (https://www.python.org/)
- pandas (http://pandas.pydata.org/)
- io
- pyvcf (https://github.com/jamescasbon/PyVCF)
- itertools
- collections
- functools

# Running phase-Stitcher

## Usage (**using the given test data in the example folder**): 

    python Stitcher_using_1stOrderMarkov_InteractiveMode.py --vcf1 MY_subSample.vcf --vcf2 SP_subSample.vcf --pop1 My --pop2 Sp --output test --het_vcf F1_subSample.vcf --f1_sample 2ms04h



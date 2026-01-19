**ASSIGNMENT 1**

**Introduction**

The objective of this project is to assemble a *Salmonella enterica* genome from Oxford Nanopore Technologies (ONT) long reads (R10 chemistry), then compare the resulting consensus assembly to a public reference genome from NCBI to identify and visualize variants.

The goal of genome assembly is described as producing complete, accurate sequences with minimal fragmentation. This has been described as the “one chromosome, one contig,” ideal (Koren & Phillippy, 2015).  Long reads help span repeats and structural complexity that commonly break short-read assemblies, but ONT reads historically have higher per-base error rates than Illumina, which can leave small errors that matter for downstream interpretation and variant calling (Wick & Holt, 2021). The choice of tools used in downstream steps therefore must be chosen in a way that best mitigates these errors.

**In order to achieve the objective of this project, the following workflow is required:**

**QC \-\> Assembly \-\> Polishing \-\> comparison to reference genome \-\> visualization.**  

There are a number of assembly tools that are used in literature. Benchmarks on prokaryotic long-read data consistently find that there are several top performing tools across different metrics, such as Flye (Kolmogorov et al., 2019). However, no single assembler performs perfectly on every metric (Wick & Holt, 2021). A potential solution would be to use consensus genome assembler tools, the most advanced being Autocycler (Wick et al., 2025\) that combine multiple input assemblies to produce a high-quality consensus. 

Post assembly, polishing corrects residual base-level mistakes by re-aligning reads to the draft assembly and refining the consensus. Comparative evaluations of microbial polishing strategies highlight that polishing is a critical step (Lee et al., 2021), once more with multiple tools offering varying results. Medaka is a commonly used tool used on long-reads that performs better than its competitors (Wick et al., 2023, Lee et al., 2021\) and is directly compatible with Flye, according to its developers (*Nanoporetech/Medaka*, 2021). 

The objective of comparison to a reference genome follows, where either raw reads are aligned to the reference genome or the assembled genome is aligned to the same. Minimap2 is an alignment tool designed for ONT reads (Li, 2018).

As a final step, in order to visualize the findings of the project, IGV is a standard tool for interactive genome data exploration (Thorvaldsdottir et al., 2012).

**Proposed Methods**

Data acquisition:

* Obtain ONT reads for *S. enterica*. Record dataset characteristics: read count, total bases, and read length distribution.

Pre-assembly Quality Check:

* Generation of basic QC summaries in order to interpret assembly outcomes and identify obvious data issues such as unexpectedly low yield or extreme read-length truncation.

De novo assembly:

* Assemble using **Flye**  
* Parameters to be defined beyond the defaults include:  
  *  \- \-nano- hq and \- \-read-error 0.03 as specified in documentation as being appropriate for R10 ONT data  
  * \- \- threads 16 as per computer specifications

Polishing:

* Medaka: neural-network consensus polishing on top of the draft/polished assembly, using a model appropriate for the basecaller/chemistry.   
* Parameters to be defined include:   
  * BASECALLS, DRAFT and OUTDIR variables, the \-t option for the number of CPU threads used   
  * The correct inference model according to basecaller used (Allowed values must be found by running medaka tools list\\\_models)  
  * the flag \-- bacteria that selects the bacterial model if it is compatible with the basecallers input

QC to check whether assembly is plausibly complete:

* The ideal would be few contigs, ideally closed chromosomes and plasmids

Reference genome selection (NCBI):

* Download complete/high-quality reference *S. enterica* reference genome from NCBI (record accession and strain/assembly level is to be taken into consideration).

Alignment and variant calling:

1. **Assembly-to-reference comparison**  
* Alignment of the polished assembly to the NCBI reference using **minimap2** (assembly/contig alignment settings).  
* Extraction of differences and summary of counts by type.

2. **Read-based validation**  
* Mapping of  ONT reads to the reference genome with minimap2 and processing alignments with **SAMtools** (sorting/indexing).  
* Medaka for small-variant (haploid) calling from long reads, since bacterial isolates are haploid. This must be enabled in the medaka\_variant workflow via: medaka\_variant \-i \<reads.fastq\> \-r \<ref.fasta\>

Visualization:

* Loading of alignments and variants into **IGV** to inspect representative SNPs/indels and any suspicious regions, such as regions with clustered mismatches or homopolymers.

# **References**

Kolmogorov, M., Yuan, J., Lin, Y., & Pevzner, P. A. (2019). Assembly of long, error-prone reads using repeat graphs. *Nature Biotechnology*, *37*(5), 540–546. https://doi.org/10.1038/s41587-019-0072-8  

Koren, S., & Phillippy, A. M. (2015). One chromosome, one contig: complete microbial genomes from long-read sequencing and assembly. *Current Opinion in Microbiology*, *23*, 110–120. https://doi.org/10.1016/j.mib.2014.11.014  

Lee, J. Y., Kong, M., Oh, J., Lim, J., Chung, S. H., Kim, J.-M., Kim, J.-S., Kim, K.-H., Yoo, J.-C., & Kwak, W. (2021). Comparative evaluation of Nanopore polishing tools for microbial genome assembly and polishing strategies for downstream analysis. *Scientific Reports*, *11*(1). https://doi.org/10.1038/s41598-021-00178-w  

Li, H. (2018). Minimap2: pairwise alignment for nucleotide sequences. *Bioinformatics*, *34*(18), 3094–3100. https://doi.org/10.1093/bioinformatics/bty191  
*nanoporetech/medaka*. (2021, April 15). GitHub. https://github.com/nanoporetech/medaka  

Thorvaldsdottir, H., Robinson, J. T., & Mesirov, J. P. (2012). Integrative Genomics Viewer (IGV): high-performance genomics data visualization and exploration. *Briefings in Bioinformatics*, *14*(2), 178–192. https://doi.org/10.1093/bib/bbs017  

Wick, R. R., & Holt, K. E. (2021). Benchmarking of long-read assemblers for prokaryote whole genome sequencing. *F1000Research*, *8*, 2138\. https://doi.org/10.12688/f1000research.21782.4  

Wick, R. R., Howden, B. P., & Stinear, T. P. (2025). Autocycler: long-read consensus assembly for bacterial genomes. *Bioinformatics*, *41*(9). https://doi.org/10.1101/2025.05.12.653612  

Wick, R. R., Judd, L. M., & Holt, K. E. (2023). Assembling the perfect bacterial genome using Oxford Nanopore and Illumina sequencing. *PLOS Computational Biology*, *19*(3), e1010905. https://doi.org/10.1371/journal.pcbi.1010905

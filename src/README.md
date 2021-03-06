## Diffacto: Differential (Factor) Analysis for Comparative Shotgun Proteomics
----
Requirements
----
[Anaconda](https://www.continuum.io/downloads) Python3.5

Packages needed:
* numpy 1.10+
* scipy 0.17+  
* pandas 0.18+
* networkx 1.10+
* scikit-learn 0.17+
* [pyteomics](https://pythonhosted.org/pyteomics) 3.3+

(Installation via _pip_ commands)   
<code> pip install numpy scipy pandas networkx scikit-learn pyteomics </code>

---

Usage
----

<code>python run_diffacto.py  -i quantification.csv [-parameters values] </code>

<pre>
-i input_file         Quantification result of peptides in CSV format.
                      (default: peptides.csv)

-db [database.fa]     Protein database in FASTA format. If None, the peptide
                      file must have protein ID(s) in the second column.
                      (default: None)

-samples [list.txt]   File of the sample list. One run and its sample group
                      per line, separated by tab. If None, read from peptide
                      file headings, then each run will be summarized as a
                      group. (default: None)

-log2 [False]         Input abundances are in log scale (True) or linear
                      scale (False) (default: False)

-normalize {average, median, GMM, None}
                      Method for sample-wise normalization. (default: None)

-farms_mu [0,1]       Hyperparameter mu (default: 0.1)

-farms_alpha [0,1]    Hyperparameter weight of prior probability (default: 0.1)

-reference [REF]      Names of reference sample groups (separated by semicolon)
                      (default: average)

-min_samples [1]      Minimum number of samples peptides needed to be
                      quantified in (default: 1)

-use_unique [False]   Use unique peptides only (default: False)

-impute_threshold [0,1]
                      Minimum fraction of missing values in the group.
                      Impute missing values if missing fraction is larger
                      than the threshold. (default: 0.99)

-cutoff_weight [0,1]
                      Peptides weighted lower than the cutoff will be
                      excluded (default: 0.5)

-fast [False]         Allow early termination in EM calculation when noise
                      is sufficiently small. (default: False)

-out [output]         Path to output file (writing in TSV format).
                      (default: <stdout>)

-mc_out [MC_OUT]      Path to MCFDR output (writing in TSV format).
                      (default: None)
</pre>            


----
Example
---

* Peptide abundances recorded in log scale. map peptides to the protein database HUMAN.fa, using GMM (Gaussian Mixture Model) for per-sample normalization, read sample groups in the file sampleLables.txt, and output protein quantification result to the file protein.txt. Peptide abundance will be scaled by comparing average abundances of all samples.

<code>
python run_diffacto.py -i peptides.csv -log2 True -db HUMAN.fa -normalize GMM -samples sampleLables.txt -out protein.txt
</code>


* Peptide abundances recorded in linear scale, using median abundances for per-sample normalization, read sample groups in the file sampleLables.txt, and output protein quantification result to the file protein.txt. Peptide abundance will be scaled by comparing to average abundances of samples labeled as of Sample1 and Sample3 in the sample list. Use peptides unique to the protein and quantified at least in 20 samples. For a given group of sample, if missing values consist more than 70% of the results, impute missing values at half of the minimum non-missing abundance. Apply sequential Monte Carlo permutation tests and estimate MCFDR for differentially expressed proteins.

<code>
python run_diffacto.py -i peptides.csv -out protein.txt -normalize median -samples sampleLables.txt -ref Sample1;Sample3  -use_unique True  -min_samples 20  -impute_threshold 0.7 -mc_out protein.MCFDR.txt
</code>

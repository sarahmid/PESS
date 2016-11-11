# PESS (Protein Empirical Structure Space) 

Sensitive protein fold recognition using an empirical structure space and 1NN.


## Getting started

External requirements:
- [Python](http://www.python.org/download) (2 or 3)
    - [NumPy](http://www.numpy.org)
    - [scikit-learn](http://scikit-learn.org/stable/)
- [CNFsearch](http://raptorx.uchicago.edu/download/) - part of the RaptorX threading package. Only version 1.66 has been tested.

Installation:

1. Download this repo & unzip.
1. Install required software. The [Anaconda](https://www.continuum.io/downloads) package manager is highly recommended for easier download of numpy and scikit-learn.
1. Test that everything is working by running the demo dataset (see below).


## Demo example

Using `demo.fa` (included with PESS download):

*Note: in the following, replace `<PATH_TO_PESS>` with the relative or absolute path to where you unzipped the PESS code, and replace `<NUM_CPU>` with the number of CPUs you want to utilize.*

1. Navigate to the directory where your RaptorX executables are stored. (You must run the PESS code from within this directory or RaptorX will not work properly.)

1. Run the threading script using the following command:<br />
    `python <PATH_TO_PESS>/pess_1.0.0/step1_threading.py <PATH_TO_PESS>/pess_1.0.0/demo/demo.fa --cpu=<NUM_CPU> --out="<PATH_TO_PESS>/pess_1.0.0/demo/demo_results"`<br />
*Note that this step will take several minutes per sequence, so we recommend using multiple CPU.*

1. Run the classification script using the following command:<br />
    `python <PATH_TO_PESS>/pess_1.0.0/step2_classification.py <PATH_TO_PESS>/pess_1.0.0/demo/demo_results/demo.scoremat --cpu=<NUM_CPU>`<br />
*This step is faster, but can still benefit from multiple CPUs.*

The output will be a file (`<PATH_TO_PESS>/pess_1.0.0/demo/demo_results/demo.scoremat.fold_preds.txt`) containing the predicted fold for each sequence, its distance to the nearest training neighbor (which can give some idea of confidence), and whether the prediction is considered high confidence or not (based on having a nearest neighbor distance of <= 17.5).

## Scripts

For these scripts, use -h to view options.

> ### `step1_threading.py`

    Threads protein sequences (provided in FASTA format) against a set of 1,814 representative templates 
    using the CNFalign_lite module of RaptorX. Outputs a table of scores (.scoremat)

    Notes:
        - IMPORTANT: This script must be run from within the directory that holds the RaptorX executables, 
          otherwise those executables won't run properly.
        - Individual fasta files are created for each input sequence in a folder called /indiv_fasta/
        - Feature profiles are built, if they don't already exist, in /tgt_files/
        - These folders are created in same folder as the fasta file unless otherwise specified with --out
        - If the feature files (phase I) already exist, the script can start at threading (phase II). You 
          will be prompted to choose whether to do this when the script detects that /tgt_files/ exists.

    Usage:
        python step1_threading.py FASTA [options]
   
    Examples:
        python ~/pess/step1_threading.py ~/pess/demo/demo.fa
        python ~/pess/step1_threading.py ~/pess/demo/demo.fa --cpu=32 --out="~/pess/demo/demo_results"


> ### `step2_classification.py`

    Given a .scoremat file (generated by step1_threading.py), outputs fold classifications for each
    sequence using a nearest neighbor (NN) classifier. Sequences with a NN distance of <= 17.5 are
    marked as "high" confidence. All predictions are output to the .fold_preds.txt file. 

    Notes:
        - Requires Numpy and scikit-learn
        - Output file is <SCOREMAT>.fold_preds.txt

    Usage:
       python step2_classification.py SCOREMAT [options]
 
    Usage examples: 
       python ~/pess/step2_classification.py ~/pess/demo/demo.scoremat --cpu=4


## Reference
Middleton, SA, Illuminati, J, and Kim, J. 2016. Complete fold annotation of the human proteome using a novel structural feature space. (Submitted)

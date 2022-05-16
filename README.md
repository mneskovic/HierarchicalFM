# HierarchicalFM
Implementation and analysis of hierarchical fm-index over the compacted de Bruijn graph

## Running

All necessary libraries must be installed on the system. We did so using bioconda. Necessary packages (at minumum) include the cuttlefish, hisat, pufferfish, and sshash libraries. 

### Hierarchical FM-Index
To run the HFM over a CDBG, we must create a CDBG and then use the hisat library to run queries on it. An example of running this on the ecoli dataset in your current directory would be to run the following commands:
- `mkdir temp`
- `cuttlefish build -s ecoli.fa -t 4 -o cdbg -f 1 -w temp/`
- `awk \'/^S/{{print ">"$2"\\n"$3}}\' cdbg.gfa1 | fold > cdbg.fa`
- `mkdir index`
- `hisat2-build cdbg.fa index/index`
- `hisat2 -f -x index/index -U query.fa -S e1g.sam`
The index itself (set of index files) will be located in the index directory, while the results of running our queries will be located in the e1g.sam file. The SAM file includes the set of all of the contigs and positions (as well as other useful metadata) for each of our queries from the query FASTA.

### runIndexing Script
This script runs an index given an input FASTA, index type, and query FASTA. It takes the given input FASTA, creates a CDBG for it, creates an index over the CDBG and then runs the given queries, then records the size of the index and time for index to run in the analytics.txt file. The script is purely for making it easier to gather data, so the index and related files are deleted with each run.

### Queries
To generate a larger set of query reads, we used wgsim. The following commands simulates 1 million reads with an error rate of 0.05, where output will be stored in the testr1.fq and testr2.fq files, and converts the testr1.fq into a FASTA file.
- `wgsim -e 0.05 ecoli.fa testr1.fq testr2.fq`
- `cat testr1.fq | awk '{if(NR%4==1) {printf(">%s\n",substr($0,2));} else if(NR%4==2) print;}' > query.fa`

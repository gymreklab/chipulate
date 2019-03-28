# ChIPulate
A Python3 framework to simulate read counts in a ChIP-seq experiment. 

## Getting Started

ChIPulate requires the following Python3 libraries installed --- ```argparse```,```numpy```,```scipy```, ```pybedtools``` and ```pandas```. ChIPulate has been tested to work with these versions of these libraries ---
    argparse : `1.1`
    numpy : `1.15.2`
    scipy : `1.1.0`
    pandas : `0.23.4`
    pybedtools : `0.8.0`
    
These packages can be installed using the Python3 ```pip``` installer with the command ```pip3 install <package>```. In addition, `pybedtools` needs `bedtools` to be pre-installed on the system in order to run. 

## Running ChIPulate
	usage: chipulate.py [-h] [--mu-A MU_A] [--mu-B MU_B]
			    [-c CONTROL_CELL_FRACTION] [-b INPUT_BG] [-n NUM_CELLS]
			    [-d DEPTH] [-p PCR_CYCLES] -i INPUT_FILE
			    [--chrom-size-file CHROM_SIZE_FILE] [-g GENOME_FILE]
			    [-o OUTPUT_PREFIX] [--output-dir OUTPUT_DIR]
			    [--read-length READ_LENGTH]
			    [--fragment-length FRAGMENT_LENGTH]
			    [--fragment-jitter FRAGMENT_JITTER]

	The ChIPulate pipeline for simulating read counts in a ChIP-seq experiment

	optional arguments:
	  -h, --help            show this help message and exit
	  --mu-A MU_A           Chemical potential (in units of k_B T) of TF A, where
				A is the target TF of the ChIP-seq. (default: 3.0)
	  --mu-B MU_B           Chemical potential (in units of k_B T) of TF B, where
				B is a second TF that may be involved in cooperative
				or indirect interactions with A, the target TF of the
				ChIP-seq. (default: 3.0)
	  -c CONTROL_CELL_FRACTION, --control-cell-fraction CONTROL_CELL_FRACTION
				Control cell ratio. This is the fraction ofthe number
				of cells used in the ChIP sample that is used for the
				control sample. This value should be between 0 and 1.
				Setting this parameter to 1 sets the number of cells
				used in the ChIP and control samples to 1. (default:
				0.1)
	  -b INPUT_BG, --input-bg INPUT_BG
				Background binding energy (in units of k_BT) in the
				input sample of the ChIP-seq experiment. Must be less
				than the unbound energy. A higher value indicates
				weaker binding in the input sample. (default: 1.0)
	  -n NUM_CELLS, --num-cells NUM_CELLS
				Number of cells used in the ChIP sample of the
				experiment. A progressive increase in this value slows
				down the simulation. (default: 100000)
	  -d DEPTH, --depth DEPTH
				Sequencing depth. We define this as the number of
				reads expected per binding location if an equal number
				of reads came from each location. The total number of
				sequence reads used is the product of the sequencing
				depth and the number of binding locations. A
				fractional value can be passed if the total number of
				reads is less than the number of binding locations.
				The depth is set to be equal in both ChIP and input
				samples. (default: 100)
	  -p PCR_CYCLES, --pcr-cycles PCR_CYCLES
				Number of cycles employed in the PCR amplification
				step. (default: 15)
	  -i INPUT_FILE, --input-file INPUT_FILE
				File name of a tab-separated file that contains
				location-wise information about the genome being
				simulated and the experimental parameters of the ChIP-
				seq. Each line contains an entry of the form <p_ext>
				<p_amp> <binding_energy_A> <|binding_energy_B|>
				<|binding_type|> <|interaction energy|> <|sequence|>
				<|chrom_accessibility|>, where the columns enclosed in
				|..| are optional. See README for more information on
				each column. (default: None)
	  --chrom-size-file CHROM_SIZE_FILE
				File containing sizes of each chromosome. This
				argument is ignored when the chr, start and end
				columns are not specified in the input file. If these
				columns are specified, a tab-separated file where the
				first two columns contain the chromosome name and
				size, respectively, must be supplied. (default: )
	  -g GENOME_FILE, --genome-file GENOME_FILE
				File containing a genome sequence in FASTA format. If
				FASTA output is requested (by specifying the chr,
				start and end columns in the input file), a single
				FASTA file containing the genome sequence must be
				specified. If chr, start and end columns are not
				specified in the input, then this argument is ignored.
				(default: )
	  -o OUTPUT_PREFIX, --output-prefix OUTPUT_PREFIX
				Prefix of the output file. The\ output is a tab
				separated file that lists the following\ columns ---
				<chip_reads> <unique_chip_reads> <control_reads>\
				<unique_control_reads>. See README for more
				information on\ each column. (default: None)
	  --output-dir OUTPUT_DIR
				Directory to which all output should be written.
				Ensure that you have write privileges to this
				directory. (default: None)
	  --read-length READ_LENGTH
				Read length (in base pairs) to simulate. This must be
				smaller than the fragment length(s) specified in the
				--fragment-length argument, and is a required argument
				if FASTQ output is requested. Only single-end reads
				are simulated. (default: 150)
	  --fragment-length FRAGMENT_LENGTH
				Fragment length (in base pairs) to simulate. This must
				be larger than the read length specified for --read-
				length. (default: 200)
	  --fragment-jitter FRAGMENT_JITTER
				Variation in the starting position of fragments (in
				base pairs) at a genomic region. A larger value leads
				to a greater variation in start positions of fragments
				in the ChIP sample only. This parameter does not
				affect the starting position of fragments in the
				control sample. (default: 20)
				
	  --library-type LIBRARY_TYPE
				Type of sequencing library. This can be set to either
				"single-end" or "paired-end". If single-end is
				specified, a single BED and FASTQ file is generated
				for ChIP and control samples. If "paired-end" is
				specified, paired-end reads are simulated. In this
				case, two sets of BED and FASTQ files are produced for
				each of the ChIP and control samples.The BED and FASTQ
				files containing reads from the forward and reverse
				strands are suffixed with _R1 and _R2, respectively.
				(default: single-end)
				
	
## Input file
	
The header of the GENOME_FILE input must contain the following column items, of which ```p_ext```, ```p_amp``` and ```energy_A``` are the minimum columns that must be specified to run ChIPulate (see the Examples section of the README). The GENOME_FILE columns represent the following quantities:

	chr --- Chromosome coordinate of each genomic region. This is mandatory for BED and FASTQ files to be generated.
	start --- Starting position of each genomic region. This is mandatory for BED and FASTQ files to be generated.
	end ---- End position of each genomic region. This is mandatory for BED and FASTQ files to be generated.
	name --- Names for each entry. This is optional. If this column is not specified, each entry will be called region_1, region_2, ... .
	p_ext --- The extraction efficiency at each genomic location. The value must
	lie between 0 and 1.
	p_amp --- PCR efficiency at each genomic location, which must lie between 0
	and 1. The mean number of amplified fragments at a location is (1 + p)^n, where
	p is the PCR efficiency at the location and n is the number of PCR cycles. Note
	that the PCR efficiencies are truncated to two decimal places in order to speed
	up the process of computing the number of amplified fragments obtained at each
	genomic location.  
	energy_A --- Binding energies of TF A at each location, which is the
	target TF of the ChIP-seq. A binding energy of 0 represents the strongest
	binding site with positive values representing weaker binding. 
	energy_B --- Binding energies of TF B at each location. This is only
	in case a second TF is being simulated.
	binding_type --- When no second TF is supplied, every location is considered
	to be directly bound by the TF A i.e., the value at each location defaults to
	"direct". If a second TF is supplied, the binding_type can be either be
	"direct" or "indirect". 
	int_energy--- When binding energies for A and B are supplied, the
	interaction energy at each location determines whether it is cooperatively,
	competitively or independently bound. At a given location, a negative value
	represents a cooperative interaction, a positive value represents a competitive
	interaction and a value of zero represents no interaction i.e., independent
	binding. 
	sequence--- The sequence of the region being bound. This sequence can be of
	any length. 
	chrom_accessibility--- The chromatin accessibility at each location. This is
	a value that lies between 0 and 1.

## Output generated	

The main output file, with the extension ```.chipulate.out```, is a tsv file that includes the following columns in addition to the columns specified in the input file ---

	chip_reads --- The number of reads in the ChIP sample at each genomic
	location. This includes both unique reads and duplicate reads arising from
	sequencing PCR duplicate fragments during library preparation.
	unique_chip_reads --- The number of unique reads in the ChIP sample at each
	genomic location.
	control_reads --- The number of reads in the control sample at each genomic
	location. This includes both unique reads and duplicate reads arising from
	sequencing PCR duplicate fragments during library preparation.
	unique_control_reads --- The number of unique reads in the control sample at
	each genomic location.
	
Two more files are generated in addition to the ```.chipulate.out``` file, which are suffixed with ```.chipulate.out.run_info``` and ```.chipulate.out.diag_output```. The run_info file conains the parameters with which the output was generated  while the '.diag_output' contains results from the intermediate steps of computation when the read counts are generated. See
the Examples section for more details.

If `chr`,`start` and `end` positions are supplied in the input file, then additional `.bed` and `.fastq` files are generated by ChIPulate, one each for the ChIP and control samples. The `.bed` file generated contains six columns (these are the first six columns of the standard BED format https://genome.ucsc.edu/FAQ/FAQformat.html#format1). These columns are coordinates of single-end reads, the name of each read (with duplicates marked), and the strand from which they originated. The `.fastq` files contain the sequences of each read, along with a fixed quality score of each read in the phred+33 scale. 

## Running the examples

### Minimum working example

The file ```basicExample.tsv``` in the ```examples``` folder is the minimum working input required to run ```chipulate.py```. The contents of the file are the following (10 locations are being simulated in this example) ---

	p_ext	p_amp	energy_A
	0.539179	0.18	0.15
	0.505944	0.58	0.15
	0.498672	0.58	0.41
	0.479857	0.79	0.15
	0.494356	0.58	0.15
	0.554812	0.58	0.15
	0.545281	0.38	0.41
	0.492197	0.58	0.41
	0.503651	0.58	0.41
	0.578344	0.28	0.15

To execute ```chipulate.py``` on this file with the remaining parameters set to their default values, run the following command ---

	python3 chipulate.py -i basicExample.tsv
	
No output file is specified in the above syntax. When this is the case, the input file name is used as a prefix to generate the  three output files described above. In this case, the files are named ```basicExample.chipulate.out``` (the main output file), ```basicExample.chipulate.out.diag_output``` (contains intermediate output from ChIPulate along with the output from the main output file) and ```basicExample.chipulate.run_info``` (contains run information about the parameters of the simulation). 


#### Main output 

In a run of the command ```python3 chipulate.py -i basicExample.tsv```, the contents of ```basicExample.chipulate.out``` are ---

	p_ext	p_amp	energy_A	chip_reads	unique_chip_reads	control_reads	unique_control_reads
	0.539179	0.18	0.15	0	0	0	0
	0.505944	0.58	0.15	84	70	82	66
	0.498672	0.58	0.41	84	66	98	81
	0.479857	0.79	0.15	481	185	487	187
	0.494356	0.58	0.15	73	57	76	65
	0.554812	0.58	0.15	71	58	77	69
	0.545281	0.38	0.41	11	11	6	6
	0.49219700000000005	0.58	0.41	81	66	75	64
	0.5036510000000001	0.58	0.41	109	80	93	71
	0.578344	0.28	0.15	6	5	6	6

#### .run_info output
The information on the parameters used to generate this output are in ```basicExample.chipulate.out.run_info``` ---

	Number of cells in ChIP sample : 100000
	Control cell ratio : 0.1
	Number of cells in control sample : 10000
	Chemical Potential of A : 3.0
	Number of PCR cycles : 15
	Sequencing depth : 100
	Total read count : 1000
	
#### .diag_output diagnostic output
The diagnostic output generated is in ```basicExample.chipulate.out.diag_output``` ---

	name	energy_A	binding	sequence	p_occ_chip	p_occ_bg	chip_fragments	control_fragments	unique_control_reads	control_reads	unique_chip_reads	chip_reads	amp_control_fragments	amp_chip_fragments	ext_control_fragments	ext_chip_fragments	read_count_ratio
	1	0.15	direct		0.9453186827840592	0.04742587317756678	94473	497	0	0	0	0	3396	2674	284	239	
	2	0.15	direct		0.9453186827840592	0.04742587317756678	94503	478	66	82	70	84	234729	204422	245	222	1.0606060606060606
	3	0.41	direct		0.9302152171234199	0.04742587317756678	93037	463	81	98	66	84	238212	227874	243	233	0.8148148148148148
	4	0.15	direct		0.9453186827840592	0.04742587317756678	94359	455	187	487	185	481	1298190	1352338	211	217	0.9893048128342246
	5	0.15	direct		0.9453186827840592	0.04742587317756678	94461	465	65	76	57	73	215390	214698	235	224	0.8769230769230769
	6	0.15	direct		0.9453186827840592	0.04742587317756678	94465	461	69	77	58	71	239417	251417	257	240	0.8405797101449275
	7	0.41	direct		0.9302152171234199	0.04742587317756678	93046	472	6	6	11	11	31739	35954	248	265	1.8333333333333333
	8	0.41	direct		0.9302152171234199	0.04742587317756678	93064	456	64	75	66	81	206097	221844	220	227	1.03125
	9	0.41	direct		0.9302152171234199	0.04742587317756678	93089	456	71	93	80	109	209127	273018	221	260	1.1267605633802817
	10	0.15	direct		0.9453186827840592	0.04742587317756678	94540	500	6	6	5	6	11240	11229	279	285	0.8333333333333334

### Running ChIPulate with command-line parameters different from the default values

The sequencing depth, chemical potential, number of cells used in the control sample, and the number of PCR cycles can be changed from the command line. The remaining experimental parameters such as extraction efficiency, amplification efficiency need to be changed in the input file. 

The following command runs the file ```basicExample.tsv``` with different parameters and the output prefix set to a user-defined value ---

	python3 chipulate.py -i examples/basicExample.tsv --mu-A 1.5 --depth 300 --num-cells 10000 --control-cell-fraction 0.4 -o examples/mwe
	
The main output is now written to ```examples/mwe.chipulate.out``` ---

	p_ext	p_amp	energy_A	chip_reads	unique_chip_reads	control_reads	unique_control_reads
	0.539179	0.18	0.15	2	2	1	1
	0.505944	0.58	0.15	207	64	240	84
	0.498672	0.58	0.41	301	82	247	83
	0.479857	0.79	0.15	1368	69	1513	91
	0.494356	0.58	0.15	221	71	211	65
	0.554812	0.58	0.15	264	85	234	80
	0.545281	0.38	0.41	40	28	39	31
	0.49219700000000005	0.58	0.41	261	84	240	84
	0.5036510000000001	0.58	0.41	329	91	260	79
	0.578344	0.28	0.15	7	7	15	13
	
The file ```examples/mwe.chipulate.run_info``` shows the modified parameters ---

	Number of cells in ChIP sample : 10000
	Control cell ratio : 0.4
	Number of cells in control sample : 4000
	Chemical Potential of A : 1.5
	Number of PCR cycles : 15
	Sequencing depth : 300.0
	Total read count : 3000.0
	
The diagnostic output is written to ```examples/mwe.chipulate.diag_output``` ---

	name	energy_A	binding	sequence	p_occ_chip	p_occ_bg	chip_fragments	control_fragments	unique_control_reads	control_reads	unique_chip_reads	chip_reads	amp_control_fragments	amp_chip_fragments	ext_control_fragments	ext_chip_fragments	read_count_ratio
	1	0.15	direct		0.7941296281990528	0.04742587317756678	7964	169	1	1	2	2	1096	1431	90	102	2.0
	2	0.15	direct		0.7941296281990528	0.04742587317756678	7959	172	84	240	64	207	95250	65008	98	77	0.7619047619047619
	3	0.41	direct		0.7483817216070642	0.04742587317756678	7487	181	83	247	82	301	88268	84959	93	94	0.9879518072289156
	4	0.15	direct		0.7941296281990528	0.04742587317756678	7983	184	91	1513	69	1368	546708	436389	92	69	0.7582417582417582
	5	0.15	direct		0.7941296281990528	0.04742587317756678	7907	163	65	211	71	221	64847	73790	71	79	1.0923076923076922
	6	0.15	direct		0.7941296281990528	0.04742587317756678	7980	184	80	234	85	264	88031	90382	102	101	1.0625
	7	0.41	direct		0.7483817216070642	0.04742587317756678	7451	193	31	39	28	40	13153	10078	100	82	0.9032258064516129
	8	0.41	direct		0.7483817216070642	0.04742587317756678	7478	199	84	240	84	261	97850	88916	98	95	1.0
	9	0.41	direct		0.7483817216070642	0.04742587317756678	7551	187	79	260	91	329	88385	103121	93	106	1.1518987341772151
	10	0.15	direct		0.7941296281990528	0.04742587317756678	7963	191	13	15	7	7	4572	3334	102	84	0.5384615384615384

### Running ChIPulate with indirect binding between two transcription factors

When indirect binding between two TFs is to be simulated, the ```binding_type```, ```energy_B``` and ```int_energy``` columns need to be specified. See ```examples/indirectExample.tsv``` for an example. Its contents are pasted below ---

	p_ext	p_amp	energy_A	binding_type	energy_B	int_energy
	0.539179	0.18	2.15	direct	0.20	0
	0.505944	0.58	1.85	direct	0.20	0
	0.498672	0.58	3.41	direct	0.31	0
	0.479857	0	9.79	direct	2	0
	0.494356	0.58	6.15	direct	0	0
	0.554812	0.58	5.15	direct	0.20	0
	0.545281	0.38	2.15	indirect	0	0
	0.492197	0.58	1.85	indirect	0	0
	0.503651	0.58	3.41	indirect	0	0
	0.578344	0.28	9.79	indirect	0	0
	0.578344	0.28	5.15	indirect	0	0
	0.578344	0.28	2.15	indirect	0	0

After running ChIPulate with the command ```python3 chipulate.py -i examples/indirectExample.tsv -o examples/indirectExample```, the output is written to ```indirectExample.chipulate.out``` ---

	p_ext	p_amp	energy_A	binding_type	energy_B	int_energy	chip_reads	unique_chip_reads	control_reads	unique_control_reads
	0.539179	0.18	2.15	direct	0.2	0	3	3	1	1
	0.505944	0.58	1.85	direct	0.2	0	266	167	197	123
	0.498672	0.58	3.41	direct	0.31	0	143	84	167	110
	0.479857	0.0	9.79	direct	2.0	0	0	0	0	0
	0.494356	0.58	6.15	direct	0.0	0	14	11	169	109
	0.554812	0.58	5.15	direct	0.2	0	35	21	231	155
	0.545281	0.38	2.15	indirect	0.0	0	35	32	25	25
	0.49219700000000005	0.58	1.85	indirect	0.0	0	316	186	190	129
	0.5036510000000001	0.58	3.41	indirect	0.0	0	319	197	194	114
	0.578344	0.28	9.79	indirect	0.0	0	17	16	10	10
	0.578344	0.28	5.15	indirect	0.0	0	30	29	10	10
	0.578344	0.28	2.15	indirect	0.0	0	22	21	6	6

### Simulating cooperative and competitive binding with ChIPulate, along with sequences at each location

In the example for indirect binding, all the interaction energies between both transcription factors are set to zero. To simulate cooperative or competitive binding, the interaction energies can be set to negative or positive values, respectively. The file ```coopExample.tsv``` below shows an example of this, along with sequences to be associated with each location. 

	p_ext	p_amp	energy_A	sequence	binding_type	energy_B	int_energy
	0.539179	0.18	0.15	GTCACGTGAT	direct	0.20	-2
	0.505944	0.58	0.15	GTCACGTGAT	direct	0.20	-2
	0.498672	0.58	0.41	ATCAGGTTAGCAGAT	direct	0.31	-2
	0.479857	0.79	0.15	GTCACGTATAAGAT	direct	0	0
	0.494356	0.58	0.15	GTCAtaaataCGTGAT	direct	0	-1
	0.554812	0.58	0.15	GTCAgacaCGTGAT	direct	0.20	3
	0.545281	0.38	0.41	ATCAGGTGAT	direct	0.20	2
	0.492197	0.58	0.41	ATCAGGTGAT	direct	0.59	-1
	0.503651	0.58	0.41	ATCAGGTGAT	direct	0	0
	0.578344	0.28	0.15	GTCACGTGAT	direct	0.20	0

When the command ```python3 chipulate.py -i examples/coopExample.tsv --output-dir examples --output-prefix coopExample``` is run, the output is written to ```examples/coopExample.chipulate.out```

	p_ext	p_amp	energy_A	sequence	binding_type	energy_B	int_energy	chip_reads	unique_chip_reads	control_reads	unique_control_reads
	0.539179	0.18	0.15	GTCACGTGAT	direct	0.2	-2	1	1	1	1
	0.505944	0.58	0.15	GTCACGTGAT	direct	0.2	-2	81	67	81	67
	0.498672	0.58	0.41	ATCAGGTTAGCAGAT	direct	0.31	-2	78	62	69	60
	0.479857	0.79	0.15	GTCACGTATAAGAT	direct	0.0	0	524	204	504	196
	0.494356	0.58	0.15	GTCAtaaataCGTGAT	direct	0.0	-1	60	50	77	61
	0.554812	0.58	0.15	GTCAgacaCGTGAT	direct	0.2	3	100	79	98	80
	0.545281	0.38	0.41	ATCAGGTGAT	direct	0.2	2	11	11	9	9
	0.49219700000000005	0.58	0.41	ATCAGGTGAT	direct	0.59	-1	63	56	83	70
	0.5036510000000001	0.58	0.41	ATCAGGTGAT	direct	0.0	0	78	63	71	54
	0.578344	0.28	0.15	GTCACGTGAT	direct	0.2	0	4	4	7	7


## Running ChIPulate to generate FASTQ files from genomic regions

### Basic example (single-end reads)
In order to run ChIPulate in FASTQ generation mode, genomic intervals need to be specified for each entry in the input file. An example of this is the `examples/fastq-basicExample.tsv` file shown below---

	chr	start	end	p_ext	p_amp	energy_A
	chr1	1523	1773	0.539179	0.18	0.15
	chr1	5612	5862	0.505944	0.58	0.15
	chr1	11241	11491	0.498672	0.58	0.41
	chr1	11620	11870	0.479857	0.79	0.15
	chr1	20066	20316	0.494356	0.58	1.15
	chr1	22716	22966	0.554812	0.58	0.15
	chr1	35534	35784	0.545281	0.38	0.41
	chr1	48914	49164	0.492197	0.58	9.41
	chr1	92766	93016	0.503651	0.58	0.41
	chr1	156041	156241	0.578344	0.28	0.15
	
In addition to the `chr`,`start` and `end` positions being supplied for each entry, the `--genome-file` and `--chrom-size-file` arguments must be supplied. The genome file must be a single FASTA file that contains the sequences of all chromosomes in the genome. The second argument should be a tab-separated file containing at least two columns, where the first two columns contain the chromosome name (with the same names as in the FASTA file) and the length of each chromosome. Such a file is commonly generated using the `faidx` command from the `samtools` suite. 

If no `chr`,`start` and `end` columns are supplied in the input file, the `--genome-file`, `--chrom-size-file` and other arguments related to the FASTQ generation mode (namely, `--read-length`, `--fragment-length` and `--fragment-jitter`) will be disregarded, and no `.bed` or `.fastq` files will be output by ChIPulate.

The following command is a minimum working example of the FASTQ generation mode. The default fragment length used to generate fragments is 200 bp, the default read length is 150 bp and the fragment jitter is 20 bp (explained below). 

	python3 chipulate.py --input-file examples/fastq-basicExample.tsv --genome-file examples/yeast-genome.fa --chrom-size-file examples/yeast-genome.fa.fai --output-dir examples/
	
To run this example, ensure that the `yeast-genome.fa.gz` file in the `examples/` folder is extracted. The `yeast-genome.fa.fai` provided in the `examples/` folder was obtained by running the command `samtools faidx yeast-genome.fa`, which gives the following contents---

	chr1	230218	6	60	61
	chr2	813184	234067	60	61
	chr3	316620	1060811	60	61
	chr4	1531933	1382714	60	61
	chr5	576874	2940186	60	61
	chr6	270161	3526681	60	61
	chr7	1090940	3801351	60	61
	chr8	562643	4910480	60	61
	chr9	439888	5482507	60	61
	chr10	745751	5929734	60	61
	chr11	666816	6687922	60	61
	chr12	1078177	7365859	60	61
	chr13	924431	8462013	60	61
	chr14	784333	9401859	60	61
	chr15	1091291	10199272	60	61
	chr16	948066	11308759	60	61
	chrM	85779	12272633	60	61

The first two columns contain the chromosome names and sizes. Note that a file containing only the first two columns will suffice for ChIPulate. Any additional tab-separated columns provided will be ignored.

Two `.bed` files are output from running the above command --- `fastq-test.chip_reads.bed` and `fastq-test.control_reads.bed`. 
The first five lines of `fastq-test.chip_reads.bed` generated in a sample run are ---

	chr1	5659	5809	region_2_read_1	1	+
	chr1	5632	5782	region_2_read_2	1	+
	chr1	5713	5863	region_2_read_3	1	-
	chr1	5629	5779	region_2_read_4	1	-
	chr1	5701	5851	region_2_read_5	1	-

These five lines represent unique single-end reads that are not PCR duplicates. Duplicates reads carry the same name, as shown below for reads from `region_4` ---

	chr1	11745	11895	region_4_read_40	1	-
	chr1	11745	11895	region_4_read_40	1	-

In addition to these two `.bed` files, two FASTQ files are generated --- `fastq-test.chip_reads.fastq` and `fastq-test.control_reads.fastq`. The first five entries of `fastq-test.chip_reads.fastq` generated in a sample run are ---

	@region_2_read_1(+)
	ATCAAATGCACTAATATTGTAACGTTCTTACAAAGGGCAGACAACTTGAGAACTTTCATGCGTGCAACAGTATTAATATTTTACTGTCTTGATATCGTTATCCTCATCGTAACGTGAATTTTTTTGTCTCATACGTTAAGGTAAATTTTG
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_2(+)
	CTTTATTATGTGGCCGAATCAACATTAATCAAATGCACTAATATTGTAACGTTCTTACAAAGGGCAGACAACTTGAGAACTTTCATGCGTGCAACAGTATTAATATTTTACTGTCTTGATATCGTTATCCTCATCGTAACGTGAATTTTT
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_3(-)
	AAACAATAAAAGGGTGCTTTATACAGTAAGGCAAACAAGGACAACGGGGGTCATCAAAATTTACCTTAACGTATGAGACAAAAAAATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAA
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_4(-)
	AATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAAAGTTCTCAAGTTGTCTGCCCTTTGTAAGAACGTTACAATATTAGTGCATTTGATTAATGTTGATTCGGCCACATAATAAAGTTT
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_5(-)
	GGTGCTTTATACAGTAAGGCAAACAAGGACAACGGGGGTCATCAAAATTTACCTTAACGTATGAGACAAAAAAATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAAAGTTCTCAAGTT
	+

The read names correspond to the read names generated in the respective BED files, and indicate the strand orientation (`+/-`) in parentheses. By default, the maximum base quality is set at `K` in the Phred-33 scale. 

The figure below is a snap-shot of reads aligned to `region_7`. To generate this image, reads from both chip and control samples were aligned to the yeast genome (`examples/yeast-genome.fa`) using `bwa`, and the resulting SAM files were converted to BAM, sorted, and indexed, using `samtools`. The sorted BAM files obtained were input to Integrated Genome Viewer (IGV). The reads aligned to `region_7` (the genomic interval `chr1:35534-35784`) in both ChIP and input samples are shown below ---

![IGV snapshot of region_7 when --fragment-jitter is set to 20](examples/igv_region_7_snapshot1.png)

The read length and fragment length can be altered by specifying the `--read-length` and `--fragment-length` parameter. In the snapshot above, these were both set to their default values (150 bp and 200 bp respectively). The `--fragment-jitter` parameter was set to 20 bp.  An increase in the `--fragment-jitter` parameter "smears" out the reads by increasing the variability of the start positions of fragments. To see this, run the following command, where the fragment jitter is set to 50 bp --- 

	python3 chipulate.py --input-file examples/fastq-basicExample.tsv --genome-file examples/yeast-genome.fa --chrom-size-file examples/yeast-genome.fa.fai --output-dir examples/ --fragment-jitter 50

The reads obtained from this run are shown in the snapshot below. The increase in the apparent extent of the peak in `region_7` can be clearly seen.

![IGV snapshot of region_7 when --fragment-jitter is set to 50](examples/igv_region_7_snapshot2.png)

### Paired-end mode

Specifying `--libraryType paired-end` allows ChIPulate to output paired-end reads ---

	python3 chipulate.py --input-file examples/fastq-basicExample.tsv --genome-file examples/yeast-genome.fa --chrom-size-file examples/yeast-genome.fa.fai --output-dir examples/

In this case, two sets of BED and FASTQ files are generated for the ChIP and control samples. Reads mapping to the forward strands are stored in files suffixed with `_R1` and those mapping to the reverse strand are stored in files suffixed with `_R2`. The above command creates four BED files and four FASTQ files in the`examples/` folder with the filename prefixes as  `fastq-test.chip_reads_R1.bed`,`fastq-test.chip_reads_R2`, `fastq-test.control_reads_R1`, `fastq-test.control_reads_R2`. The read names in the `_R1` file end in `/1` and the read from the other end of the fragment ends in `/2`. An example is shown below from `fastq-test.chip_reads_R1.fastq` and `fastq-test.chip_reads_R2.fastq` ---

The first five reads in `fast-test.chip_reads_R1.fastq` are

	@region_2_read_1/1
	CAAATGCACTAATATTGTAACGTTCTTACAAAGGGCAGACAACTTGAGAACTTTCATGCGTGCAACAGTATTAATATTTTACTGTCTTGATATCGTTATCCTCATCGTAACGTGAATTTTTTTGTCTCATACGTTAAGGTAAATTTTGAT
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_2/1
	TATTATGTGGCCGAATCAACATTAATCAAATGCACTAATATTGTAACGTTCTTACAAAGGGCAGACAACTTGAGAACTTTCATGCGTGCAACAGTATTAATATTTTACTGTCTTGATATCGTTATCCTCATCGTAACGTGAATTTTTTTG
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_3/1
	TGTGGCCGAATCAACATTAATCAAATGCACTAATATTGTAACGTTCTTACAAAGGGCAGACAACTTGAGAACTTTCATGCGTGCAACAGTATTAATATTTTACTGTCTTGATATCGTTATCCTCATCGTAACGTGAATTTTTTTGTCTCA
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_4/1
	TTATTATGTGGCCGAATCAACATTAATCAAATGCACTAATATTGTAACGTTCTTACAAAGGGCAGACAACTTGAGAACTTTCATGCGTGCAACAGTATTAATATTTTACTGTCTTGATATCGTTATCCTCATCGTAACGTGAATTTTTTT
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_5/1
	AACTTTATTATGTGGCCGAATCAACATTAATCAAATGCACTAATATTGTAACGTTCTTACAAAGGGCAGACAACTTGAGAACTTTCATGCGTGCAACAGTATTAATATTTTACTGTCTTGATATCGTTATCCTCATCGTAACGTGAATTT
	+

The corresponding reads from the reverse strand, stored in `fastq-test.chip_reads_R2.fastq` are 

	@region_2_read_1/2
	ACAATAAAAGGGTGCTTTATACAGTAAGGCAAACAAGGACAACGGGGGTCATCAAAATTTACCTTAACGTATGAGACAAAAAAATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAAAG
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_2/2
	AGGCAAACAAGGACAACGGGGGTCATCAAAATTTACCTTAACGTATGAGACAAAAAAATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAAAGTTCTCAAGTTGTCTGCCCTTTGTAAG
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_3/2
	CAGTAAGGCAAACAAGGACAACGGGGGTCATCAAAATTTACCTTAACGTATGAGACAAAAAAATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAAAGTTCTCAAGTTGTCTGCCCTTT
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_4/2
	GGCAAACAAGGACAACGGGGGTCATCAAAATTTACCTTAACGTATGAGACAAAAAAATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAAAGTTCTCAAGTTGTCTGCCCTTTGTAAGA
	+
	KKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKKK
	@region_2_read_5/2
	AACAAGGACAACGGGGGTCATCAAAATTTACCTTAACGTATGAGACAAAAAAATTCACGTTACGATGAGGATAACGATATCAAGACAGTAAAATATTAATACTGTTGCACGCATGAAAGTTCTCAAGTTGTCTGCCCTTTGTAAGAACGT
	+

The reads in the `_R1` and `_R2` files are designed to map in the `FR` orientation, with a fixed insert size that is equal to the fragment length specified by the `--fragment-length` parameter. The IGV snapshot below was obtained from running ChIPulate with a read length of 50 bp in paired end mode, where the fragment length is left at the default value of 200 bp ---

	python3 chipulate.py --input-file examples/fastq-basicExample.tsv --genome-file examples/yeast-genome.fa --chrom-size-file examples/yeast-genome.fa.fai --output-dir examples/ --library-type paired-end --read-length 50

![IGV snapshot of region_7 with paired end reads of 50 bp length](examples/igv_region_7_snapshot_paired_50bp.png)

### Specifying summit coordinates in input file

In the basic example above, the summit of each peak region passed into ChIPulate is assumed to be the midpoint between the `start` and `end` coordinate of each region. ChIPulate considers the summit to be the location at which a binding event has occurred, and thus assumes that the mid-points of fragments in the ChIP sample are centered at the summit. 

If a `summit` column is specified in the input file, where the summit position is defined with respect to the `start` position of each genomic region, then ChIPulate will use this column as the location of the TF-DNA binding event in each region. The following is a way of specifying the summits for each region ---

	chr	start	end	summit	p_ext	p_amp	energy_A
	chr1	1523	1773	74	0.539179	0.18	0.15
	chr1	5612	5862	103	0.505944	0.58	0.15
	chr1	11241	11491	100	0.498672	0.58	0.41
	chr1	11620	11870	43	0.479857	0.79	0.15
	chr1	20066	20316	18	0.494356	0.58	1.15
	chr1	22716	22966	127	0.554812	0.58	0.15
	chr1	35534	35784	87	0.545281	0.38	0.41
	chr1	48914	49164	17	0.492197	0.58	9.41
	chr1	92766	93016	175	0.503651	0.58	0.41
	chr1	156041	156241	120	0.578344	0.28	0.15

The summit in the first region is thus `74` bp inside the region `chr1:1524-1773` and is thus located at `chr1:1598`.

A ChIP-seq peak with multiple binding events and thus, multiple summits, can be simulated by providing multiple entries in the input file that share the same `(chr,start,end)` coordinates but possess different summits --- 

	chr	start	end	summit	p_ext	p_amp	energy_A
	chr1	1523	1773	74	0.539179	0.18	0.15
	chr1	5612	5862	103	0.505944	0.58	0.15
	chr1	11241	11491	100	0.498672	0.58	0.41
	chr1	11620	11870	43	0.479857	0.79	0.15
	chr1	20066	20316	18	0.494356	0.58	1.15
	chr1	20066	20316	58	0.494356	0.58	2.90
	chr1	20066	20316	123	0.494356	0.58	1.55
	chr1	22716	22966	127	0.554812	0.58	0.15
	chr1	35534	35784	87	0.545281	0.38	0.41
	chr1	48914	49164	17	0.492197	0.58	9.41
	chr1	92766	93016	175	0.503651	0.58	0.41
	chr1	156041	156241	120	0.578344	0.28	0.15

In the example above, the peak `chr1:20066-20316` contains three summits at positions `18,58` and `123`. Note that the binding energies and other parameters can differ between these entries. 

### Specifying region names

If no `name` column is specified in the input file, ChIPulate will refer to each location as `region_1`,`region_2`,... . A `name` column can be specified as below ---

	chr	start	end	name	summit	p_ext	p_amp	energy_A
	chr1	1523	1773	peak_1	74	0.539179	0.18	0.15
	chr1	5612	5862	peak_100	103	0.505944	0.58	0.15
	chr1	11241	11491	peak_50	100	0.498672	0.58	0.41
	chr1	11620	11870	peak_20	43	0.479857	0.79	0.15
	chr1	20066	20316	peak_21	18	0.494356	0.58	1.15
	chr1	20066	20316	peak_21_summit_2	58	0.494356	0.58	2.90
	chr1	20066	20316	peak_21_diffmotif	123	0.494356	0.58	1.55
	chr1	22716	22966	false_pos_1 127	0.554812	0.58	0.15
	chr1	35534	35784	false_pos_2 87	0.545281	0.38	0.41
	chr1	48914	49164	hotspot_3	17	0.492197	0.58	9.41
	chr1	92766	93016	hotspot_15	175	0.503651	0.58	0.41
	chr1	156041	156241	peak_121	120	0.578344	0.28	0.15

The name supplied to each region need not be unique and this will not cause any error in the ChIPulate output. However, this may make it more difficult to mark duplicate reads based on the name of the read. For this reason, it is recommended that the `name` column contain unique entries, or is left blank unless needed. 

## Notes on parameters and performance of ChIPulate

The running time of ChIPulate is largely determined by the number of amplified fragments that are generated during the simulation. The parameters that increase the number of amplified fragments generated (while other parameters are held constant) are ---

* A decrease in the binding energies across the genome (```energy_A``` and/or ```energy_B``` in input file). This increases the occupancy probability of locations, which gives rise to more bound fragments.
* A decreaase in the background binding energy in the input sample (```--input-bg``` command-line switch).
* An increase in the chemical potential (```--mu_A``` and/or ```--mu-B``` command-line switches). This is increases the occupancy probability of all genomic locations.
* An increase in the number of genomic locations.
* An increase in the number of cells (```--num-cells``` command-line switch).
* an increase in the control cell fraction (```--control-cell-fraction``` command-line switch).
* An increase in the extraction efficiency (```p_ext``` column in input file).
* An increase in the PCR efficiency (```p_amp``` column in input file) and/or the number of PCR cycles (--pcr-cycles command-line switch). The input file to ChIPulate requires PCR efficiencies to be input to the program. See the PCR efficiency -> Amplification ratio conversion table for how efficiencies map to amplification ratios for guiding choices of this parameter. *Note :* It is recommended to use ```n = 15``` cycles of PCR whenever possible for purposes of speed. See the section "PCR  simulation process" for more information.
* An increase in chromatin accessibility (```chrom_accessibility``` column in input file).

### PCR efficiency to amplification ratio table

The key quantity to control while setting the PCR efficiency column in the input file is the amplification ratio. This is defined as A = (1 + p)^{n}, where A is the amplification ratio, p is the PCR efficiency and n is the number of PCR cycles. The table below gives values of p and n and the amplification ratios corresponding to them.

|A	| p = 0.1|p=0.25| p=0.5|p=0.75|p=0.9|
|:---:|:---:|:---:|:---:|:---:|:---:|
|n=10|2.59|9.31|57.67|269.39|613.11|
|n=12|3.14|14.55|129.75|825.01|2213.31|
|n=15|4.18|28.42|437.89|4421.51|15181.13|
|n=18|5.56|55.51|1477.89|23696.54|104127.35|

### PCR simulation process

The probability mass functions of the number of amplified fragments obtained after 15 cycles of PCR are present in the ```output/pcr-data/``` folder. This considerably speeds up the simulation process since this distribution does not have to be computed afresh. These are files named ```15-0.01.npy```, ```15-0.02.npy``` to ```15-0.99.npy```. The filename convention is ```<number of cycles>-<PCR efficiency>.npy```. Note that if PCR efficiencies other than these values are specified in the ```p_amp``` column in the input file, they will be rounded down to 2 decimal places. 

This is because the computation of the probability mass function of the number of amplified fragments obtained from a single fragment becomes a slow process beyond $n = 10$ cycles. If you wish to simulate more cycles of PCR, or any number other than 15 cycles, this can still be specified in the ```--pcr-cycles``` command-line switch. ChIPulate will first compute these distributions for all efficiencies between 0.01 and 0.99 in steps of 0.01 and then store them in the ```output/pcr-data/``` for future use. Later runs of ChIPulate with this number of PCR cycles will then be fast since these stored distributions will be loaded from disk. 

## Reproducing results from our manuscript
The ```paper/``` folder contains a jupyter notebook (```Manuscript.ipynb```) that can be executed to reproduce the results in our manuscript. An additional download is required to run the last cell of the notebook. Instructions for this are provided within the notebook itself. 

Email vishakadatta@gmail.com in case of any issues. 

## License
This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Authors
Vishaka Datta S

## Acknowledgements

* Sridhar Hannenhalli, Rahul Siddharthan for helping set up each aspect of the ChIPulate pipeline.
* Sandeep Krishna, Gautam Menon for useful discussions on the binding model employed in ChIPulate.
* Parul Singh for discussions on the ChIP-seq protocol and peak callers. 

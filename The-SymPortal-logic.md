# This page is under development
# A brief introduction to SymPortal
#### Overview
SymPortal is a analytical framework for phylogenetically resolving putative taxa within the Symbiodiniaceae using NGS data of the ITS2 amplicon. SymPortal makes use of the considerable intragenomic diversity found within every Symbiodiniaceae genome to achieve a resolution that matches that of hypervariable markers such as the chloroplastic psbAncr whilst maintaining a superior taxonomic breadth over which comparisons can be made. 

#### Resolving intra- and inter-genomic diversity: the central assumption
Because Symbiodiniaceae harbouring corals may associate with more than one taxon of Symbiodiniaceae both intragenomic and intergenomic sources of ITS2 sequence diversity may be present in coral-derived Symbiodiniaceae samples. As such, both intra- and inter-genomic sources of ITS2 seuence diversity may be present in samples. To identify set of sequences most likely derived from a single Symbiodiniaceae taxa, SymPortal works under a single biologically-derived assumption. Although corals may associate with multiple Symbiodiniaceae taxa simultaneously, within the same Symbiodiniaceae genus, corals more often than not associate with only one predominant Symbiodiniaceae taxa. As such, a set of Symbiodiniaceae ITS2 sequences found within a number of samples will become more likely to be representative of a single set taxon the more samples that set of sequences is found in. Put conversely, a set of Symbiodiniaceae ITS2 sequences found within a number of samples will become less likely to be representative of multiple taxa coincidentally harboured as similar abundances the more samples this set of sequences is found in. SymPortal therefore searches sets of Symbiodiniaceae ITS2 PCR amplicons amplified from Symbiodiniaceae-harbouring coral samples for sets of ITS2 sequences that are found across multiple samples. 

#### The ITS2 type profile: the taxonomic unit of SymPortal
Set of sequences are used to define the taxonomic unit of SymPortal, the ITS2 type profile. This ITS2 type profile is a genotype representative of a putative taxa. The sequences that are used to define these ITS2 type profiles are referred to as defining intragenomic [sequence] variants, or DIVs.

#### A community driven framework
Because SymPortal relies on finding set of sequences in multiple samples, SymPortal's ability to resolve ITS2 type profiles is dependent on the number of samples available to any given analysis. As such SymPortal's ability to resolve increases with use. It is a community driven framework. As such, each new SymPortal analysis makes use of the sequencing information associated with not only the new samples, but all previously analysed samples as well. 

#### The database
To enable this functionality the SymPortal analysis integrates with a SQL database referred to as the SymPortal database. This database is used to store the sequencing information from every previously submitted data set, and the results of analyses that have been run on these datasets. In order to cover the details of how data is submitted to the SymPortal database and how analyses are run an understanding of the structure of this relational database, and the objects (tables) that it holds is critical.

# The SymPortal relational database
For additional general information on the SymPortal database as well as instructions on how to interact with the database programmatically please refer to the wiki page [Querying the SymPortal database](https://github.com/SymPortal/SymPortal_framework/wiki/Querying-the-SymPortal-database).
## Overview
Figure 1, gives an overview of the database objects and their relations.

<p align="center">
<img src="https://github.com/didillysquat/symportal_wiki_assets/blob/master/db_schematic.svg" width="70%" height="70%">
</p>

**Figure 1.** Schematic representation of the database objects (tables; bold and italicized) and the attributes used to define relations to other objects of the database (plain text associated with objects). Arrows indicate relations between objects. The objects within the database can be separated into two groups, those concerned with submission of data to the SymPortal database (data submission objects), and those associated with performing data analyses (data analysis objects).

All of the database objects have attributes. The full list can be seen in the [models.py](https://github.com/SymPortal/SymPortal_framework/blob/master/dbApp/models.py) file.

All of the database objects are related to at least one other database object. Database objects can be queried according to these relationships. 

## Data submission-based objects
The data submission-based objects are:
* _**data_set**_ - collection of samples usually from the same study. A _**data_set**_ object will generally associate to multiple _**data_set_sample**_ objects, one for every sample of the submitted data set.
* _**data_set_sample**_ - representative of a single sample. A _**data_set_sample**_ must be associated to a _**data_set**_ object and unless it contains 0 Symbiodiniaceae sequences will be associated to multiple _**data_set_sample_sequence**_ objects (one for every distinct sequence of the _**data_set_sample**_). A _**data_set_sample**_ object will also be associated with one _**clade_collection**_ object per clade represented in its collection of _**data_set_sample_sequence**_ objects. However, the summed abundance of the sequence representatives per clade must be greater than 200 for a _**clade_collection**_ object to be created.
* _**clade_collection**_ - collection of sequences of the same clade from a given sample with a summed abundance > 200. A _**clade_collection**_ must be associated to a _**data_set_sample**_ and will be associated to one _**data_set_sample_sequence**_ object for every non-distinct sequence the collection represents. A _**clade_collection**_ object may also be associated to a _**clade_collection_type**_ object if a sub-set of the sequences associated to the _**clade_collection**_ object have been evaluated to be representative of an ITS2 type profile during a SymPortal analysis. A _**clade_collection**_ object will be related to one _**clade_collection_type**_ object per ITS2 type profile identified from the sequences associated to the _**clade_collection**_ and per SymPortal analysis. As such, this object, in conjunction to the clade_collection_type object, link the data submission-based objects to the data analysis-based objects.
* _**data_set_sample_sequence**_ - a distinct ITS2 sequence found in a single sample. E.g. if the C3 sequence is returned from a sample 232 times, one data_set_sample sequence object will exist, rather than 232 separate objects. One data_set_sample_sequence object will exist for every distinct ITS2 sequence for every sample.
* _**reference_sequence**_ - an ITS2 sequence that may be found in multiple samples. The same single _**reference_sequence**_ object will represent both of two different C3 _**data_set_sample_sequence**_ objects each found in a separate sample. For example, if one sample 'A345' and a second sample 'A346' both returned the C3 sequence (abundances of 232 and 16890, respectively for each sample), the will be a _**data_set_sample_sequence**_ object for each of these sequence occurrences. However, each of these _**data_set_sample_sequence**_ objects will associate with the same single _**reference_sequence**_ object.

## Data analysis-based objects
The data analysis-based objects are:
* _**data_analysis**_ - An analysis that was run on a collection of **_data_set_** objects
* _**analysis_type**_ - An ITS2 type profile found in one or more _**clade_collection**_
* _**clade_collection_type**_ - An abstract object used to link the _**analysis_type**_ and _**clade_collection**_ objects. This object therefore represents the link between data submission-based and data analysis-based objects

# Data submission
Before any analyses are performed on samples, these samples must be converted from raw, demultiplexed illumina reads to SymPortal database objects. Once this conversion is complete, this set of objects may be used in multiple SymPortal analyses but will remain unmodified. As such the inputted data/samples can be viewed separately from the analyses that are conducted on them. This section concerns this conversion from raw data to SymPortal database objects.

### Data input format
SymPortal takes sets of demultiplexed, paired fastq.gz files as input with a forward and reverse read file for every sample. This set of submitted files will be represented in the SymPortal database as an instance of a _**data_set**_ object. 

## Sequence quality control.
Quality control (QC) of these paired files is performed on a sample by sample basis rather than working on all sequences of all samples together. Each sample contained within the data_set object is represented in the SymPortal database as a data_set_sample object. Quality control of the sequences contained in each of the samples is performed using [mothur](https://www.mothur.org/), the [blast+ suite of executables](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs&DOC_TYPE=Download) and [minimum entropy decomposition](http://merenlab.org/software/med/) (MED). 

### mothur QC
The following steps are taken during the mothur-based processing:
* contiguous sequences are generated (make.contigs); 
* sequences are screened with maximum ambiguities allowed set to 0 and maximum homopolymer set to 5;
* sequences are ‘uniqued’ (distinct sequences identified); 
* singletons and doublets are removed;
* amplicons are _in silico_ PCRed using the SymVar primer pair (Hume, et al. 2018) allowing 2 forward differences and 2 reverse differences to the primers; 
* sequence direction is assessed and sequences are reverse complimented as necessary.

### blastn
#### Primary screening
The blastn programme from the blast+ suite of executables is used to identify sequence as Symbiodiniaceae or non-Symbiodiniaceae. On a sample by sample basis every sequence of the sample is run against the [symClade.fa](https://github.com/SymPortal/SymPortal_framework/blob/master/symbiodiniumDB/symClade.fa)-based blast database. 

If a sequence has an identity greater than 80% and a coverage greater than 95% to any sequence in the reference database, it is assumed to be Symbiodiniaceae in origin. 

#### Secondary screening (remote SymPortal only)
Sequences that fall below either of these thresholds are put into a bin for further taxonomic identification (this only happens on the remote instance of the SymPortal framework as access to the entire NCBI ‘nt’ database is required; as are the computational resources required to load the ‘nt’ database into memory). 

In this process, the binned sequences are run against the [NCBI’s ‘nt’ database](ftp://ftp.ncbi.nlm.nih.gov/blast/db/). The 10 closest matches are identified. If 4 or more of the matches are taxonomically annotated as either Symbiodinium or Symbiodiniaceae, the percentage coverage is above 95%, and the percentage identity match is above 60%, then this sequence is considered Symbiodiniaceae in origin (sequences must also be between 184 and 310bp in length; more below).

Importantly, all sequences identified as Symbiodiniaceae in origin at this stage are added to [the symClade.fa fasta](https://github.com/SymPortal/SymPortal_framework/blob/master/symbiodiniumDB/symClade.fa) and the related database objects are remade. The screening of the sequences is then repeated iteratively until no new Symbiodiniaceae sequences are found. Sequences deemed to be Symbiodiniaceae in origin are carried forwards in the analysis. Those sequences deemed to be non-Symbiodiniaceae in origin are written to disk (in the directory of the paired fastq.gz files) for access by the user.

### Size screening
After taxonomic screening Symbiodiniaceae sequences are size screened. This is done with hard cut-offs that were determined empirically as 50 bp +- the average size of the smallest and largest clades (clade A 234 bp, cladeB 266). These cutoffs are max=310, min=184. At this stage, the Symbiodiniaceae sequences are written to disk separated by taxonomic clade (A-I) in preparation for MED analysis.

### MED decomposition
A [MED decomposition is run](https://www.nature.com/articles/ismej2014195) using a dynamic M value set as the largest of 4 or 0.004 * the number of sequences being decomposed. MED nodes are read in from the analysis output and are eventually used to create the _**data_set_sample_sequence**_ and _**reference_sequence**_ objects of the database. However, before we take a closer look at these two objects we must first take a closer look at the _**clade_collection**_ object.

### An introduction to the _**clade_collection**_, _**data_set_sample_sequence**_ and _**reference_sequence**_ objects
Broadly speaking, in the field of Symbiodiniaceae taxonomy (specifically Symbiodiniaceae phylogenetics) all Symbiodiniaceae taxa defined using the ITS2 marker are resolved at, or below, the clade level. Therefore, no Symbiodiniaceae taxa defined with the ITS2 markers may contain intragenomic variants from multiple clades (although multiple Symbiodiniaceae taxa from different clades may reside in a single host). As such, SymPortal subdivides the ITS2 amplicon sequences found in every sample into clade groupings. SymPortal will only attempt to discover ITS2 type profiles (the taxonomic unit of resolution outputted from SymPortal analyses) from any such sample's clade grouping if it contains more than 200 sequences. Any cut-off lower than this may lead to an inability to detect defining intragenomic variants due to insufficient sequencing depth rather than true biological absence . Each collection of these clade grouped sequences from a given sample is represented in the SymPortal database as a _**clade_collection**_. Given that an analysis will not attempt to search a clade group where the total abundance of the constituent sequences is not above 200, per sample, _**clade_collection**_ objects will only be made for clade-separated groups of sequences above this threshold. E.g. if a sample contains 1980 clade C sequences, 42192 clade D sequences and 124 clade A sequences, it will have a _**clade_collection**_ object for each of the clade C and clade D sequences but there will not be a _**clade_collection**_ object representing the clade A sequences. The sample, will contain two clade collections, despite harbouring representative sequences from three of the Symbiodiniaceae clades.

An instance of a _**clade_collection**_ will contain a number of ITS2 amplicon sequences. To reduce information redundancy, multiple occurrences of the same sequence associated with a _**clade_collection**_ are stored in a single object. For example, 100 C3 sequences found in a _**clade_collection**_ will be represented as a single sequence instance found 100 times rather than 100 sequence instances. As well as being found multiple times within the same sample, sequences will be found in common between many different samples. For example, the previously mentioned C3 sequence is found globally. To minimise information redundancy, information specific to an instance of a sequence, e.g. which _**clade_collection**_ it was found in and at what abundance it was found at, is stored separately from the sequence information e.g. the nucleotide sequence, the clade, and the sequence name. The _**clade_collection**_-specific object is the _**data_set_sample_sequence**_ whilst the general sequence information is the _**reference_sequence**_ object. This concept is illustrated in figure 2.

<p align="center">
<img src="https://github.com/didillysquat/symportal_wiki_assets/blob/master/sequenceNaming_noCC.svg" width="50%" height="50%">
</p>

**Figure 2.** Schematic representation of the relationship between the _**data_set_sample**_, _**clade_collection**_, _**data_set_sample_sequence**_ and _**reference_sequence**_ objects within the context of minimising redundancy when storing sequence information.

### Naming of ITS2 sequences
Due to the massive diversity of ITS2 sequences that have already been submitted to the SymPortal database, not all sequences are given names. Only those sequences that are used in the definition of ITS2 type profiles (i.e. DIVs) are named. This naming process currently only happens in the remote version SymPortal to prevent disagreement with novel sequences named by local instance. Given that only those sequences that are used to define ITS2 type profiles are named, the naming process doesn't happen during data submission but rather at the end of a data analysis. This is due to the fact that it is impossible to know whether any novel sequences encountered (those sequences not already in the SymPortal database) during data submission will be used to define ITS2 type profiles before a data analysis has been run. However, during data submission, each of the MED nodes output from the MED analysis are checked against the current reference_sequence objects held in the SymPortal database. If the MED node sequences already match one of sequences of an existing reference_sequence, the data_set_sample_sequence generated from this MED node will be associated to this reference_sequence. Importantly, when looking for matches to the output MED nodes, subsetting is taken into account. For example, if a MED node sequence is AGGATGCA and there is a _**reference_sequence**_ object with a sequence sequence of GGATGCA then these will be considered a match, and the _**data_set_sample_sequence**_ generated will be associated to the _**reference_sequence**_ with the GGATGCA sequence. If there is no match for a given MED node sequence, a new _**reference_sequence**_ object is created. This _**reference_sequence**_ will not have a name. Unnamed _**reference_sequence**_ objects are referred to by their unique identifier (UID).

### Data submission conclusion and output
Once MED nodes have been used to generate the _**data_set_sample_sequence**_ objects, that are in turn associated to the _**reference_sequence**_, _**clade_collection**_, and _**data_set_sample**_ objects, that are all associated to a single _**data_set**_ object, data submission is complete.

# Data analysis
## Overview
A SymPortal data analysis is the process in which ITS2 type profiles are predicted for a selection of _**data_set**_ objects and their associated samples and sequences. A SymPortal data analysis can be divided into two phases: ITS2 type profile discovery and ITS2 type profile assignment. 

Briefly, in the discovery phase, sets of sequences found in samples are searched for in all other samples to identify sets of sequences as large as possible that are found in common between as many samples as possible. Sets of sequences that are shared between a number of samples greater than a set minimum threshold are used to define ITS2 type profiles. That is, for each set of sequences, each sequence becomes a DIV for the ITS2 type profile being defined. Also the maximum and minimum relative abundances of each DIV (across all of the samples in which the set of sequences in question was found) is assigned to the ITS2 type profile and used for identifying the ITS2 type profile in the ITS2 type profile assignment phase.

In the assignment phase, every _**clade_collection**_ (every sample) is searched to see whether the DIVs of the ITS2 type profiles identified in the discovery phase can be found, and if so, whether the relative abundances at which these DIVs are found are within the defining relative abundances of the ITS2 type profile in question. A more detailed description of the discovery and the assignment phases are given below.

## ITS2 type profile discovery
The purpose of ITS2 type profile discovery is to find a set of sequences from the submitted _**data_set_sample**_-associated _**clade_collection**_ objects where each set of sequences is as large as possible whilst still maintaining the minimum level of support (number of _**clade_collection**_ objects the set of sequences is found in). Support is quantified as the number of _**clade_collection**_ objects in which the set of sequences is found. Currently a set of sequences must be found in at least 4 **_clade_collection_** objects to be considered supported and therefore go on to be used to define an ITS2 type profile. Each _**clade_collection**_ can only count towards the support of one ITS2 type profile. An easy way to think about this is that a putative ITS2 type profile is assigned to a _**clade_collection**_ in type discovery.

### Creation of initial profiles for testing
For each _**clade_collection**_ in the analysis, an initial profile is identified, which will then be tested to see if it is supported, i.e. found in other _**clade_collection**_ objects. Not all sequences in a _**clade_collection**_ are used to create this initial profile rather, only sequences found at a relative abundance above 3% are considered (relative to the total number of sequences in the _**clade_collection**_). This 3% cutoff is referred to as the withinCladeCutoff. The 3% value of the withinCladeCutoff has been empirically determined. Higher values begin to limit resolving power. Lower values quickly increase the number of sequences included in the initial type profiles. This increases computational effort considerably, reduces the probability of finding profiles in common between samples, and increases the probability of non-discovery of DIVs due to sequencing depth artefacts rather than true biological absence.

### Searching for supported type profiles
The above process generates a list of initial type profiles, one for each _**clade_collection**_. For each putative type profile the number of _**clade_collection**_ it was found in and the most abundant sequence of each _**clade_collection**_ it was found in (referred to as the majority sequence or majSeq) are kept track of. Two initial profiles are considered identical if they contain the same set of DIVs, i.e. the abundance of DIVs is not taken into account at this point.

Searching for support for the above list of initial profiles is done clade by clade, i.e. supported types from clade A are determined first, then clade B etc. You might imagine that determining support for the list of profiles above is simple and would follow the following logic: for every putative type profile found, see how many _**clade_collection**_ it was found in, if enough, consider this type profile supported. However, a large number of initial profiles will not gain support from this method alone, let’s call these profiles ‘unsupported’. SymPortal works with these ‘unsupported’ profiles by evaluating permutations of shorter subsets of the profiles’ sequences (essentially collapsing them) until additional support is found. See section [A.1 Determining supported profiles](#A1-determining-supported-profiles) for further detail.

Once a list of supported profiles has been generated, these profiles are represented by _**analysis_type**_ objects in the database.

Each _**analysis_type**_ has a name that is made up of the defining intragenomic variants. How each of these DIVs is named will be covered later, but the format of the _**analysis_type**_ name will be explained here. The DIVs in a _**analysis_type**_ name are listed in order of total abundance across all the _**clade_collection**_ objects the _**analysis_type**_ was found in. For example, a typical _**analysis_type**_ name might look like, C3-C3a-C3cc, where C3 is the most abundant sequence found and C3cc the least. In this example, C3 was the most abundant sequence (majSeq) of the three DIVs in each of the _**clade_collection**_ objects the _**analysis_type**_ was found in. In some cases, the most abundant DIV of a type might vary from _**clade_collection**_ to _**clade_collection**_. For example, let’s examine the _**analysis_type**_ C3/C3c-C3cc. This _**analysis_type**_ was found in 10 _**clade_collection**_ objects. In 7 of the _**clade_collection**_ objects, C3 was the most abundant sequence (majSeq) of the three DIVs. However, in 3 of the _**clade_collection**_ objects, C3c was the most abundant. This so-called co-dominance is denoted by the ‘/’. Co-dominant DIVs are always listed in the order of the number of _**clade_collection**_ objects they are the majSeq in. After the co-dominant sequences have been listed in a name, the other DIVs are listed in order of total abundance as described in the first naming example.

### Artefact resolutions caused by the withinCladeCutoff
As discussed [above](#creation-of-initial-profiles-for-testing), implementing a withinCladeCutoff has many benefits however, it can also introduce resolution artefacts (the presence or absence of ITS2 type profiles caused solely by the use of the withinCladeCutoff). These artefacts may happen either during [ITS2 type profile discovery](#ITS2-type-profile-discovery) or [ITS2 type profile assignment](#ITS2-type-profile-assignment). SymPortal implements logic specifically designed to mitigate these artefact resolutions at two points: directly after the ITS2 type profile discovery phase and during ITS2 type profile assignment. For further detail, please see [A.2 Mitigating withinCladeCutoff artefact resolutions](#A2-mitigating-withincladecutoff-artefact-resolutions).

## ITS2 type profile discovery
In ITS2 type profile assignment each of the discovered analysis_type objects is searched for within the _**clade_collection**_ objects. Each _**clade_collection**_ may contain more than one _**analysis_type**_ so long as the _**analysis_type**_ objects in question do not have DIVs in common.

### Type profile assignment - Logic
Within each _**clade_collection**_, every _**analysis_type**_ of the corresponding clade is searched for. An _**analysis_type**_ is found within a _**clade_collection**_ if the _**clade_collection**_ contains the DIVs that define the _**analysis_type**_ within the relative abundance ranges defined for that _**analysis_type**_ during the ITS2 type profile discovery phase (explained below; except for the relaxed lower limits implemented due to artefact mitigation in unlocked DIVs; see [A.2 Mitigating _withinCladeCutoff_ artefact resolutions](#A2-mitigating-withincladecutoff-artefact-resolutions)). For each DIV within each typeProfile this relative abundance range is simply the max and min relative abundance that the DIVs were found at in the initial supporting _**clade_collection**_ objects.

> e.g. when searching for analysis_type C3-C3a-C3cc in clade_collection 'Example1':
> 1. Determing maximum and minimum relative abundances for each DIV in the analysis_type by examining supporting clade_collection onjects.
> * a) the abundances of the _**analysis_type**_ object's DIVs are summed
>
> || C3 | C3a | C3cc | C3ab | C3d | C3t | C3z | Total seqs | DIV seqs |
> | --- | --- | --- | --- |--- | --- |--- | --- |--- | --- |
> | supporting _**clade_collection**_ 1 | **1002** | **220** | **198** | 0 | 9 |23 | 0 | 1453 | 1221 |
> | supporting _**clade_collection**_ 2 | **989** | **228** | **192** | 9 | 8 |0 | 12 | 1438 | 1209 |
> | supporting _**clade_collection**_ 3 | **999** | **213** | **178** | 21 | 0 |7 | 8 | 1426 | 1190 |
> | supporting _**clade_collection**_ 4 | **1023** | **198** | **199** | 0 | 4 |0 | 2 | 1426 | 1220 |
> | supporting _**clade_collection**_ 5 | **1333** | **190** | **191** | 4 | 0 |6 | 5 | 1729 | 1514 |
> | supporting _**clade_collection**_ 6 | **829** | **170** | **193** | 0 | 18 |23 | 0 | 1210 | 992 |
>
> * b) the relative abundances are calculated as: the DIV abundances / DIV seqs total (NOT the total seqs of the _**clade_collection**_).
> * c) the maximum and minimum relative abundances (emboldened) are associated to the _**analysis_type**_ and used in ITS2 type profile assignment.
> 
> |  | C3 | C3a | C3cc |
> | --- | --- | --- | --- |
> | supporting _**clade_collection**_ 1 | 0.66 | 0.18| 0.16 |
> | supporting _**clade_collection**_ 2 | 0.65| **0.19** | 0.16 |
> | supporting _**clade_collection**_ 3 | 0.67| 0.18| 0.15 |
> | supporting _**clade_collection**_ 4 | 0.67 |0.16 | 0.16 |
> | supporting _**clade_collection**_ 5 | **0.75 ** | **0.13** | 0.13 |
> | supporting _**clade_collection**_ 6 | **0.73** | 0.17 |0.19|
> 
> 2. Check to see if _**clade_collection**_ 'Example1' contains all DIVs within the maximum and minimum relative abundances of the _**analysis_type**_.
> * a) DIVs of the _**analysis_type**_ in the _**clade_collection**_ are summed
> 
> |  | C3 | C3a | C3cc | C3ab | C3d | C3t | C3z | Total seqs | DIV seqs |
> | --- | --- | --- | --- |--- | --- |--- | --- |--- | --- |
> | Example1 | **751** | **210** | **180** | 12 | 121 |0 | 0 | 1274 | **1141** |
> 
> * b) the relative abundances of the _**analysis_type**_ in question's DIV sequences for _**clade_collection**_ 'Example1' are calculated
> 
> |  | C3 | C3a | C3cc |
> | --- | --- | --- | --- |
> | Example1 | 0.66 | 0.18 | 0.16 |
> 
> Result: analysis_type C3-C3a-C3cc _is_ found in **_clade_collection_** 'Example1' as the **_clade_collection_** object's relative abundances are within the maximum and minimum relative abundances of the **_analysis_type_**.

Following the above logic several _**analysis_type**_ objects that share the same DIVs may be found in a single _**clade_collection**_. However, SymPortal does not allow _**analysis_type**_ objects that have DIVs in common to be associated to the same _**clade_collection**_. SymPortal will always associate the _**analysis_type**_ that covers the greatest number of sequences for the given _**clade_collection**_ rather than an _**analysis_type**_ that covers more DIVs of the _**clade_collection**_ object's sequences.

> e.g. when checking whether the _**analysis_type**_ objects, C3-C3a-C3cc, C3-C3a-C3dd or C3-C3a should be associated to _**clade_collection**_ 'Example1':
> 
> Check to see which _**analysis_type**_ objects' DIVs cover the largest proportion of sequences in _**clade_collection**_ 'Example1'.
> 
> Abundance of DIVs in _**clade_collection**_ 'Example1':
>
> |  | C3 | C3a | C3cc | C3ab | C3d | C3t | C3z | Total|
> | --- | --- | --- | --- |--- | --- |--- | --- |--- |
> | C3-C3a-C3cc | **751** | **210** | **180** | 12 | 121 |0 | 0 | 1141 |
> | C3-C3a-C3d | **751** | **210** | 180 | 12 | **121** |0 | 0 | 1082 |
> | C3-C3a | **751** | **210** | 180 | 12 | 121 |0 | 0 | 961 |
> 
> _**analysis_type**_ C3-C3a-C3cc represents the largest proportion of _**clade_collection**_ 'Example1's sequences and is associated to the _**clade_collection**_. The other two _**analysis_type**_ objects are not.


<p align="center">
<img src="https://github.com/didillysquat/symportal_wiki_assets/blob/master/analysis_type_assignment2.svg" width="80%" height="80%">
</p>

# Appendix
## A.1 Determining supported profiles
back to [searching for supported type profiles](#searching-for-supported-type-profiles)
Initial profiles are processed in order of length (the number of sequences they contain). Each unique profile is firstly checked to see how many _**clade_collection**_ objects it was found in. If it was found in >=4 _**clade_collection**_ objects, it is deemed supported. It is added to a list of supported profiles, each of which will become an instance of an _**analysis_type**_ (ITS2 type profile). If for all of the clade_collections in the analysis, all profiles of this length (n) are supported then the process continues with the next smallest profile (n-1).

> e.g. if initial profile C3-C3ab-C1c-C1cc-C3dv is found in 6 clade_collection objects, this profile will become an analysis_type.

However, if there are profiles of length n, that have not been identified in a sufficient number of _**clade_collection**_ objects, profile collapse begins. Firstly, profiles of length n-1, where n is the current length of profile we are assessing, are tested to see if they contain the same sequences as the profiles of length n. If they do, and the majority sequences (most abundant sequence of the _**clade_collection**_ object) of the length n profiles are found in the n-1 profiles, the n profile is collapsed to the n-1 profile. _**clade_collection**_ objects associated with the the length n profiles are transferred to the n-1 profile. Essentially the length n profiles ceases to exist. 

> e.g. if initial profiles C3-C3ab-C1c-C1cc-C3fg (found in 2 _**clade_collection**_ objects) and C3-C3ab-C1c-C1cc (found in 1 _**clade_collection**_ object) exist, the first profile, will be collapsed into the second.   C3‑C3ab-C1c-C1cc-C3fg will cease to exist and C3-C3ab-C1c-C1cc will now be associated to 3 clade_collection objects.

If after this process there are still length n profiles remaining that have not been associated to n-1 profiles, SymPortal will attempt to collapse these n length profiles into novel n-1 length profiles that can be derived from the subsets of the length n profiles themselves (the subset collapsed to must contain the majority sequence of the initial length n profile; the novel subsets must be found in at least two of the uncollapse length n profiles).

> e.g. if the following length n initial profiles exist (number of _**clade_collection**_ objects found in shown in parentheses) that have not been associated with any other n-1 profiles yet:
> * C3-C3ab-C1c-C1cd-C3fg (1),  
> * C3-C3ab-C1c-C1cg-C3fg (2),
> * C3-C3ab-C1c-C1p-C3fg (1),
> * C3-C3ab-C1c-C1f-C3fg (1)
> All four initial profiles will be collapsed to the novel n-1 length profile C3-C3ab-C1c-C3fg. Incidentally, this profile will be supported (by 5 _**clade_collection**_ objects) and will eventually become an _**analysis_type**_.

If all profiles of length n have been collapsed to n-1 length profiles the entire profile support process restarts with the next smallest profiles. If there are profiles remaining that were unable to be collapsed, these profiles remain and are reconsidered in the next iteration where they will be attempted to be collapsed to profiles that are n-2 in length. This entire process continues until n=2. Profiles of length 2 and profiles that have not been able to be collapsed of length > 2 will be collapsed to their majority sequence.

> e.g. if profiles:
> * C8-C9-C8b-C8dc-C8sc (2)
> * C8-C6 (1)
> have not been collapsed, the profile C8 will eventually become an analysis_type.

back to [searching for supported type profiles](#searching-for-supported-type-profiles)

## A.2 Mitigating _withinCladeCutoff_ artefact resolutions
back to [Artefact resolutions caused by the _withinCladeCutoff_](#artefact-resolutions-caused-by-the-withincladecutoff)

Artefacts resolutions due to the _withinCladeCutoff_ may occur either in ITS2 type profile assignment or during ITS2 type profile discovery. To introduce the theory behind the generation of artefact ITS2 type profiles we will first discuss the ITS2 type profile assignment cases. 

### Artefacts during the ITS2 type profile assignments phase
Consider an ITS2 type profile of C3a-C3ab-C3ac-C3d. Let’s consider that this type profile has been found in 6 corals, and that we know the true relative abundances of each of the DIVs in these corals, by some means other than SymPortal. In all of the corals each of the DIVs has a relative abundance above 5% (i.e. 2% clear of the required 3% _withinCladeCutoff_), except for the C3d DIV. Its relative abundances are 0.06, 0.05, 0.04, 0.038, 0.028, 0.025. In this instance, the relative abundances of the DIV spans the 0.03 (3%) _withinCladeCutoff_. 

Following the type profile discovery logic, C3a-C3ab-C3ac-C3d is a supported profile and thus becomes a _**analysis_type**_ object. However, in our above example, 2 out of the 6 coral samples will not have gone towards supporting this _**analysis_type**_ due to their C3d sequence relative abundances being below the 0.03 threshold. In type profile assignment (see section XXX) typeProfiles are found in a _**clade_collection**_ object only if the DIVs of the _**analysis_type**_ are present, and at relative abundances within the range of those found in the initial supporting _**clade_collection**_ objects. For example, the C3a-C3ab-C3ac-C3d was created, and was supported by four _**clade_collection**_ objects where the C3d DIV relative abundances were 0.06, 0.05, 0.04, 0.038. Therefore, in type profile assignment, as well as meeting all other requirements, a _**clade_collection**_ must contain the C3d DIV at a relative abundance of between 0.038 and 0.06. As such the _**clade_collection**_ objects that contained this DIV at 0.028 and 0.025 will not be assigned this _**analysis_type**_. 

The purpose of the _withinCladeCutoff_ is to identify which sequences (e.g. C3, C3ab, C3ac, C3d) become DIVs for ITS2 type profiles, and not to affect which instances of sequences (e.g. a C3d at 0.038 or 0.028) are included. Such exclusion cases thus represent an artefact. To prevent such artefacts, the lower relative abundance limits are lowered to 0.005 (0.5%) for any DIV that has been found at below 0.05 (5%) and is therefore likely symptomatic of there being further DIVs below the 3% cutoff that would otherwise be excluded. Any DIV that has instances below the 5% relative abundance is referred to as an ‘unlocked’ DIV. 

To continue our example, considering specifically the C3d DIV of the ITS2 profile C3a-C3ab-C3ac-C3d, the relative abundance of this DIV in the four supporting _**clade_collection**_ objects was 0.06, 0.05, 0.04, 0.038. Because at least one of these is at or below 0.05, a lowered relative abundance limit of 0.005 is used for the C3d DIV; it is unlocked. 

The maximum and minimum allowable abundance at which each _**analysis_type**_ object's DIVs must be found at in order for that _**analysis_type**_ to be found in a given _**clade_collection**_ is defined in the ITS2 type profile discovery phase. These maximum and minimum abundances are defined according to relative abundances of the DIV's in the _**clade_collection**_ objects the DIVs were found in during the ITS2 type profile discovery phase  (XXX see section 3.1.3 Type profile assignment – Logic). Therefore, after initial supported types have been discovered, each _**clade_collection**_ object from the analysis is checked against each _**analysis_type**_ again, this time allowing DIV _withinCladeCutoffs_ of 0.005 for DIVs that have been ‘unlocked’. In this way, some initial types will gain _**clade_collection**_ support, whilst others may lose support. The support of some types will remain unchanged.

To complete our example, the _**clade_collection**_ objects that contained the C3d DIV at 0.028 and 0.025 would not have originally supported the C3a-C3ab-C3ac-C3d _**analysis_type**_. However, after the checking of all _**clade_collection**_ objects against each **_analysis_type_** again, taking into account the unlocked DIVs’ lower _withinCladeCutoffs_, these _**clade_collection**_ objects would move from supporting whichever _**analysis_type**_ they had been associated with to the C3a-C3ab-C3ac-C3d _**analysis_type**_. The unlocked DIVs and associated 0.005 cut-off are also used in type profile assignment. The corals that contained the C3d DIV at 0.028 and 0.025 will therefore have this type assigned to them.

### Artefacts during the ITS2 type profile discovery phase
As well as creating artefacts at the ITS2 type profile assignment phase, the _withinCladeCutoff_ can also cause artefacts in ITS2 type discovery. In a small number of cases, two _**analysis_type**_ objects will be created to represent sequence sets that would best be represented by a single _**analysis_type**_ due to DIVs occurring across the _withinCladeCutoff_ boundary. 

For example, types D1-D17-D6-D17b and D1-D17-D6-D2 may exist. In the _**clade_collection**_ object initially supporting the D17b profile the D2 DIV may exist at > 0.03 in some _**clade_collection**_ objects and at abundances <0.03 and > 0.005 in all other _**clade_collection**_ objects. Equally, in the D2 profile the D17b DIV may exist at > 0.03 in some _**clade_collection**_ objects and at abundances <0.03 and > 0.005 in all other _**clade_collection**_ objects. See example below.


<p align="center">
<img src="https://github.com/didillysquat/symportal_wiki_assets/blob/master/typeDiscoArtefact.svg" width="80%" height="80%">
</p>

Without correction for the effects of the _withinCladeCutoff_, these _**clade_collection**_ objects will be represented as the two ITS2 type profiles shown above (in the scenario where fewer than 4 of the supporting _**clade_collection**_ objects contain both the D17b and D2 DIV > 0.03). Again, the _withinCladeCutoff_ is affecting which instances of sequences rather than just which sequences, are being incorporated into an ITS2 type profile and thus creating an artefact. In this scenario, the super-profile of D1-D17-D6-D2-D17b would be a better match. To mitigate this artefact, closely related types are compared in a pairwise manner to see whether their combined _**clade_collection**_ objects would be better represented by their super-type (combination of DIVs). In this comparison any unlocked DIVs are only required to be found at a relative abundance of 0.005 (0.5%) in the supporting _**clade_collection**_ objects. If the super-type is found to be a better match, for a subset of the _**clade_collection**_ objects (>= 4 cladeCollections for support) a new _**analysis_type**_ is created and _**clade_collection**_ support is redistributed. If the original _**analysis_type**_ objects being compared no longer have the required support, due to their supporting _**clade_collection**_ objects now supporting the new super-type, they are deleted, else, they remain. Any _**clade_collection**_ object(s) that are not associated with the new super-type, but were associated to one of the original _**analysis_type**_ objects that no longer has support, are re-associated to an existing or new analysis_type.
Mitigation of _withinCladeCutoff_ artefacts is conducted directly after the ITS2 type discovery phase. The lowering of the lower abundance limits for unlocked DIVs is implemented in ITS2 type profile assignment phase.

back to [Artefact resolutions caused by the _withinCladeCutoff_](#artefact-resolutions-caused-by-the-withincladecutoff)







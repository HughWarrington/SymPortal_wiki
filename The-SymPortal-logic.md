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

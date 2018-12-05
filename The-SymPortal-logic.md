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
<img src="https://github.com/didillysquat/symportal_wiki_assets/blob/master/db_schematic.png" width="70%" height="70%">
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

### _**data_set**_
_**data_set**_ objects represent a set of samples that are usually part of a single study.


### _**data_set_sample**_

### _**clade_collection**_

### _**data_set_sample_sequence**_

### _**reference_sequence**_


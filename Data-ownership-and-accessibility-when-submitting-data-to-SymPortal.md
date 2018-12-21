SymPortal is available for researchers to [run locally](https://github.com/didillysquat/SymPortal_framework/wiki/SymPortal-setup) on their own systems. However, to benefit from the additional resolving power of running an ananlysis alongside thousands of other previously-sumbitted samples, researchers are encouraged to submit their data to the remote version of SymPortal hosted at SymPortal.org.

Data submitted by users will most often be unpublished data that is not publicly available. This page aims to address concerns from users about how their data will be used during the SymPortal analysis, and what access will be permitted to their data after it has been submitted to the SymPortal.org database.

It is the aim of the SymPortal team that this page be an evolving reference. As such, if you have any questions or concerns that are not fully addressed here, please get in contact with one of the SymPortal team so that we can add additional information accordingly.

### Submitting data to the SymPortal database and accessibility of data addressed

By submitting data to the remote SymPortal instance hosted on SymPortal.org users agree to have their data submitted to the remote SymPortal database, and have it used as part of SymPortal analyses that are run. This means that their data will be used by the SymPortal analysis to inform on the resolutions of all other samples that are part of the same analysis which their data is a part of. Very little information will be available to end users about other users’ unpublished datasets. Any information that is available is completely anonymized and is limited to information on how many times each ITS2 type profile identified in the user’s data was identified in the SymPortal analysis across all samples. This is the only information (that is not associated with the user’s own samples), generated from unpublished data, that will be shared with end users.

Should a researcher want to remove their data from the SymPortal framework (whilst it is still unpublished), this is not a problem. The data will be removed from the SymPortal database and will therefore not be used as part of any further analyses. Yes, the user in question’s data will already have informed the resolution of previous analyses but this was part of the initial agreement in submitting their data.

### Use of dataset sequences in the publicly available reference fasta files used to construct blast databases

By submitting their data to the remote instance of SymPortal.org users also agree that sequences from their samples may be added to the publically available reference fasta files. For most studies, no sequences will be added to the reference fasta files. In the cases where sequences are added it is usually a very small percentage of the sequences from a dataset and the sequences are completely anonymised (no information regarding which samples or datasets the sequences came from is retained).

### Public accessibility of the remotely hosted SymPortal database

No public access will be given to the SymPortal remote database. As such, there will be/is no open database license agreement. The SymPortal code and all other content in the GitHub repository is provided under the [GNU General Public License v3.0](https://github.com/didillysquat/SymPortal_framework/blob/master/LICENSE.txt).

### Future plans for the accessibility to published, publicly available data

In the future, it is the intention of the SymPortal team to publicly display datasets that have been submitted and analysed through SymPortal on the SymPortal.org website ONCE the datasets have been published AND once the datasets have been made publicly available through submission to a public repository (e.g. NCBI or Dryad). For each of these publicly displayed datasets the database objects behind them will also be downloadable and a script for automatically loading them into local instances of SymPortal will be provided. Equally, a set of database objects that represent all of these publicly displayed datasets will be made available so that users may easily update their local instances of SymPortal. In this way, and coupled with the powerful Django API that allows Python3-based [querying of the SymPortal database](https://github.com/didillysquat/SymPortal_framework/wiki/Querying-the-SymPortal-database), the SymPortal framework will become a resource for the whole community.

### Timings of SymPortal database updates and analyses

Presently, submissions to the remote SymPortal instance are relatively infrequent. As such, new analyses are conducted for every new submission of data. However, as submissions of data – hopefully – become more frequent, running a new analysis for every newly submitted dataset will become impractical. Rather, datasets will be submitted to the SymPortal remote database as they are received on a continuous basis, but analyses of the datasets housed in the remote database will be conducted at set intervals (likely once a week to begin with). Researchers submitting to the remote instance of SymPortal will therefore have to wait until the next analysis is run before having access to their outputs.

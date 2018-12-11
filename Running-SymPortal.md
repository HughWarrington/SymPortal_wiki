This section will guide you through the 2 main steps of running a SymPortal analysis:
* **[Submitting data](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#submitting-data)**
* **[Running an analysis](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#running-an-analysis)**

It will also cover some lower level functionality including:
* **[Data output - independent of submission or analysis](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#data-output---independent-of-submission-or-analysis)**
* **[Generating within clade, pairwise UniFrac distances and PCoA](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#generating-within-clade-pairwise-unifrac-distances-and-pcoa)**

This guide assumes you already have [SymPortal set up](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup) and uses the same example dataset that is used in the [SymPortal manuscript] (coming soon). This dataset can be downloaded from [here](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing).

To check that you have SymPortal setup correctly:
```console
$ ./main.py
```

This command should produce an output similar to:

```console
/path/to/repo/SymPortal_framework/db.sqlite3
usage: main.py [-h] [--submit path_to_dir] [--display_data_sets]
               [--analyse data_set IDs]
               [--print_output_types data_set IDs, analysis ID] [--display_analyses]
               [--num_proc NUM_PROC] [--name NAME] [--description DESCRIPTION]
               [--db_version DB_VERSION] [--vacuum_database]
main.py: error: one of the arguments --submit --display_data_sets --analyse --print_output_types --display_analyses --vacuum_database is required
```

***

#### i. Download data
Download the dataset from [here](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing). Note the directory in which the dataset is saved. This will be used as an input in the following steps.


***

### Submitting data
The first step of analysing any dataset is to submit it to the SymPortal framework's database. In this step, SymPortal will perform all quality control filtering of the sequence data and convert the raw sequence data into database objects such as **data_set**, **data_set_sample**, **clade_collection**, **data_set_sample_sequence** and **reference_sequence**. For more information on the database objects and how to query the database, please see the [Querying the SymPortal database page](https://github.com/didillysquat/SymPortal_framework/wiki/Querying-the-SymPortal-database). As part of a data_set submission, SymPortal will output a count table of the ITS2 sequences returned from each of the **data_set_sample**s in the **data_set**. A plot visualizing this count table will also be produced.  In addition, SymPortal will generate clade separated, between sample pairwise distance matrices (BrayCurtis-based by default). By default SymPortal will also run a principal coordinate analysis (PCoA) on these distance matrices and return the coordinates of the principal components for each **data_set_sample**.

To submit a dataset to the database:
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission
```
By default the submission will be completed using one process however multiple processors may be utilised using the --num_proc argument.
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission --num_proc 3
```
The ```--data_sheet``` flag may also be applied for submissions (recommended). By applying this flag and passing the full path to a data_sheet (see the smith_et_al_meta_data_input file in the [example dataset google drive folder](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing) for a template) meta information may be associated to the **data_set_sample** objects held in the SymPortal database.
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission --num_proc 3 --data_sheet /path/to/example_data_location/smith_et_al_meta_input.xlxs
```
Passing a data_sheet at submission also allows for custom sample names to be associated to each of the fastq.gz pairs. If no data_sheet is submitted, sample names will be generated automatically from the name of the respective fastq.gz pairs.

To switch off the automatic generation of plots (stacked bar charts for the count tables and scatter plots of the clade separated PCoA coordinates), the ```--noFig``` flag can be passed as an argument.
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission --num_proc 3 --data_sheet /path/to/example_data_location/smith_et_al_meta_input.xlxs --noFig
```
To switch off the ordination component of the data_submission the ```--noOrd``` flag may be passed.
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission --num_proc 3 --data_sheet /path/to/example_data_location/smith_et_al_meta_input.xlxs --noOrd
```
Both the ```--noFig``` and ```--noOrd``` flags may be passed simultaneously.

##### A note on running SymPortal with multiple processors
SymPortal has been designed to take advantage of multi-processor environments and is parallelised wherever possible. However, the somewhat simple SQLite database that the local framework comes setup with by default (for sake of simplicity) has limited support for handling simultaneous requests to write. As such, whilst all of the SymPortal functions may be run with multiple processors, when using the default SQLite database, the chances of a 'timeout' failure or conflict will increase with the number of processors used. In general a small degree of parallelisation, e.g. ```--num_proc 3```, should be very unlikely to cause any issue. To robustly run SymPortal in a highly parallelised manner, the SQLite database should be upgraded to a server based PostgreSQL database.

There are multiple online resources that can be used to aid in setting up the [Django framework](https://www.djangoproject.com/) (the framework underlying SymPortal's interaction with the database) to run with a PostgreSQL, rather than a SQLite, database: [here](https://www.digitalocean.com/community/tutorials/how-to-use-postgresql-with-your-django-application-on-ubuntu-14-04), [here](https://tutorial-extensions.djangogirls.org/en/optional_postgresql_installation/?q=) and [here](https://www.youtube.com/watch?v=Axh8rNKgvmk) for example.

#### Checking data_set submissions
The ID, name and time stamp of submitted data_set instances can be output by running the following command:
```console
$ python3.6 main.py --display_data_sets
1: first_submission	2018-07-04 05:07:59.418975
```

#### Debug mode
The ```--debug``` flag can be passed when submitting data. When this flag is passed the output to stdout will be significantly more verbose. This is useful in identifying where errors may be occurring.

***

### Running an analysis
Running a SymPortal analysis will programmatically search for recurring sets of ITS2 sequences in the **data_set_sample**s of the **data_set** objects submitted to the analysis. The output of an analysis is a count table of predicted ITS2 type profiles (representative of putative taxa). By default a graphical representation of this count table, as well as the sequences found in the samples, is output. Clade separated, between ITS2 type profile pairwise similarity distance matrices are also output (BrayCurtis-based by default). As are the coordinates of the PCoA run on each of these matrices.

To run an analysis on one of the data_set instances that have been submitted to the database:
```console
$ ./main.py --analyse 1 --name first_analysis --num_proc 3
```
Where 1 is the ID of the data_set to analyse.

Note that when running an analysis containing only one data_set instance, an int can be passed to the --analyse flag. However, if you wish to run an analysis containing more than one data_set instance, a comma separated string may be passed.

e.g.
```console
$ ./main.py --analyse '1,2,5' --name second_analysis --num_proc 3
```

Similar to running a data_set submission, the ```--noFig``` and ```--noOrd``` flags may be passed (no figure output; no ordination or similarity calculations).

#### Checking data_analysis instances
The ID, name and time stamp of completed data_analysis instances can be output by running the following command:
```console
$ ./main.py --display_analyses
1: first_analysis	2018-07-04 05:57:23.933207
```

***

### Data output - independent of submission or analysis
Count tables for ITS2 sequence and ITS2 type profile abundances are output automatically during data submission and analyses, respectively. However, these count tables may also be output independent of a submission or analysis. Corresponding plots will also be output.

To output only the ITS2 sequence count table (i.e. equivalent to the count table output after a data_set submission):

The output files should be displayed on completion of table generation and output
```console
$ ./main.py --print_output_seqs 1
DIV table output files:
./outputs/non_analysis/1.DIVs.absolute.txt
./outputs/non_analysis/1.DIVs.relative.txt
./outputs/non_analysis/1.DIVs.fasta
```

To output both ITS2 sequence and ITS2 type profile count tables (i.e. equivalent to the count tables output during an analysis):

```console
$ ./main.py --print_output_types 1 --data_analysis_id 1 --num_proc 3
DIV table output files:
/SymPortal_framework/outputs/1/1_1.DIVs.absolute.txt
/SymPortal_framework/outputs/1/1_1.DIVs.relative.txt
/SymPortal_framework/outputs/1/1_1.DIVs.fasta
ITS2 type profile output files:
/SymPortal_framework/outputs/1/1_1.profiles.absolute.txt
/SymPortal_framework/outputs/1/1_1.profiles.relative.txt
```
For either output type, multiple data_set IDs may be passed:

```console
$ ./main.py --print_output_types '1,3,5' --data_analysis_id 1 --num_proc 3
```

This may be useful when only wanting to output a subset of the data_set objects that were input to an analysis.
***

### Generating within clade, pairwise UniFrac distances and PCoA
Pairwise distances and Principal Coordinate Analyses may be generated as either between sample or between ITS2 type profile. Distance matrices may be generated either by a BrayCurtis- or UniFrac-based methodology. The BrayCurtis method is faster and requires no additional dependencies. However, it can perform less optimally than the UniFrac method when comparing very closely related ITS2 sequence profiles. The UniFrac methodology requires more time to compute and additional dependencies. Please see [this](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#6-third-party-dependencies) section in the wiki.

Pairwise UniFrac distance for between either samples (independent of analysis) or ITS2 type profiles (from an analysis) may be generated. A PCoA is automatically run on these distances to facilitate visual ordination of data.
##### Between samples
```console
$ ./main.py --between_sample_distances 5 --bootstrap 100 --num_proc 3
/SymPortal_framework/outputs/ordination/5/between_samples/mothur/C/2018-12-10_07-25-34.420342.PCoA_coords.csv
/SymPortal_framework/outputs/ordination/5/between_samples/mothur/C/2018-12-10_07-25-34.420342.consensus_tree_sumtrees.newick.dist
```
##### Between ITS2 type profiles
```console 
$ ./main.py --between_type_distances 5 --data_analysis_id 2 --bootstrap 100 --num_proc 3
Output files:
/SymPortal_framework/outputs/ordination/5/between_profiles/mothur/C/2018-12-10_07-25-34.420342.PCoA_coords.csv
/SymPortal_framework/outputs/ordination/5/between_profiles/mothur/C/2018-12-10_07-25-34.420342.consensus_tree_sumtrees.newick.dist
```
Additionally, it is sometimes useful to compare a set of samples that are a subset of one or multiple data_set object(s). The ```--between_sample_distances_sample_set``` takes a comma separated list of data_set_sample object IDs as its input and will compute distances and perform PCoAs for only these samples. As always, distances are BrayCurtis-based by default, but the UniFrac method may be used instead by passing ```--distance_method unifrac```.
```console
$ ./main.py --between_sample_distances_sample_set 5 --num_proc 3 --distance_method unifrac
Output files:
/SymPortal_framework/outputs/ordination/5/between_samples/mothur/C/2018-12-10_07-25-34.420342.PCoA_coords.csv
/SymPortal_framework/outputs/ordination/5/between_samples/mothur/C/2018-12-10_07-25-34.420342.consensus_tree_sumtrees.newick.dist
```

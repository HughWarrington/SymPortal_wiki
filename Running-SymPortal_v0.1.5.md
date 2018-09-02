This section will guide you through the 3 main steps of running a SymPortal analysis:
* **[Submitting data](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#submitting-data)**
* **[Running an analysis](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#running-an-analysis)**
* **[Data output](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#data-output)**
    * [from analysis](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#analysis-dependent-output)
    * [independent of analysis](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#analysis-independent-output) (environmental samples)

It will also document some additional functionality of SymPortal:
* **[Generating within clade, pairwise UniFrac distances](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#generating-within-clade-pairwise-UniFrac-distances-and-PCoA)**
    * [between samples](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#between-samples)
    * [between ITS2 type profiles](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#between-ITS2-type-profiles)

This guide assumes you already have [SymPortal set up](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup) and uses the same example dataset that is used in the [SymPortal manuscript](). This dataset can be downloaded from [here](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing).

To check that you have SymPortal setup correctly:
```console
$ ./main.py
```

This command should produce an output similar to:

```console
/path/to/repo/SymPortal_framework/db.sqlite3
usage: main.py [-h] [--submit path_to_dir] [--display_data_sets]
               [--analyse data_set IDs]
               [--print_output data_set IDs, analysis ID] [--display_analyses]
               [--num_proc NUM_PROC] [--name NAME] [--description DESCRIPTION]
               [--db_version DB_VERSION] [--vacuum_database]
main.py: error: one of the arguments --submit --display_data_sets --analyse --print_output --display_analyses --vacuum_database is required
```

***

#### i. Download data
Download the dataset from [here](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing). Note the directory in which the dataset is saved. This will be used as an input in the following steps.


***

#### Submitting data
The first step of analysing any dataset is to submit it to the SymPortal framework's database. In this step, SymPortal will perform all quality control filtering of the sequence data and convert the raw sequence data into database objects such as **data_set**, **data_set_sample**, **clade_collection**, **data_set_sample_sequence** and **reference_sequence**.

To submit a dataset to the database:
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission
```
By default the submission will be completed using one process however multiple processors may be utilised using the --num_proc argument.
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission --num_proc 3
```
The ```--data_sheet``` flag may also be applied for submissions. By applying this flag and passing the full path to a data_sheet (see the smith_et_al_meta_data_input file in the [example dataset google drive folder](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing) for a template) meta information may be associated to the data_set_sample held in the SymPortal database.
```console
$ ./main.py --submit /path/to/example_data_location --name first_submission --num_proc 3 --data_sheet /path/to/example_data_location/smith_et_al_meta_input.xlxs
```
Passing a data_sheet at submission also allows for custom sample names to be associated to each of the fastq.gz pairs. If no data_sheet is submitted, sample names will be generated automatically from the name of the respective fastq.gz pairs.
##### A note on running SymPortal with multiple processors
SymPortal has been designed to take advantage of multi-processor environments and is parallelised wherever possible. However, the somewhat simple SQLite database that the local framework comes setup with by default (for sake of simplicity) has limited support for handling simultaneous requests to write. As such, whilst all of the SymPortal functions may be run with multiple processors, when using the default SQLite database, the chances of a 'timeout' failure or conflict will increase with the number of processors used. In general a small degree of parallelisation, e.g. ```--num_proc 3```, should be very unlikely to cause any issue. To robustly run SymPortal in a highly parallelised manner, the SQLite database should be upgraded to a server based PostgreSQL database.

There are multiple online resources that can be used to aid in setting up the [Django framework](https://www.djangoproject.com/) (the framework underlying SymPortal's interaction with the database) to run with a PostgreSQL, rather than a SQLite, database: [here](https://www.digitalocean.com/community/tutorials/how-to-use-postgresql-with-your-django-application-on-ubuntu-14-04), [here](https://tutorial-extensions.djangogirls.org/en/optional_postgresql_installation/?q=) and [here](https://www.youtube.com/watch?v=Axh8rNKgvmk) for example.
#### Checking data_set submissions
The ID, name and time stamp of submitted data_set instances can be output by running the following command:
```console
$ ./main.py --display_data_sets
1: first_submission	2018-07-04 05:07:59.418975
```

***

#### Running an analysis
To run an analysis on one or more of the data_set instances that have been submitted to the database:
```console
$ ./main.py --analyse 1 --name first_analysis --num_proc 3
```
Not that when running an analysis containing only one data_set instance, an int can be passed to the --analyse flag. However, if you wish to run an analysis containing more than one data_set instance, a commar separted string may be passed.

e.g.
```console
$ ./main.py --analyse '1,2,5' --name second_analysis --num_proc 3
```

#### Checking data_analysis instances
The ID, name and time stamp of completed data_analysis instances can be output by running the following command:
```console
$ ./main.py --display_analyses
1: first_analysis	2018-07-04 05:57:23.933207
```

***

#### Data output
Data output may be performed in an analysis dependent or independent manner.
When data output is performed in association with a data analysis, count tables for both ITS2 sequences and ITS2 type profiles will be produced.
When output independent of a data analysis, only an ITS2 sequence count table will be produced. This data analysis independent output is especially useful when working with samples that are not from a host origin, i.e. free living samples.
##### Analysis dependent output
Count tables for ITS2 sequence abundances and ITS2 type profiles will be output with the following command:
```console
$ ./main.py --print_output 1 --data_analysis_id 1 --num_proc 3
```
The output files should be displayed on completion of table generation and output
```console
DIV table output files:
/SymPortal_framework/outputs/1/1_1.DIVs.absolute.txt
/SymPortal_framework/outputs/1/1_1.DIVs.relative.txt
/SymPortal_framework/outputs/1/1_1.DIVs.fasta
ITS2 type profile output files:
/SymPortal_framework/outputs/1/1_1.profiles.absolute.txt
/SymPortal_framework/outputs/1/1_1.profiles.relative.txt
```
To output a single set of count tables containing data for several data_set instances you may pass a comma separated str containing the IDs of the data_set instances to be output.
```console
$ ./main.py --print_output '1,3,5' --data_analysis_id 1 --num_proc 3
```
##### Analysis independent output
```console
$ ./main.py --print_output_no_types 1
DIV table output files:
./outputs/non_analysis/1.DIVs.absolute.txt
./outputs/non_analysis/1.DIVs.relative.txt
./outputs/non_analysis/1.DIVs.fasta
```
***

#### Generating within clade, pairwise UniFrac distances and PCoA
N.B. in order for these commands to be run the additional third party dependencies must be met. Please see [this](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#additional-optional-third-party-dependencies) section in the wiki.

Pairwise UniFrac distance for between either samples (independent of analysis) or ITS2 type profiles (from an analysis) may be generated. A PCoA is automatically run on these distances to facilitate ordination of data.
##### Between samples
```console
$ ./main.py --between_sample_distances 5 --bootstrap 100 --num_proc 3
/SymPortal_framework/outputs/ordination/5/between_samples/mothur/C/PCoA_coords.csv
/SymPortal_framework/outputs/ordination/5/between_samples/mothur/C/consensus_tree_sumtrees.newick1.weighted.phylip.dist
```
##### Between ITS2 type profiles
```console 
$ ./main.py --between_type_distances 5 --data_analysis_id 2 --bootstrap 100 --num_proc 3
Output files:
/SymPortal_framework/outputs/ordination/5/between_profiles/mothur/C/PCoA_coords.csv
/SymPortal_framework/outputs/ordination/5/between_profiles/mothur/C/consensus_tree_sumtrees.newick1.weighted.phylip.dist
```
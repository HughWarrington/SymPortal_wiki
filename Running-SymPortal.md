This section will guide you through the 3 main steps of running a SymPortal analysis:
* **[Submitting data](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#submitting-data)**
* **Starting an analysis**
* **Data output**
    * from analysis
    * independent of analysis (environmental samples)

It will also document some additional functionality of SymPortal:
* **Generating within clade, pairwise UniFrac distances**
    * between samples
    * between ITS2 type profiles

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
Once an analysis has been completed count tables may be generated and output.

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

***

This section will guide you through the 3 main steps of running a SymPortal analysis:
* **Submitting data**
* **Starting an analysis**
* **Data output**

This guide assumes you already have [SymPortal setup]() and uses the same example dataset that is used in the [SymPortal manuscript]. This dataset can be downloaded from [here](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing).

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

#### 1. Submitting data
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


To setup a local instance of the SymPortal analytical framework follow the directions below. SymPortal is designed to run using a MacOS or linux environment.

#### 1. Clone the SymPortal repository into your chosen working directory.

See GitHub's [online documentation](https://help.github.com/articles/cloning-a-repository/) for further details.


#### 2. Configure settings.py

You will find a file in your chosen directory named: **settings_blank.py**.

Rename this file **settings.py**.

Generate a bespoke secret key [here](https://www.miniwebtool.com/django-secret-key-generator/) and paste it into your settings.py file so that:

`SECRET_KEY = ''` 

becomes (key only for example)

`SECRET_KEY = '^3#q(9(ky$5#=tg$8m45k*5gh!==n&nh)5*8=hf0g)giob^mzd'`

Save the modified **settings.py**.

#### 3. Setting up the Python3.6 environment

The SymPortal framework is scripted in the [Python3](https://www.python.org/download/releases/3.0/) language.

**WARNING:** Please be aware that overwriting or replacing your system's current python installation can cause fatal instability. If you are unsure of what you are doing, please seek the advice of someone who is. 

To run SymPortal you must have a python3.6 installation that satisfies all of SymPortal's [package and module prerequisites](####31-Package-and-module-prerequisites). Irrespective of whether you already have a Python3.6 installation on your system, we recommend the [Miniconda](https://conda.io/docs/user-guide/install/download.html) or [Anaconda](https://conda.io/docs/user-guide/install/download.html) distributions. Please see the [docs](https://conda.io/docs/user-guide/install/download.html) for further information on which distribution suits your needs best. Both may be installed independent of root privileges.

We recommend running SymPortal via setting up a Python3.6 virtual environment ([conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) or [standard](https://docs.python.org/3.6/library/venv.html)) containing all required [package and module prerequisites](####31-Package-and-module-prerequisites). But SymPortal may be run using any Python3.6 installation.

#### 3.1. Package and module prerequisites
The Python3.6 installation used to run SymPortal must have the following packages and modules installed:
* Django==1.11.4
* numpy==1.13.1
* matplotlib==2.0.2
* scipy==0.19.1
* sklearn==0.0
* texttable==0.9.1
* colorama==0.3.9
* pandas==0.23.1
* plumbum==1.6.6
* biopython==1.71
* scikit-bio==0.5.2

#### 4. Creating the framework's database

The SymPortal analysis is integrated with a mySQL database. This database can be either a [SQLite](https://www.sqlite.org) or [PostgreSQL](https://www.postgresql.org/) database.

The cloned SymPortal repository is currently setup to create a [SQLite](https://www.sqlite.org) database. This database is a much lighter database than the [PostgreSQL](https://www.postgresql.org/) and is file-based rather than server-based.

To create the SQLite database (that will be named **db.sqlite3** by default) and automatically populate it with the tables defined in the **dbApp/models.py** file we will use the `makemigrations` and `migrate` commands from the [Django API](https://www.djangoproject.com/) via the **manage.py** file.

From within the SymPortal_framework directory (using the Python3.6 installation meeting the package and module requirements):
```shell
$ python3.6 manage.py makemigrations
```
A file, **dbApp/migrations/0001_initial.py** will be created.

then:
```console
$ python3.6 manage.py migrate
```
You should see that a **db.sqlite3** file has been created in your working directory. This is your SQLite database populated with empty tables.

#### 5. Populating the local database with SymPortal's reference_sequences
A key part of the SymPortal framework is its enforced standardisation of the data it works with. The quality controls built into the SymPortal framework ensures that all submitted sequence data goes from raw input to output following the same set of standards. 

One key part of this standardisation is the naming of sequences. The same nucleotide sequence should always have the same name and these nucleotide sequences should always represent the same region of the rDNA. To ensure that SymPortal maintains good agreement with the important sequence names already existing in the literature, e.g. A1, D1, D4, D6, C1, C3, C15 and C21 to name just a few, the SymPortal database was initialised using a carefully curated collection of > 350 commonly found sequences. However, ITS2 sequence diversity is immensely large and many thousands of sequences have already been submitted to the SymPortal database (maintained remotely). To assign identities to these sequences when run via SymPortal.org the SymPortal framework generates systematic alphanumeric identifiers e.g. C3ac or D1cd. However, given the immensity of ITS2 sequence diversity, not all sequences are given names. Only those sequences defined as DIVs and used to define ITS2 type profiles are named. I.e. only sequences used to characterise putative _Symbiodinium_ taxa are named.

The SymPortal analysis run locally is not integrated with the remote SymPortal database. Whilst the local analysis could also generate alphanumeric identifiers, doing so would run the risk of assigning alphanumeric identifiers that were out of sync with the SymPortal database. I.e. more than one name could be associated to a single sequence or vice versa. To prevent this from happening, locally run instances of the SymPortal framework do not generate alphanumeric identifiers for sequences that are currently unnamed. For DIVs output from local analyses, the UID of the local database will be used in place of an alphanumeric identifier if one is not available.

However, to aid in the identification of ITS2 type profiles and ITS2 sequences output from local analyses, all named sequences from the current SymPortal database have been provided as part of this repository. These can be found in the [refSeqDB.fa](https://github.com/SymPortal/SymPortal_framework/tree/master/symbiodiniumDB) file.

#### 5.1. Populating reference_sequences
To make use of the sequences contained in the [refSeqDB.fa](https://github.com/SymPortal/SymPortal_framework/tree/master/symbiodiniumDB) file, they must be input to the local database. A standalone script has been provided to do this: [populate_db_ref_seqs.py](https://github.com/SymPortal/SymPortal_framework/tree/master/populate_db_ref_seqs.py).

Simply run:
```shell
$ python3.6 populate_db_ref_seqs.py
```

#### 6. Third party dependencies
To perform quality filtering, node identification and tree creation SymPortal relies on the following third party programmes:
* [mothur](https://www.mothur.org/)(version=1.39.5)
* [MED](http://merenlab.org/software/med/): o-pad-with-gaps; decompose (oligotyping pipeline version=2.1)
* [SumTrees](https://www.dendropy.org/programs/sumtrees.html): sumtrees.py
* [BLAST+ executables](): blastn; makeblastdb (version 2.6.0+)
* [MAFFT](https://mafft.cbrc.jp/alignment/software/)

For SymPortal to run correctly, each of the above executables must be in your systems PATH.
To test whether these executables are found in your PATH you can use the _which_ command.
```console
$ which mothur
/usr/bin/mothur
$ which o-pad-with-gaps
/home/user/anaconda2/bin/o-pad-with-gaps
$ which decompose
/home/user/anaconda2/bin/decompose
$ which sumtrees.py
/home/user/miniconda3/bin/sumtrees.py
$ which blastn
/home/user/ncbi-blast-2.6.0+/bin/blastn
$ which makeblastdb
/home/user/ncbi-blast-2.6.0+/bin/makeblastdb
$ which mafft
/home/user/mafft/bin/mafft
```
If the executable is installed and found in your PATH, the path to the executable should be returned 
***

#### To check that you have SymPortal setup correctly:
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
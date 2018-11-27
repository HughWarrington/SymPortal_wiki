To setup a local instance of the SymPortal analytical framework follow the directions below. SymPortal is designed to run using a MacOS or linux environment.

#### 1. Download the [latest release](https://github.com/SymPortal/SymPortal_framework/releases/latest) and decompress in your chosen working directory.
or
#### 1. Clone the SymPortal repository into your chosen working directory.

See GitHub's [online documentation](https://help.github.com/articles/cloning-a-repository/) for further details.


#### 2a. Configure settings.py

You will find a file in your chosen directory named: **settings_blank.py**.

Rename this file **settings.py**.

Generate a bespoke secret key [here](https://www.miniwebtool.com/django-secret-key-generator/) and paste it into your settings.py file so that:

`SECRET_KEY = ''` 

becomes (key only for example)

`SECRET_KEY = '^3#q(9(ky$5#=tg$8m45k*5gh!==n&nh)5*8=hf0g)giob^mzd'`

Save the modified **settings.py**.

#### 2b. Configure sp_config (optional)

Replace the value for the ```user_name``` and ```user_email``` keys in the **sp_config** file.

e.g. 

```"user_name"   :   "undefined"``` --> ```"user_name"   :   "bcchume"```

```"user_email"   :   "undefined"``` --> ```"user_name"   :   "bcchume@symportal.com"```

You will not be contacted on this email, it will only be used to log your details with the data_set and data_analysis objects. It will also be appended to output results.


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
* pandas==0.23.1
* plumbum==1.6.6
* biopython==1.71
* scikit-bio==0.5.2
* xlrd==1.1.0

__N.B. some of the above package versions are not maintained in the conda repositories. As such, please install the packages using pip.__ 

E.g. (making sure you are installing to the correct python version):
```console 
(sp_venv)$ pip install sklearn==0.0
```

#### 4. Creating the framework's database

The SymPortal analysis is integrated with a mySQL database. This database can be either a [SQLite](https://www.sqlite.org) or [PostgreSQL](https://www.postgresql.org/) database.

The cloned SymPortal repository is currently setup to create a [SQLite](https://www.sqlite.org) database. This database is a much lighter database than the [PostgreSQL](https://www.postgresql.org/) and is file-based rather than server-based.

Please see [A note on running SymPortal with multiple processors](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#a-note-on-running-symportal-with-multiple-processors) for some considerations when running parallelised instances of SymPortal with the default SQLite database.

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
SymPortal relies on a number of third party programmes that can be divided into core dependencies (required for the core SymPortal analysis) and additional dependencies (required only for the UniFrac-based ordination functions; no additional dependencies are required for the BrayCurtis-based ordinations that are run as default).

__Core dependencies__ (required)
To perform quality filtering and MED node identification SymPortal relies on the following core third party programmes (please visit the links for information on how to install them):
* [mothur](https://www.mothur.org/)(version=1.39.5)
* [MED](http://merenlab.org/software/med/): o-pad-with-gaps; decompose (oligotyping pipeline version=2.1) __(please see the note on installing the oligotyping pipeline below)__
* [BLAST+ executables](): blastn; makeblastdb (version 2.6.0+)

__Additional dependencies__ (only required for UniFrac-based ordination analyses) 
* [SumTrees](https://www.dendropy.org/programs/sumtrees.html): sumtrees.py
* [MAFFT](https://mafft.cbrc.jp/alignment/software/)
* [PHYLIPNEW](http://emboss.sourceforge.net/) __(please see the note on installing PHYLIPNEW below)__

__Testing dependencies__

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

Please also ensure that the programmes are working correctly:
```console
$ mothur -v
Mothur version=1.39.5
Release Date=3/20/2017
$ decompose -v
Oligotyping Pipeline Version .....................................: 2.1
$ sumtrees.py --version
/==========================================================================\
|                                 SumTrees                                 |
|                     Phylogenetic Tree Summarization                      |
|                              Version 4.4.0                               |
|                   By Jeet Sukumaran and Mark T. Holder                   |
|                          Using: DendroPy 4.4.0                           |
+--------------------------------------------------------------------------+
|                                 Citation                                 |
|                                 ~~~~~~~~                                 |
| If any stage of your work or analyses relies on code or programs from    |
| this library, either directly or indirectly (e.g., through usage of your |
| own or third-party programs, pipelines, or toolkits which use, rely on,  |
| incorporate, or are otherwise primarily derivative of code/programs in   |
| this library), please cite:                                              |
|                                                                          |
|   Sukumaran, J and MT Holder. 2010. DendroPy: a Python library for       |
|     phylogenetic computing. Bioinformatics 26: 1569-1571.                |
|                                                                          |
|   Sukumaran, J and MT Holder. SumTrees: Phylogenetic Tree Summarization. |
|     4.4.0. Available at https://github.com/jeetsukumaran/DendroPy.       |
|                                                                          |
| Note that, in the interests of scientific reproducibility, you should    |
| describe in the text of your publications not only the specific          |
| version of the SumTrees program, but also the DendroPy library used in   |
| your analysis. For your information, you are running DendroPy 4.4.0.     |
\==========================================================================/
$ blastn -version
blastn: 2.2.31+
Package: blast 2.2.31, build Jan  7 2016 23:17:17
$ makeblastdb -version
makeblastdb: 2.2.31+
Package: blast 2.2.31, build Jan  7 2016 23:17:17
$ mafft --version
v7.402 (2018/May/23)
$ fseqboot -version
EMBOSS:6.6.0.0 PHYLIPNEW:3.69.650
```
__Issues installing the oligotyping pipeline (decompose)__
The oligotyping pipeline can be difficult to install. The online instructions will simply point you towards installing it using pip. An install using conda will also not break, unitl you try to run it. In my experience, the best way to install the oiligotyping pipeline is as follows:
* create a virtual python2.7 (not 3.6) environment: ```$ conda create -n med_install python=2.7```
* activate new environment ```$ source activate med_install```
* downgrade the pip version ```(med_install)$ pip install pip==9.0.3```
* install ```(med_install)$ pip install oligotyping```
* deactivate med_install venv ```source deactivate```

__Installing the PHYLIPNEW collection of commands__
In order to be able to compute UniFrac-based distance matrices and PCoA ordinations between sample and ITS2 type profiles, respectively, some further packages are required. These packages are all contained in the PHYLIPNEW package which is part of the [EMBOSS](http://emboss.sourceforge.net/) project. The PHYLIPNEW tar.gz can be downloaded from [here](ftp://emboss.open-bio.org/pub/EMBOSS/). There is a bit of knack to getting the PHYLIPNEW packages installed on a system. I recommend reading the answer to this question: [Can you give an example of how to install an EMBASSY package](http://emboss.sourceforge.net/docs/faq.html).
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
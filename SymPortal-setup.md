To set up a local instance of the SymPortal analytical framework follow the directions below. SymPortal is designed to run in a MacOS or Linux environment.

#### 1. Obtain a copy of the code

The simplest option is to download the [latest release](https://github.com/SymPortal/SymPortal_framework/releases/latest) and decompress in your chosen working directory.

Alternatively if you are comfortable with `git` and plan to share with others any changes you make to the code, clone the SymPortal repository. See GitHub's [online documentation](https://help.github.com/articles/cloning-a-repository/) for instructions.


#### 2a. Configure settings.py

You will find a file in your chosen directory named: **settings_blank.py**.

Rename this file to **settings.py**.

Generate a bespoke secret key [here](https://www.miniwebtool.com/django-secret-key-generator/) and paste it into your settings.py file so that:

`SECRET_KEY = ''`

becomes (key only for example)

`SECRET_KEY = '^3#q(9(ky$5#=tg$8m45k*5gh!==n&nh)5*8=hf0g)giob^mzd'`

Save the modified **settings.py**.

#### 2b. Configure sp_config (optional)

Replace the value for the `user_name` and `user_email` keys in the **sp_config** file.

e.g. 

`"user_name"   :   "undefined"` --> `"user_name"   :   "bcchume"`

`"user_email"   :   "undefined"` --> `"user_name"   :   "bcchume@symportal.com"`

You will not be contacted on this email, it will only be used to log your details with the data_set and data_analysis objects. It will also be appended to output results.


#### 3. Setting up the Python environment

The SymPortal framework is written in the [Python 3](https://www.python.org/download/releases/3.0/) language.

To run SymPortal you must have installed a Python 3.6 environment with all of the libraries specified in [requirements.txt](https://github.com/didillysquat/SymPortal_framework/blob/master/requirements.txt).

To prevent problems with other programs and existing Python installations, we recommend starting with a fresh [Conda](https://conda.io) environment. The details of how to set up a Python environment on your machine are outside the scope of this document, but [this page](https://conda.io/docs/user-guide/install/) is a good starting point.

**WARNING:** modifying or replacing your system's Python installation can cause other programs to stop working. If you are unsure of what you are doing, please consult someone with more experience.

To install the libraries SymPortal depends on, from within your Python environment you can type:

```console
(sp_venv)$ pip install -r requirements.txt
```


#### 4. Creating the database

SymPortal stores its results in a SQL database. This database can be either a [SQLite](https://www.sqlite.org) or [PostgreSQL](https://www.postgresql.org) database.

By default, SymPortal is configured to create a SQLite database. This is the simplest configuration. If you are an advanced user and are considering running SymPortal with multiple processors to improve performance, read [this section](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#a-note-on-running-symportal-with-multiple-processors).

To create the SQLite database and automatically populate it with the tables defined in the **dbApp/models.py** file we will use the `makemigrations` and `migrate` commands from the [Django API](https://www.djangoproject.com/) via the **manage.py** entry point.

Enter your Python environment and change into the **SymPortal_framework** directory. Then run:
```shell
$ python3.6 manage.py makemigrations
$ python3.6 manage.py migrate
```

The first command will create the file **dbApp/migrations/0001_initial.py**. And the second command will create **db.sqlite3** in your working directory. This is your SQLite database populated with empty tables.


#### 5. Populating the local database with SymPortal's reference_sequences

An important aspect of the SymPortal framework is its enforced standardisation of data. The framework's quality controls ensure that all submitted sequence data goes from raw input to output following the same set of standards. 

One key part of this standardisation is the naming of sequences. The same nucleotide sequence should always have the same name and these nucleotide sequences should always represent the same region of the rDNA.

To ensure that SymPortal maintains good agreement with the important sequence names already existing in the literature, e.g. A1, D1, D4, D6, C1, C3, C15 and C21 to name just a few, the SymPortal database has been initialised using a carefully curated collection of > 350 commonly found sequences. However, ITS2 sequence diversity is immense and many thousands of sequences have already been submitted to SymPortal.org. To assign identities to these sequences SymPortal generates systematic alphanumeric identifiers, e.g. C3ac or D1cd. However, given the immensity of ITS2 sequence diversity, not all sequences are given names. Only those sequences defined as DIVs and used to define ITS2 type profiles are named. I.e. only sequences used to characterise putative _Symbiodinium_ taxa are named.

A SymPortal analysis run locally is not integrated with the central SymPortal.org database. Whilst the local analysis could also generate alphanumeric identifiers, doing so would risk assigning alphanumeric identifiers that were inconsistent with the SymPortal.org database. I.e. more than one name could be associated to a single sequence or vice versa. To prevent an inconsistent mapping from arising, local runs of SymPortal do not generate alphanumeric identifiers for sequences that are currently unnamed. For DIVs output from local analyses, an alphanumeric identifier will be used if available; otherwise the UID from the local database will be used instead.

However, to aid in the identification of ITS2 type profiles and ITS2 sequences output from local analyses, a file containing a recent copy of all named sequences from the current SymPortal.org database is provided in [refSeqDB.fa](https://github.com/SymPortal/SymPortal_framework/tree/master/symbiodiniumDB).

#### 5.1. Populating reference_sequences

To make use of the sequences contained in the [refSeqDB.fa](https://github.com/SymPortal/SymPortal_framework/tree/master/symbiodiniumDB) file, they must be input to the local database. A standalone script has been provided to do this: [populate_db_ref_seqs.py](https://github.com/SymPortal/SymPortal_framework/tree/master/populate_db_ref_seqs.py).

Simply run:
```shell
$ python3.6 populate_db_ref_seqs.py
```


#### 6. Third party dependencies

SymPortal relies on a number of third party programs that can be divided into core dependencies (required for the core SymPortal analysis) and additional dependencies (required only for the UniFrac-based ordination functions; no additional dependencies are required for the BrayCurtis-based ordinations that are run as default).

__Core dependencies__ (required)
To perform quality filtering SymPortal relies on the following core third party programs (please visit the links for information on how to install them):
* [mothur](https://www.mothur.org/) (version=1.39.5)
* [BLAST+ executables](): blastn; makeblastdb (version 2.6.0+)

__Additional dependencies__ (only required for UniFrac-based ordination analyses) 
* [SumTrees](https://www.dendropy.org/programs/sumtrees.html): sumtrees.py
* [MAFFT](https://mafft.cbrc.jp/alignment/software/)
* [PHYLIPNEW](http://emboss.sourceforge.net/) __(please see the note on installing PHYLIPNEW below that details an alternative to compiling the executables yourself)__

__Testing dependencies__

For SymPortal to run correctly, each of the above executables must be in your system `PATH` variable.
To test whether these executables are found in your path you can use the _which_ command.
```console
$ which mothur
/usr/bin/mothur

$ which o-pad-with-gaps
/home/user/anaconda2/bin/o-pad-with-gaps

$ which sumtrees.py
/home/user/miniconda3/bin/sumtrees.py

$ which blastn
/home/user/ncbi-blast-2.6.0+/bin/blastn

$ which makeblastdb
/home/user/ncbi-blast-2.6.0+/bin/makeblastdb

$ which mafft
/home/user/mafft/bin/mafft
```
If the executable is installed and found in your PATH, the path to the executable will be returned.

Please also ensure that the programs are working correctly. You should see output similar to the below:
```console
$ mothur -v
Mothur version=1.39.5
Release Date=3/20/2017

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

##### Installing the PHYLIPNEW collection of commands

In order to be able to compute UniFrac-based distance matrices and PCoA ordinations between sample and ITS2 type profiles, respectively, some further packages are required. These packages are all contained in the PHYLIPNEW package which is part of the [EMBOSS](http://emboss.sourceforge.net/) project. The PHYLIPNEW tar.gz can be downloaded from [here](ftp://emboss.open-bio.org/pub/EMBOSS/). There is a bit of knack to getting the PHYLIPNEW packages installed on a system. I recommend reading the answer to this question: [Can you give an example of how to install an EMBASSY package](http://emboss.sourceforge.net/docs/faq.html).

Because the compilation of these executable can be difficult (due to library dependencies) I have also made the precompiled executables available [here](https://drive.google.com/drive/folders/1W0rw8UpW2-Ly1BLiIY-kCiZ8KsZMtzWc?usp=sharing). To 'install' these executables for use in SymPortal, download the .zip file, decompress it and copy the three executables into your `./lib/phylipnew/` directory. SymPortal will automatically check to see whether the required executables are found in your system's PATH. If not, it will search for them in this ./lib/phylipnew/ directory.


#### 7. Checking your installation

If you have set up SymPortal correctly, you should see the following when you run `main.py`:

```console
$ ./main.py
/path/to/repo/SymPortal_framework/db.sqlite3
usage: main.py [-h] [--submit path_to_dir] [--display_data_sets]
               [--analyse data_set IDs]
               [--print_output data_set IDs, analysis ID] [--display_analyses]
               [--num_proc NUM_PROC] [--name NAME] [--description DESCRIPTION]
               [--db_version DB_VERSION] [--vacuum_database]
main.py: error: one of the arguments --submit --display_data_sets --analyse --print_output --display_analyses --vacuum_database is required
```

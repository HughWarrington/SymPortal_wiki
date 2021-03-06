This page describes how to access the data stored in your SymPortal database.

# Contents

* [**The Django interface**](#the-django-interface)
    * [Starting a Python interactive shell and using the Django API to interact with your database](#starting-a-python-interactive-shell-and-using-the-django-api-to-interact-with-your-database)
    * [Importing database objects](#importing-database-objects)
* [**Interacting with your database**](#interacting-with-your-database)
    * [Deleting and renaming objects](#deleting-and-renaming-objects)
* [**The database objects**](#the-database-objects)
    * [**Data submission-based objects**](#data-submission-based-objects)
        * [The _**data_set**_ object](#the-data_set-object)
        * [The _**data_set_sample**_ object](#the-data_set_sample-object)
        * [The _**clade_collection**_ and _**data_set_sample_sequence**_ objects](#the-clade_collection-and-data_set_sample_sequence-objects)
        * [The _**reference_sequence**_ object](#the-reference_sequence-object)
    * [**Data analysis-based objects**](#data-analysis-based-objects)
        * [The _**data_analysis**_ object](#the-data_analysis-object)
        * [The _**analysis_type**_ object](#the-analysis_type-object)
        * [The _**clade_collection_type**_ object](#the-clade_collection_type-object)

# The Django interface
SymPortal uses an [API provided as part of the Django module](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#playing-with-the-api) to interact with the SymPortal database. By using the Django API, the database can be queried in an interactive Python shell using Python objects, rather than having to write SQL.

### Starting a Python interactive shell and using the Django API to interact with your database
To start a Python shell and connect to your database using Django, run the following command from within your SymPortal_framework base directory:
```console
(sp_venv) $ python3 manage.py shell

Python 3.6.5 |Anaconda, Inc.| (default, Apr 26 2018, 08:42:37) 
Type 'copyright', 'credits' or 'license' for more information
IPython 6.4.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]:
```

### Importing database objects
To work with any database objects in this interactive shell you will first need to import them from the models.py file that is in your SymPortal_framework/dbApp/ directory. The following command will import all database objects:
```console
In [1]: from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, analysis_group, data_set_sample, data_analysis, clade_collection, clade_collection_type
```

# Interacting with your database
Eventually, an in depth description of the database schema will be provided as part of this wiki. For the time being, the [models.py](https://github.com/SymPortal/SymPortal_framework/blob/master/dbApp/models.py) file describes the different classes, their attributes, and relationships to other classes.

A thorough introduction to how to work with the Django API can be found in the [official docs](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#playing-with-the-api), but for basic functions such as querying and deleting objects, the below code examples are a primer.

## Deleting and renaming objects
The below example uses the _**data_set**_ class, however this code can be applied to any of the imported database classes.
```python
# import all the database model classes
In [1]: from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, analysis_group, data_set_sample, data_analysis, clade_collection, clade_collection_type

# display the id and name of each of the data_set objects in your database
In [2]: for ds in data_set.objects.all():
   ...:    print('{}:{}'.format(ds.id, ds.name))

1 first_data_set_submission
2 second_data_set_submission
3 third_data_set_submission
4 fourth_data_set_submission

# delete a data_set object
In [2]:data_set.objects.get(id=3).delete()
Out[3]: 
(43,
 {'dbApp.data_set_sample_sequence': 0,
  'dbApp.data_set': 1,
  'dbApp.data_set_sample': 42})

In [3]: for ds in data_set.objects.all():
   ...:    print('{}:{}'.format(ds.id, ds.name))

1 first_data_set_submission
2 second_data_set_submission
4 fourth_data_set_submission

# delete several data_set objects at once
In [4]:data_set.objects.filter(id__in=[2,4]).delete()
Out[4]: 
(234,
 {'dbApp.clade_collection_type': 0,
  'dbApp.data_set_sample_sequence': 196,
  'dbApp.data_set': 2,
  'dbApp.data_set_sample': 16,
  'dbApp.clade_collection': 20})

In [5]: for ds in data_set.objects.all():
   ...:    print('{}:{}'.format(ds.id, ds.name))

1 first_data_set_submission

# change the name of an object
In [6]: ds = data_set.objects.get(id=1)

In [7]: ds.name = 'ADifferentName'

# remember to call the save method
In [8]: ds.save()

In [9]: ds
out[9]: <data_set: ADifferentName>
```

# The database objects
## Data submission-based objects
The below example will give you an introduction into the structure of the database, the objects it holds, and their inter-relations through some code examples.

The data submission-based objects are:
* _**data_set**_ - a collection of samples usually from the same study
* _**data_set_sample**_ - a single sample
* _**clade_collection**_ - a collection of sequences of the same clade from a given sample
* _**data_set_sample_sequence**_ - an ITS2 sequence found in a single sample
* _**reference_sequence**_ - an ITS2 sequence that may be found in multiple samples

The following examples assume you have first run this `import` statement:

```python
# import all the database model classes
In [1]: from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, data_set_sample, data_analysis, clade_collection, clade_collection_type
```
### The _**data_set**_ object
```python
In [2]: for ds in data_set.objects.all():
   ...:    print('{}:{}'.format(ds.id, ds.name))

1 first_data_set_submission
2 second_data_set_submission
3 third_data_set_submission
4 fourth_data_set_submission
```

_**data_set**_ objects represent a set of samples that are usually part of a single study.
```python
# create a variable 'first_data_set_var' and assign a data_set object to it
In [2]: first_data_set_var = data_set.objects.get(id=1)

In [3]: first_data_set_var
Out[3]: <data_set: first_data_set_submission>
```

All of the database objects have attributes. The full list can be seen in the [models.py](https://github.com/SymPortal/SymPortal_framework/blob/master/dbApp/models.py) file.
We'll take a look at a couple of useful ones here:
```python
In [4]: first_data_set_var.name
Out[4]: 'first_data_set_submission'

In [4]: first_data_set_var.id
Out[4]: 1

In [5]: first_data_set_var.reference_fasta_database_used
Out[5]: 'symClade.fa'

In [6]: first_data_set_var.timeStamp
Out[6]: '2018-10-18 06:49:43.750268'
```
### The _**data_set_sample**_ object
All of the database objects are related to at least one other database object. Database objects can be queried according to these relationships. The _**data_set**_ object has _**data_set_sample**_ objects associated with it.
```python
In [7]: dss_objects_var = data_set_sample.objects.filter(dataSubmissionFrom=first_data_set_var)

In [8]: dss_objects_var
Out[8]: <QuerySet [<data_set_sample: AW0000216_BG8KK_12BA102>, <data_set_sample: AW0000128_BG8KK_12BA099>, <data_set_sample: AW0000136_BG8KK_12BA166>, <data_set_sample: AW0000120_BG8KK_12BA182>]>

In [9]: for dss_object in dss_objects_var:
   ...:    print('{}:{}'.format(dss_object.id, dss_object.name))

745 AW0000216_BG8KK_12BA102
746 AW0000128_BG8KK_12BA099
747 AW0000136_BG8KK_12BA166
748 AW0000120_BG8KK_12BA182

In [10]: dss_object = data_set_sample.objects.get(id=745)

In [11]: dss_object
Out[11]: <data_set_sample: AW0000216_BG8KK_12BA102>
```
#### __Some attributes of _**data_set_sample**_ objects:__

* _name_
* _initialTotSeqNum_ - the number of sequences after contig construction, before QC

* _post_seq_qc_absolute_num_seqs_ - absolute number of sequences after initial QC but before taxonomic QC (contains non-Symbiodiniaceae sequences)
* _initialUniqueSeqNum_ - the number of unique (distinct) sequences at same stage as above

* _finalTotSeqNum_ - absolute number of sequences after all QC including taxonomic QC (contains only Symbiodiniaceae sequences)
* _finalUniqueSeqNum_  - the number of unique (distinct) sequences at same stage as above

* _non_sym_absolute_num_seqs_ - absolute number of non-Symbiodiniaceae sequences
* _nonSymSeqsNum_ - the number of unique (distinct) sequences at the same stage as above

* _size_violation_absolute_ - absolute number of sequences thrown out due to size violations (too large or too small)
* _size_violation_unique_ - the number of unique (distinct) sequences at same stage as above

* _post_med_absolute_ - absolute number of sequences after all QC and Minimum Entropy Decomposition
* _post_med_unique_ - the number of unique (distinct) sequences at same stage as above

* _cladalSeqTotals_ - A comma separated list of the number of sequences from each of the Symbiodiniaceae clades

#### __meta-data attributes (these will be expanded in the future)__

* _sample_type_
* _host_phylum_
* _host_class_
* _host_order_
* _host_family_
* _host_genus_
* _host_species_
* _collection_latitude_
* _collection_longitude_
* _collection_date_
* _collection_depth_

### The _**clade_collection**_ and _**data_set_sample_sequence**_ objects
_**data_set_sample**_ objects are associated with _**clade_collection**_ objects. A _**clade_collection**_ object holds all of the sequences (_**data_set_sample_sequence**_ objects) of a given clade from a given sample IF the total abundance of those sequences is greater than 200. If less than 200 then SymPortal deems this collection of sequences to be too small to attempt to predict ITS2 type profiles robustly (the probability of encountering sequencing depth artefacts is too high) and no _**clade_collection**_ object will be created. To ensure that all sequences are still associated with every _**data_set_sample**_ (even if there are <200 sequences of a given clade), _**data_set_sample_sequence**_ objects are linked directly to each _**data_set_sample**_ object and to a _**clade_collection**_.

```python
# get a QuerySet of the clade_collection objects associated with the data_set_sample assigned to the dss variable
In [12]: cc_objects_var = clade_collection.objects.filter(dataSetSampleFrom=dss_object)

In [13]: cc_objects_var
Out[13]: <QuerySet [<clade_collection: AW0000216_BG8KK_12BA102>, <clade_collection: AW0000216_BG8KK_12BA102>]>

# display the IDs of the clade_collection objects associated with the data_set_sample object
In [14]: for cc in cc_objects_var:
    ...:     print(cc.id, cc.dataSetSampleFrom.name)
722 AW0000216_BG8KK_12BA102
723 AW0000216_BG8KK_12BA102

In [15]: cc_object_var = clade_collection.objects.get(id=722)

In [16]: cc_object_var
Out[16]: <clade_collection: AW0000216_BG8KK_12BA102>

# see what clade the clade_collection object is
In [17]: cc_object_var.clade
Out[17]: 'D'

# get a QuerySet of the data_set_sample_sequence objects associated with the clade_collection assigned to the cc_object_var variable
In [18]: dsss_objects_var = data_set_sample_sequence.objects.filter(cladeCollectionTwoFoundIn=cc_object_var)

In [19]: dsss_objects_var
Out[19]: <QuerySet [<data_set_sample_sequence: D1>, <data_set_sample_sequence: D4>, <data_set_sample_sequence: ID=17148>, <data_set_sample_sequence: D2>, <data_set_sample_sequence: ID=17150>, <data_set_sample_sequence: ID=17151>, <data_set_sample_sequence: ID=17152>, <data_set_sample_sequence: D1m>, <data_set_sample_sequence: ID=17154>, <data_set_sample_sequence: ID=17155>, <data_set_sample_sequence: D2.2>, <data_set_sample_sequence: ID=17157>, <data_set_sample_sequence: ID=17158>, <data_set_sample_sequence: ID=17159>, <data_set_sample_sequence: D17d>, <data_set_sample_sequence: D1r>, <data_set_sample_sequence: ID=17162>, <data_set_sample_sequence: D17e>, <data_set_sample_sequence: D17c>, <data_set_sample_sequence: ID=17165>]>

# remember that data_set_sample_sequence objects are also linked directly to the data_set_sample object:
In [20]: dss_object
Out[20]: <data_set_sample: AW0000216_BG8KK_12BA102>

In [21]: dsss_objects_directly_from_dss = data_set_sample_sequence.objects.filter(data_set_sample_from=dss_object)

In [22]: dsss_objects_directly_from_dss
Out[22]: <QuerySet [<data_set_sample_sequence: A3>, <data_set_sample_sequence: ID=17140>, <data_set_sample_sequence: ID=17141>, <data_set_sample_sequence: A1>, <data_set_sample_sequence: ID=17143>, <data_set_sample_sequence: ID=17144>, <data_set_sample_sequence: ID=17145>, <data_set_sample_sequence: D1>, <data_set_sample_sequence: D4>, <data_set_sample_sequence: ID=17148>, <data_set_sample_sequence: D2>, <data_set_sample_sequence: ID=17150>, <data_set_sample_sequence: ID=17151>, <data_set_sample_sequence: ID=17152>, <data_set_sample_sequence: D1m>, <data_set_sample_sequence: ID=17154>, <data_set_sample_sequence: ID=17155>, <data_set_sample_sequence: D2.2>, <data_set_sample_sequence: ID=17157>, <data_set_sample_sequence: ID=17158>, '...(remaining elements truncated)...']>

```
__Attributes of the _**data_set_sample_sequence**_ object__

The only attribute present on _**data_set_sample_sequence**_ objects, that isn't a relation to another database object, is _abundance_. This attribute holds the absolute abundance that this _**data_set_sample_sequence**_ was found at in the _**data_set_sample**_.

### The _**reference_sequence**_ object
Obviously it would be useful to know the name of the sequence (if it has one), its clade and its actual nucleic sequence (i.e. all those lovely AGCTs). Given that the same sequence may be found in multiple samples (think about the D1 or C3 sequence), this information (name, clade and sequence) is not stored with the **_data_set_sample_sequence_** object. Rather it is stored with the **_reference_sequence_** object. In this way, if two _**data_set_sample**_ objects both contain the C3 sequence, each sample can have a unique _**data_set_sample_sequence**_ object associated with it that will store the information on its abundance. But both of the **_data_set_sample_sequence_** objects will have the same _referenceSequenceOf_ attribute that will point to the 'C3' _**reference_sequence**_ object. In this way, the name, clade and sequence information for the C3 sequence need only be stored once, rather than storing it for every occurrence of the sequence. This offers a dramatic saving of space and speedup for the SymPortal implementation. Therefore, to get the name, clade and sequence of a given **_data_set_sample_sequence_**, we must examine the associated **_reference_sequence_** object.

```python
In [23]: for dsss in dsss_objects_directly_from_dss:
    ...:     print(dsss.id, dsss.referenceSequenceOf.name)
17139 A3
17140 4670
17141 4671
17142 A1
17146 D1
17147 D4
17148 4675

In [24]: dsss_var = data_set_sample_sequence.objects.get(id=17139)

In [25]: dsss_var
Out[25]: <data_set_sample_sequence: A3>

# will NOT work
In [26]: dsss_var.name
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-36-6a8b9711c4d6> in <module>()
----> 1 dsss_var.name

AttributeError: 'data_set_sample_sequence' object has no attribute 'name'

In [27]: dsss_var.sequence
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-37-08231c9b607d> in <module>()
----> 1 dsss_var.sequence

AttributeError: 'data_set_sample_sequence' object has no attribute 'sequence'

In [28]: dsss_var.clade
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-39-9a1828fd0519> in <module>()
----> 1 dsss.clade

AttributeError: 'data_set_sample_sequence' object has no attribute 'clade'


# now with accessing the reference_sequence object
In [29]: dsss_var.referenceSequenceOf
Out[29]: <reference_sequence: A3>

In [30]: ref_seq_of_dsss = dsss_var.referenceSequenceOf

In [31]: ref_seq_of_dsss.name
Out[31]: 'A3'

In [32]: ref_seq_of_dsss.clade
Out[32]: 'A'

In [33]: ref_seq_of_dsss.sequence
Out[33]: 'AATGGCCTCTTGAACGTGCATTGCGCTCTTGGGATATGCCTGAGAGCATGTCTGCTTCAGTGCTTCTACTTTCTTTTCTGCTGCTCTTGTTATCAGGAGCAGTGCTGCTGCATGCTTCTGCAATTGGCACTGGCATGCTAAGTACCAAGTTTCGCTTGCTGTTGTGACTGATCAACATCTCATGTCGTTTCAGTTGGCGAAACAAAGGCTTGTGTGTTCCAACACTTCCTA'
```
## Data analysis-based objects
The data analysis-based objects are:
* _**data_analysis**_ - An analysis that was run on a collection of **_data_set_** objects
* _**analysis_type**_ - An ITS2 type profile found in one or more _**clade_collection**_
* _**clade_collection_type**_ - An abstract object used to link the _**analysis_type**_ and _**clade_collection**_ objects. This object therefore represents the link between data submission-based and data analysis-based objects

Again, the examples in the following section assume you have first run this `import` statement:

```python
# import all the database model classes
In [1]: from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, data_set_sample, data_analysis, clade_collection, clade_collection_type
```
### The _**data_analysis**_ object
The _**data_analysis**_ object represents an analysis that was run on a collection of _**data_set**_ objects. It is run from the command line using the ```--analysis``` flag.

```python
In [2]: for da in data_analysis.objects.all():
    ...:     print(da.id, da.name)
1 first_analysis
2 second_analysis
3 third_analysis
4 fourth_analysis

In [3]: da_var = data_analysis.objects.get(id=13)

In [4]: da_var
Out[4]: <data_analysis: data_analysis object>
```
**Some useful attributes:**
* _name_
* _description_
* _listOfDataSubmissions_ - a comma-delimited list of the IDs of the _**data_set**_ objects included in the analysis
* _timeStamp_

### The _**analysis_type**_ object
The _**analysis_type**_ object is the database's proxy for the ITS2 type profile. Each ITS2 type profile predicted/found in an analysis has a corresponding _**analysis_type**_ object.

```python
In [5]: at_objects_var = analysis_type.objects.filter(dataAnalysisFrom=da_var)

In [6]: at_objects_var
Out[6]: <QuerySet [<analysis_type: C3-C3gulf-C3c-C3aq>, <analysis_type: C3-C3gulf-C3d-C3i-C115c>]>

In [7]: for at in at_objects_var:
    ...:     print(at.id, at.name)
    ...:     
81 C3-C3gulf-C3c-C3aq
82 C3-C3gulf-C3d-C3i-C115c

In [8]: at_var = analysis_type.objects.get(id=81)

In [9]: at_var
Out[9]: <analysis_type: C3-C3gulf-C3c-C3aq>
```

**Some useful attributes:**
* _name_
* _dataAnalysisFrom_ - the _**data_analysis**_ object which the _**analysis_type**_ is associated with
* _clade_ - the taxonomic clade
* _coDom_ - boolean; whether there is more than one sequence found as the most abundant sequence in this _**analysis_type**_
* _orderedFootprintList_ - a comma separated list of the IDs of the _**reference_sequence**_ objects that make up the DIVs of this _**analysis_type**_

### The _**clade_collection_type**_ object
The _**clade_collection_type**_ object links the data analysis-based objects to the data submission-based objects. I.e. it allows us to answer questions like, which _**data_set_sample**_ objects contained a given _**analysis_type**_ object? There is one _**clade_collection_type**_ object for every _**clade_collection**_ that was found to contain a given _**analysis_type**_ (e.g. see below).

```python
# if we start with a single instance of an analysis_type object (like we currently have asigned
# to the at_var variable) we can ask the question, which samples contained this analysis_type?

# get a QuerySet of the clade_collection_type objects that associate with this analysis_type
In [10]: cct_objects_var = clade_collection_type.objects.filter(analysisTypeOf=at_var)

In [11]: cct_objects_var
Out[11]: <QuerySet [<clade_collection_type: C3-C3gulf-C3c-C3aq>, <clade_collection_type: C3-C3gulf-C3c-C3aq>, <clade_collection_type: C3-C3gulf-C3c-C3aq>, <clade_collection_type: C3-C3gulf-C3c-C3aq>]>

# get a QuerySet of the clade_collection objects that associate with any one 
# of the clade_collection_type objects found in this cct_objects_var QuerySet
In [12]: cc_objects_var = clade_collection.objects.filter(clade_collection_type__in=cct_objects_var)

In [13]: cc_objects_var
Out[13]: <QuerySet [<clade_collection: A01>, <clade_collection: A04>, <clade_collection: A02>, <clade_collection: A03>]>

# get a QuerySet of the data_sample objects that these clade_collection_objects come from
In [14]: dss_objects_var = data_set_sample.objects.filter(clade_collection__in=cc_objects_var)

In [15]: dss_objects_var
Out[15]: <QuerySet [<data_set_sample: A03>, <data_set_sample: A02>, <data_set_sample: A01>, <data_set_sample: A04>]>

# finally, let's see which data_set objects these samples come from
In [16]: ds_objects_var = data_set.objects.filter(data_set_sample__in=dss_objects_var)

In [17]: ds_objects_var
Out[17]: <QuerySet [<data_set: Smith_testing>, <data_set: Smith_testing>, <data_set: Smith_testing>, <data_set: Smith_testing>]>

# alternatively we can call the distinct() method to remove duplicates from the QuerySet
In [18]: ds_objects_var = data_set.objects.filter(data_set_sample__in=dss_objects_var).distinct()

In [19]: ds_objects_var
Out[19]: <QuerySet [<data_set: Smith_testing>]>
```

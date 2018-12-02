## THIS PAGE IS UNDER DEVELOPMENT
#### The Django interface
SymPortal uses an [API provided as part of the Django module](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#playing-with-the-api) to interact with the SymPortal database. By using the Django API, the database is queried using Python3 language rather than SQL through an interactive Python shell.

#### Starting a Python interactive shell and using the Django API to interact with your database
To start a Python3 shell and connect to your database using Django, you can run the following command from within your SymPortal_framework base directory:
```console
(sp_venv) $ python3 manage.py shell

Python 3.6.5 |Anaconda, Inc.| (default, Apr 26 2018, 08:42:37) 
Type 'copyright', 'credits' or 'license' for more information
IPython 6.4.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]:
```

This will start the interactive python shell and a connection to your database will automatically be created.

#### Importing database objects
To work with any database objects in this interactive shell you will first need to import them from the models.py file that is in your SymPortal_framework/dbApp/ directory. The following command will import all database objects.
```console
In [1]: from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, analysis_group, data_set_sample, data_analysis, clade_collection, clade_collection_type
```

#### Interacting with your database
Eventually, an in depth introduction to the database schematic, its objects, their relationships, and their attributes will be provided as part of this wiki. For the time being, if more details are required, the [models.py](https://github.com/SymPortal/SymPortal_framework/blob/master/dbApp/models.py) file should be fairly comprehensible. However, for basic functions such as querying and deleting objects, the below code examples should be a sufficient primer.

For a thorough introduction into how to work with these database objects using the Django API you can visit the following [link](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#playing-with-the-api). In this part of the wiki we will cover some of the basics.

#### Deleting and renaming objects
The below example uses the __data_set__ object however this code can be applied to any of the imported database objects.
```python
#display the id and name of each of the data_set objects in your database
In [1]: from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, analysis_group, data_set_sample, data_analysis, clade_collection, clade_collection_type

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
### An exploration of your database objects
The below example will give you an introduction into the structure of the database, the objects it holds, and their inter-relations through some code examples.

```python
In [1]: from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, analysis_group, data_set_sample, data_analysis, clade_collection, clade_collection_type
```
#### The __data_set__ object
```python
In [2]: for ds in data_set.objects.all():
   ...:    print('{}:{}'.format(ds.id, ds.name))

1 first_data_set_submission
2 second_data_set_submission
3 third_data_set_submission
4 fourth_data_set_submission
```

__data_set__ objects represent a set of samples that are usually part of a single study
```python
# create a variable 'first_data_set_var' and assign a data_set object to it
In [2]: first_data_set_var = data_set.objects.get(id=1)

In [3]: first_data_set_var
Out[3]: <data_set: first_data_set_submission>
```

All of the database objects have attributes. The full list can be seen in the [models.py](https://github.com/SymPortal/SymPortal_framework/blob/master/dbApp/models.py) file
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
#### The __data_set_sample__ object
All of the database objects are related to at least one other database object. Database objects can be queried according to these relationships. The __data_set__ object has __data_set_sample__ objects associated to it
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
__Some attributes of data_set_sample objects:__

_name_
_initialTotSeqNum_ - the number of sequences after contig construction, before QC

_post_seq_qc_absolute_num_seqs_ - absolute number of sequences after initial QC but before taxonomic QC (contains non-Symbiodiniaceae sequences)
_initialUniqueSeqNum_ - the number of unique (distinct) sequences at same stage as above

_finalTotSeqNum_ - absolute number of sequences after all QC including taxonomic QC (contains only Symbiodiniaceae sequences)
_finalUniqueSeqNum_  - the number of unique (distinct) sequences at same stage as above

_non_sym_absolute_num_seqs_ - absolute number of non-Symbiodiniaceae sequences
_nonSymSeqsNum_ - the number of unique (distinct) sequences at the same stage as above

_size_violation_absolute_ - absolute number of sequences thrown out due to size violations (too large or too small)
_size_violation_unique_ - the number of unique (distinct) sequences at same stage as above

_post_med_absolute_ - absolute number of sequences after all QC and Minimum Entropy Decomposition
_post_med_unique_ - - the number of unique (distinct) sequences at same stage as above

_cladalSeqTotals_ - A comma separated list of the number of sequences from each of the Symbiodiniaceae clades

__meta-data attributes (these will be expanded in the future)__

_sample_type_
_host_phylum_
_host_class_
_host_order_
_host_family_
_host_genus_
_host_species_
_collection_latitude_
_collection_longitude_
_collection_date_
_collection_depth_

#### The __clade_collection__ and __data_set_sample_sequence__ objects
__data_set_sample__ objects are associated with __clade_collection__ objects. A __clade_collection__ object holds all of the sequences (__data_set_sample_sequence__ objects) of a given clade from a given sample IF the total abundance of those sequences is greater than 200. If less then 200 then SymPortal deems this collection of sequences to be too small to attempt to predict ITS2 type profiles robustly (the probability of encountering sequencing depth artefacts is too high) and no __clade_collection__ object will be created. To ensure that all sequences are still associated with every __data_set_sample__ (even if there are <200 sequences of a given clade), __data_set_sample_sequence__ objects are associated directly to each __data_set_sample__ object and to a __clade_collection__.

```python
# get a QuerySet of the clade_collection objects associated to the data_set_sample assigned to the dss variable
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

# get a QuerySet of the data_set_sample_sequence objects associated to the clade_collection assigned to the cc_object_var variable
In [18]: dsss_objects_var = data_set_sample_sequence.objects.filter(cladeCollectionTwoFoundIn=cc_object_var)

In [19]: dsss_objects_var
Out[19]: <QuerySet [<data_set_sample_sequence: D1>, <data_set_sample_sequence: D4>, <data_set_sample_sequence: ID=17148>, <data_set_sample_sequence: D2>, <data_set_sample_sequence: ID=17150>, <data_set_sample_sequence: ID=17151>, <data_set_sample_sequence: ID=17152>, <data_set_sample_sequence: D1m>, <data_set_sample_sequence: ID=17154>, <data_set_sample_sequence: ID=17155>, <data_set_sample_sequence: D2.2>, <data_set_sample_sequence: ID=17157>, <data_set_sample_sequence: ID=17158>, <data_set_sample_sequence: ID=17159>, <data_set_sample_sequence: D17d>, <data_set_sample_sequence: D1r>, <data_set_sample_sequence: ID=17162>, <data_set_sample_sequence: D17e>, <data_set_sample_sequence: D17c>, <data_set_sample_sequence: ID=17165>]>

# remember that data_set_sample_sequence objects are also associated directly to the data_set_sample object:
In [20]: dss_object
Out[20]: <data_set_sample: AW0000216_BG8KK_12BA102>

In [21]: dsss_objects_directly_from_dss = data_set_sample_sequence.objects.filter(data_set_sample_from=dss_object)

In [22]: dsss_objects_directly_from_dss
Out[22]: <QuerySet [<data_set_sample_sequence: A3>, <data_set_sample_sequence: ID=17140>, <data_set_sample_sequence: ID=17141>, <data_set_sample_sequence: A1>, <data_set_sample_sequence: ID=17143>, <data_set_sample_sequence: ID=17144>, <data_set_sample_sequence: ID=17145>, <data_set_sample_sequence: D1>, <data_set_sample_sequence: D4>, <data_set_sample_sequence: ID=17148>, <data_set_sample_sequence: D2>, <data_set_sample_sequence: ID=17150>, <data_set_sample_sequence: ID=17151>, <data_set_sample_sequence: ID=17152>, <data_set_sample_sequence: D1m>, <data_set_sample_sequence: ID=17154>, <data_set_sample_sequence: ID=17155>, <data_set_sample_sequence: D2.2>, <data_set_sample_sequence: ID=17157>, <data_set_sample_sequence: ID=17158>, '...(remaining elements truncated)...']>

```











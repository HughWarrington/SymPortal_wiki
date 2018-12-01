#### The Django interface
SymPortal uses an [API provided as part of the Django module](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#playing-with-the-api) to interact with the SymPortal database. By using the Django API, the database is queried using Python3 language rather than SQL through an interactive Python shell.

To start a Python3 shell and connect to your database using Django, you can run the following command from within your SymPortal_framework base directory:
```console
(sp_venv) $ python3 manage.py shell

Python 3.6.5 |Anaconda, Inc.| (default, Apr 26 2018, 08:42:37) 
Type 'copyright', 'credits' or 'license' for more information
IPython 6.4.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]:
```

This will start the interactive python shell and a connection to your database will automatically be created.
To work with any database objects in this interactive shell you will first need to import them from the models.py file that is in your SymPortal_framework/dbApp/ directory. The following command will import all database objects.
```console
>>> from dbApp.models import data_set, reference_sequence, data_set_sample_sequence, analysis_type, analysis_group, data_set_sample, data_analysis, clade_collection, clade_collection_type
```
Eventually, an in depth introduction to the database schematic, its objects, their relationships, and their attributes will be provided as part of this wiki. For the time being, if more details are required, the [models.py](https://github.com/SymPortal/SymPortal_framework/blob/master/dbApp/models.py) file should be fairly comprehensible. However, for basic functions such as querying and deleting objects, the below code examples should be a sufficient primer.

For a thorough introduction into how to work with these database objects using the Django API you can visit the following [link](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#playing-with-the-api). In this part of the wiki we will cover some of the basics. All of the below commands will assume that you have already imported the database objects.



```python
#display the id and name of each of the data_set objects in your database
In [1]: for ds in data_set.objects.all():
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
```

The below example will give you an introduction into the structure of the database, the objects it holds, and their inter relations.


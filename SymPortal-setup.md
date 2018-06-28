To setup a local instance of the SymPortal analytical framework follow the directions below. The directions are aimed at getting SymPortal running using a MacOS or linux environment. However, these should be applicable to getting SymPortal running on a Window OS with only minor modifications.

#### 1. Clone the SymPortal repository into your chosen working directory.

See GitHub's [online documentation](https://help.github.com/articles/cloning-a-repository/) for further details.


#### 2. Configure settings.py

You will find a file in your chosen directory named: **settings_blank.py**.

Rename this file **settings.py**.

Generate a bespoke secret key [here](https://www.miniwebtool.com/django-secret-key-generator/) and paste it into your settings.py file so that:

`SECRET_KEY = ''` 

becomes 

`SECRET_KEY = '^3#q(9(ky$5#=tg$8m45k*5gh!==n&nh)5*8=hf0g)giob^mzd'` (key only for example)

Save the modified **settings.py**.

#### 3. Setting up Python3

The SymPortal framework is scripted in the [Python3](https://www.python.org/download/releases/3.0/) language.

**WARNING:** Please be aware that overwriting or replacing your system's current python installation can cause fatal instability. If you are unsure of what you are doing, please seek the advice of a person who does. 

The following instructions will direct you in setting up a Python3 virtual environment containing all containing all non-standard packages and modules required to get the SymPortal framework up and running. 

##### 3.1 Checking for an existing Python3 installation

To create a Python3 virtual environment you must have a Python3 installation on your system. To test whether you have an existing installation you can use `$which python3`. If your system returns a path to an installation e.g. 
```shell
sp_user@local:~/path/to/SymPortal_framework$ which python3
/usr/local/bin/python3
```
then you have an existing installation. If no path is returned on the command line you likely do not have an installation or it is not in your PATH.

If you already have an existing installation skip to [3.3 Creating a Python3 virtual environment](#####3.3-Creating-a-Python3-virtual-environment)
 
<!---
Include link to detailed list of prerequisites
-->

##### 3.3 Creating a Python3 virtual environment

#### 4. Creating the framework's database

The SymPortal analysis is integrated with a mySQL database. This database can be either a SQLite or PostgreSQL database.

The cloned SymPortal repository is currently setup to create a [SQLite](https://www.sqlite.org) database. This database is a much lighter database than the [PostgreSQL](https://www.postgresql.org/) and is file-based rather than server-based.

To create the SQLite database (that will be named **db.sqlite3** by default) and automatically populate it with the tables defined in the **dbApp/models.py** file we will use the `makemigrations` and `migrate` commands from the [Django API](https://www.djangoproject.com/) via the **manage.py** file.

From within the SymPortal_framework directory.
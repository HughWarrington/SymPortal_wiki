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

#### 3. Setting up the Python3 environment

The SymPortal framework is scripted in the [Python3](https://www.python.org/download/releases/3.0/) language.

**WARNING:** Please be aware that overwriting or replacing your system's current python installation can cause fatal instability. If you are unsure of what you are doing, please seek the advice of someone who is. 

To run SymPortal you must have a python3 installation that satisfies all of SymPortal's [package and module prerequisites](#### 3.1. Package and module prerequisites). Irrespective of whether you already have a Python3 installation on your system, we recommend the [Miniconda](https://conda.io/docs/user-guide/install/download.html) or [Anaconda](https://conda.io/docs/user-guide/install/download.html) distributions. Please see the [docs](https://conda.io/docs/user-guide/install/download.html) for further information on which distribution suits your needs best. Both may be installed independent of root privileges.

We recommend running SymPortal via setting up a Python3 virtual environment ([conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) or [standard](https://docs.python.org/3.6/library/venv.html)) containing all required [package and module prerequisites](#### 3.1. Package and module prerequisites). But SymPortal may be run using any Python3 installation.

#### 3.1. Package and module prerequisites


#### 4. Creating the framework's database

The SymPortal analysis is integrated with a mySQL database. This database can be either a SQLite or PostgreSQL database.

The cloned SymPortal repository is currently setup to create a [SQLite](https://www.sqlite.org) database. This database is a much lighter database than the [PostgreSQL](https://www.postgresql.org/) and is file-based rather than server-based.

To create the SQLite database (that will be named **db.sqlite3** by default) and automatically populate it with the tables defined in the **dbApp/models.py** file we will use the `makemigrations` and `migrate` commands from the [Django API](https://www.djangoproject.com/) via the **manage.py** file.

From within the SymPortal_framework directory.
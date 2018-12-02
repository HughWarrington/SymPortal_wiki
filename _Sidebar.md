* #### [Home](https://github.com/SymPortal/SymPortal_framework/wiki/Welcome-to-SymPortal)

* #### [SymPortal setup](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup)
    * **[Getting SymPortal](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#1-download-the-latest-release-and-decompress-in-your-chosen-working-directory)**
    * **[Configuration files](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#2a-configure-settingspy)**
    * **[Python environment setup](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#3-setting-up-the-python36-environment)**
    * **[Database setup](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#4-creating-the-frameworks-database)**
    * **[Reference sequence population](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#5-populating-the-local-database-with-symportals-reference_sequences)**
    * **[Third party dependencies](https://github.com/SymPortal/SymPortal_framework/wiki/SymPortal-setup#6-third-party-dependencies)**

* #### [Running SymPortal](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal)
    * **[Submitting data](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#submitting-data)**
    * **[Running an analysis](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#running-an-analysis)**
    * **[Data output](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#data-output---independent-of-submission-or-analysis)**
    * **[Distances & Ordinations](https://github.com/SymPortal/SymPortal_framework/wiki/Running-SymPortal#generating-within-clade-pairwise-unifrac-distances-and-pcoa)**

* #### [Querying the SymPortal database](https://github.com/SymPortal/SymPortal_framework/wiki/Querying-the-SymPortal-database)
* [**The Django interface**](https://github.com/SymPortal/SymPortal_framework/wiki/Querying-the-SymPortal-database#the-django-interface)
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

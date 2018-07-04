This section will guide you through the 3 main steps of running a SymPortal analysis:
1. **Submitting data**
2. **Starting an analysis**
3. **Data output**

This guide assumes you already have [SymPortal setup]() and uses the same example dataset that is used in the [SymPortal manuscript]. This dataset can be downloaded from [here](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing).

***

#### i. Download data
Download the dataset from [here](https://drive.google.com/drive/folders/1qOZy7jb3leU_y4MtXFXxy-j1vOr1U-86?usp=sharing). Note the directory in which the dataset is saved. This will be used as an input in the following steps.

#### 1. Submitting data
The first step of analysing any dataset is to submit it to the SymPortal framework's database. In this step, SymPortal will perform all quality control filtering of the sequence data and convert the raw sequence data into database objects such as data_set, data_set_sample, clade_collection, data_set_sample_sequence and reference_sequence.

To submit a dataset to the database:
```shell

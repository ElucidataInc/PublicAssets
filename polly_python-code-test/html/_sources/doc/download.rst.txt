.. _targetd:

Downloading Dataset
*******************

1. Downloading any Dataset
--------------------------
To download any dataset, the following function can be used to get the signed URL of the dataset.

.. code::


        omixatlas.download_data("repo_key", "[dataset_id]")

|  ``repo_key``: (str) repo_id OR repo_name from where the data needs to be downloaded.
|  ``dataset_id``: (str) dataset_id which the user wants to download.
|  This will download the data.
|  The ``[repo_name OR repo_id]`` of an OmixAtlas can be identified by calling the get_all_omixatlas() function. The ``[dataset_id]`` can be obtained by querying the metadata at the dataset level using ``query_metadata("[query written in SQL]")``.
|  The output data is in .gct/h5ad format. This data can be parsed into a data frame for better accessibility using the following code:

2. Downloading .gct and opening it in a data frame
---------------------------------------------------

.. code::


            dataset_id = "GSE100003_GPL15207" #dataset which user wants to download.
            repo_key = 9 OR "geo" #repo_id OR repo_name from which dataset should be downloaded from.
            file_name = f"{dataset_id}.gct"
            data = client.download_data(repo_key, dataset_id)
            url = data.get('data').get('attributes').get('download_url')
            status = os.system(f"wget -O '{file_name}' '{url}'")
            if status == 0:
                print("Downloaded data successfully")
            else:
                raise Exception("Download not successful")

In order to parse the .gct data, a python package called cmapPy can be used in the following manner.

.. code::


            import pandas as pd
            import cmapPy
            from cmapPy.pandasGEXpress.parse_gct import parse

            gct_obj = parse(file_name) # Parse the file to create a gct object
            df_real = gct_obj.data_df # Extract the dataframe from the gct object
            col_metadata = gct_obj.col_metadata_df # Extract the column metadata from the gct object
            row_metadata = gct_obj.row_metadata_df # Extract the row metadata from the gct object


3. Downloading .h5ad file and opening it in a data frame
---------------------------------------------------------


.. code::


            dataset_id = "GSE121001_GPL19057" #dataset which user wants to download.
            repo_key = 17 OR "sc_data_lake" #repo_id OR repo_name from which dataset should be downloaded from.
            file_name = f"{dataset_id}.h5ad"
            data = client.download_data(repo_key, dataset_id)
            url = data.get('data').get('attributes').get('download_url')
            status = os.system(f"wget -O '{file_name}' '{url}'")
            if status == 0:
                print("Downloaded data successfully")
            else:
                raise Exception("Download not successful")


In order to parse the .h5ad data, a python package called scanpy can be used in the following manner.


.. code::


            import pandas as pd
            import scanpy
            data = sc.read_h5ad(file_name)
            obs = data.obs.head()
            var = data.var.head()


In order to get started with analysis of single cell data on Polly, users can refer to this `notebook <https://github.com/ElucidataInc/polly-python/blob/main/consumption_starter_notebooks/SingleCell-polly-python.ipynb>`_ hosted on our github.


.. _targetq:

Querying metadata
*****************

1. Writing a query
------------------
The polly-python library versions 0.0.7 to 0.0.9 are compatible with both storage infrastructures - v1 and v2. In order to facilitate querying on both infrastructures, there are two different API versions as well, v1 and v2, available in polly-python which refer to infrastructure used for query. This could be specified by setting query_api_version to 'v1' or 'v2' while passing the query to a function. By default, queries are made against v1. If users want to use v2 then they will have to pass an additional argument query_api_version='v2' in the query_metadata function. The querying on these two infrastructures is different and will be discussed in two different sections below.

1.2 Writing a query in V1 storage infrastructure
================================================
The complete syntax for searching and aggregating data is as follows:


.. code::


            SELECT [DISTINCT] (* | expression) [[AS] alias] [, ...]
            FROM { index_name | {[repo_id]|[repo_name]}.{datasets|samples|features} }
            [WHERE predicates]
            [GROUP BY expression [, ...]
            [HAVING predicates]]
            [ORDER BY expression [IS [NOT] NULL] [ASC | DESC] [, ...]]
            [LIMIT [offset, ] size]

1.3. Querying the dataset level metadata:
=========================================

.. code::


            query = "SELECT [column_name] FROM [files] WHERE [column_name]='[value]'"
            query = "SELECT [column_name] FROM [repo_name].datasets WHERE [column_name]='[value]'"
            query = "SELECT [column_name] FROM [repo_id].datasets WHERE [column_name]='[value]'"

1.4. Querying the sample level metadata:
========================================
For all samples except Single Cell

.. code::


            query = "SELECT [column_name] FROM [gct_metadata] WHERE [column_name]='[value]'"
            query = "SELECT [column_name] FROM [repo_name].samples WHERE [column_name]='[value]'"
            query = "SELECT [column_name] FROM [repo_id].samples WHERE [column_name]='[value]'"

For samples in Single Cell

.. code::
 
 
            query = "SELECT [column_name] FROM [h5ad_metadata] WHERE [column_name]='[value]'"

1.5. Querying the feature level metadata:
=========================================
For all features except Single Cell


.. code::


            query = "SELECT [column_name] FROM [gct_data] WHERE [column_name]='[value]'"
            query = "SELECT [column_name] FROM [repo_name].features WHERE [column_name]='[value]'"
            query = "SELECT [column_name] FROM [repo_id].features WHERE [column_name]='[value]'"

For features in Single Cell


.. code::


           query = "SELECT [column_name] FROM [h5ad_data] WHERE [column_name]='[value]'"


.. _target:

2. Experimental Expanding Search Feature
----------------------------------------
A disease is described by many different names in the literature. If we search it with a keyword, datasets annotated with related keywords for the same disease are missed in the output. For example, if 'nash' is searched for disease, 'non-alcoholic fatty liver disease', 'nonalcoholic steatohepatitis', 'nash-non-alcoholic steatohepatitis', 'non-alcoholic steatohepatitis' are missed although they are valid hits for disease keyword 'nash'.

To address this, we have included an experimental feature which allows expansion of disease query to include additional similar keywords based on ontology mapping. The query written in the Polly Python Library is expanded on the basis of the ontology tree mapping available in the following resources

|  [1]. `The disease ontology <https://disease-ontology.org/>`_
|  [2]. `Experimental factor ontology <https://www.ebi.ac.uk/efo/>`_
|  [3]. `Mondo Disease Ontology <http://obofoundry.org/ontology/mondo.html>`_
|  [4]. `NCI Thesaurus <https://github.com/NCI-Thesaurus/thesaurus-obo-edition>`_
|  [5]. `Human phenotype ontology <https://hpo.jax.org/>`_
|  [6]. `Ontology for MIRNA Target <https://github.com/OmniSearch/omit>`_

Expanding search feature could be use in the following way

Setting "expand" to True: It allows expansion of the query to include disease terms children from the ontology tree. It means no false positive datasets are included in the output while including more true positives.

.. code::


            result = omixatlas.query_metadata(
                query=user_query,
                experimental_features = {"expand":True, "related_terms":False}
            )
Setting "related_terms" to True: It allows expansion of query by including immediate parent of the disease term in the ontology tree. It causes maximum expansion but a few false positive datasets may be included.

.. code::


            result = omixatlas.query_metadata(
                query=user_query,
                experimental_features = {"expand":True, "related_terms":True})

2.1 Example queries
===================
Querying datasets in Liver OmixAtlas
====================================
To identify datasets belonging to the tissue Liver, disease Liver cirrhosis and organism Homo sapiens

.. code::


            query = """SELECT * FROM liveromix_atlas.datasets
                                    WHERE disease = 'liver cirrhosis'
                                    AND tissue = "liver"
                                    AND organism="Homo sapiens"
                                    """

To identify all datasets belonging to a Hepatocellular Carcinoma disease in Human and Mouse

.. code::


            query = """SELECT * FROM liveromix_atlas.datasets
                                    WHERE disease = 'Carcinoma, Hepatocellular'
                                    AND ( organism="Homo sapiens" or organism="Mus musculus")
                                """

To identify type of data, tissue, and data description of all the datasets belonging to a Hepatocellular Carcinoma disease in Human and Mouse

.. code::


            query = """SELECT dataset_id,description,tissue,kw_data_type
                                    FROM liveromix_atlas.datasets
                                    WHERE disease = 'Carcinoma, Hepatocellular'
                                    AND ( organism="Homo sapiens" or organism="Mus musculus")
                                """

Identify all transcriptome datasets in Hepatocellular Carcinoma disease in Human and Mouse

.. code::


            query = """SELECT * FROM liveromix_atlas.datasets
                                WHERE disease = 'Carcinoma, Hepatocellular'
                                AND ( organism="Homo sapiens" or organism="Mus musculus")
                                AND ( kw_data_type = "Transcriptomics")
                            """

Identify all transcriptome datasets from GEO in Hepatocellular Carcinoma disease in Human and Mouse

.. code::


            query = """SELECT * FROM liveromix_atlas.datasets
                                WHERE disease = 'Carcinoma, Hepatocellular'
                                AND ( organism="Homo sapiens" or organism="Mus musculus")
                                AND ( kw_data_type = "Transcriptomics")
                                AND ( dataset_source = "GEO")
                            """


Select all methylation, miRNA, and transcriptomics datasets for Hepatocellular Carcinoma disease in Human and Mouse

.. code::


            query = """SELECT * FROM liveromix_atlas.datasets
                                WHERE dataset_source = 'TCGA'
                                AND (kw_data_type = 'Methylation'
                                    OR kw_data_type = 'MiRNA'
                                    OR kw_data_type = 'Transcriptomics'
                                    )
                                AND ( organism="Homo sapiens" or organism="Mus musculus")
                            """ 


In all the examples above liveromix_atlas_files could be used in place of liveromix_atlas.datasets

Querying samples in Liver OmixAtlas
===================================
Select a sample with a GEO ID

.. code::


            query = """SELECT *
                        FROM liveromix_atlas.samples
                        WHERE kw_column  = 'GSM798352'
                        """

Identify all samples in which "CYP1B1" gene has been knocked out

.. code::


            query = """SELECT *
                        FROM liveromix_atlas.samples
                        WHERE kw_curated_genetic_mod_type = 'knockout'
                        AND kw_curated_modified_gene = "CYP1B1"
                        """

Identify all samples where gene "YAP1" has been knocked in, in hepatocype cell line

.. code::


            query = """SELECT kw_curated_cell_line,kw_curated_drug,kw_curated_genetic_mod_type,kw_curated_modified_gene,kw_curated_cell_type
                            FROM liveromix_atlas.samples
                            WHERE kw_curated_genetic_mod_type = 'knockin'
                            AND kw_curated_cell_type = "hepatocyte"
                            AND kw_curated_modified_gene = "YAP1"
                        """

Identify all samples of 'Carcinoma, Hepatocellular' disease that have been treated with some drug

.. code::


            query = """SELECT kw_curated_cell_line,kw_curated_drug,kw_curated_tissue,kw_curated_disease
                        FROM liveromix_atlas.samples
                        WHERE kw_curated_disease = 'Carcinoma, Hepatocellular'
                        AND kw_curated_drug <> "none"
                        """ 
                        
In all the examples above liveromix_atlas_gct_metadata could be used in place of liveromix_atlas.samples

Querying features in Liver OmixAtlas
====================================

Identify all samples in which the gene “YAP1” is upregulated

.. code::


            query = """SELECT *
                            FROM liveromix_atlas.features
                            WHERE kw_index = 'YAP1'
                            AND kw_column.kw_expression > 0
                        """ 
                        
In this example liveromix_atlas_gct_data could be used in place of liveromix_atlas.features

3. Writing a query in V2.1 storage infrastructure
-------------------------------------------------
The complete syntax for searching and aggregating data is as follows:


.. code::


            [ WITH with_query [, ...] ]
            SELECT [ ALL | DISTINCT ] select_expression [, ...]
            [ FROM from_item [, ...] ]
            [ WHERE condition ]
            [ GROUP BY [ ALL | DISTINCT ] grouping_element [, ...] ]
            [ HAVING condition ]
            [ { UNION | INTERSECT | EXCEPT } [ ALL | DISTINCT ] select ]
            [ ORDER BY expression [ ASC | DESC ] [ NULLS FIRST | NULLS LAST] [, ...] ]
            [ OFFSET count [ ROW | ROWS ] ]
            [ LIMIT [ count | ALL ] ]

Some example queries have been given in a notebook on our `github <https://github.com/ElucidataInc/polly-python/blob/main/polly-python-query-examples.ipynb>`_.

.. csv-table::
    :header: "Operators",  "Functions performed"
    :delim: |

    ``=`` |	Equal to operator which can be used to find matching strings with values in the columns
    ``<>`` |	Not equal to operator which can be used to find non-matching strings with values in the columns
    ``>`` |	Greater than operator which can be used ONLY for integer based columns
    ``<`` |	Less than operator which can be used ONLY for integer based columns
    ``>=`` |	Greater than or equal to operator which can be used ONLY for integer based columns
    ``<=`` |	Less than or equal to operator which can be used ONLY for integer based columns
    ``IS`` | NULL	Check if the field value is ``NULL``.
    ``IS`` | NOT NULL	Check if the field value is ``NOT NULL``.
    ``AND`` |	All values across the parameters searched for have to be present in a dataset for it to be returned as a match when the AND operator is used. e.g. “organism = ‘Homo sapiens' AND disease = 'Carcinoma, Hepatocellular’” would only return datasets that belong to homo sapiens and have the disease as hepatocellular carcinoma.
    ``OR`` |	Atleast any one value across the parameters searched for have to be present in a dataset for it to be returned as a match when the OR operator is used. e.g. ``organism = 'Homo sapiens' OR disease = 'Carcinoma, Hepatocellular'`` would return datasets that belong to homo sapiens or have the disease as hepatocellular carcinoma or match both criteria.
    ``MATCH QUERY(,'value')`` |	It works like a fuzzy search. If you add a string for a parameter with this operator, it would return all possible results matching each word in the string. The search output is returned with a “Score” using which the output is sorted. e.g. ``MATCH_QUERY(description,'Transcriptomics profiling')`` would return all datasets having ``transcriptomics profiling`` , ``Transcriptomics and profiling`` as possible terms within their description. Each dataset would be scored on the basis of matching of the searched string with the information present within the dataset.
    ``MATCH PHRASE(,'value')`` |	This can be used for exact phrase match with the information being searched for. e.g. ``MATCH_PHRASE(description,'Transcriptomics profiling')`` would only return the datasets that have ``Transcriptomics profiling`` within their description.
    ``MULTI MATCH('query'='value', 'column_name'='value)`` |	This can be used to search for text in multiple fields, use ``MULTI MATCH('query'='value', 'column_name'='value)``. e.g. ``MULTI MATCH('query'='Stem Cells', 'fields'='tissue','description')`` would return datasets that have ``"Stem Cell"`` in either tissue OR description fields.
    ``GROUP BY`` |	The GROUP BY operator groups rows that have the same values into summary rows. The ``GROUP BY`` statement is often used with aggregate functions ``(COUNT, MAX, MIN, SUM, AVG)`` to group the result-set by one or more columns.
    ``HAVING`` |	Use the HAVING clause to aggregate inside each bucket based on aggregation functions ``(COUNT, AVG, SUM, MIN, and MAX)``. The HAVING clause filters results from the GROUP BY clause
    ``COUNT(*)`` |	This counts each row present in a table/index being queried. NOTE: The output of this query would return a JSON stating the total number of rows in the table
    ``LIMIT`` |	NOTE: The response of any query returns 200 entries by default. You can extend this by defining the LIMIT of the results you want to query to be able to return.
    ``ORDER BY`` |	Can only be used to sort the search results using integer based parameters in the schema. Sorting on the basis of dataset_id, number of samples, _score of the data is available at the dataset-level metadata. ASC or DESC can be used to define whether you want to order the rows in ascending or descending order respectively

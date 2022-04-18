About Polly Library
*********************
Polly Libraries give access to the various capabilities on Polly like querying, filtering and accessing the data on Polly OmixAtlas. It allows access to data in OmixAtlas over any computational platform (like DataBricks, SageMaker, Polly, etc.) of your choice. These functionalities can be accessed through functions in python and `bash <https://docs.elucidata.io/Scaling%20compute/Polly%20CLI%201.html>`_ which can be used over a Terminal.

About Polly Python
------------------
Polly Python library provides convenient access to the above-mentioned functionalities through function in Python language.

.. figure:: ./Polly_property.png
   :height: 200
   :width: 185
   :scale: 300
   :alt: Overview of services that polly provides
   :align: center

   overview of services that polly provides

1. Installation
---------------
Install Polly Python using pip


.. code::


        pip install polly-python

2. Getting started
------------------
Import from libraries
The following libraries need to be imported over the development environment to access the data.

.. code::


        from polly.auth import Polly
        from polly.omixatlas import OmixAtlas
        from polly.workspaces import Workspaces
        from polly.cohort import Cohort

.. _auth:

3. Authentication
------------------
Authentication of the account is required to be able to access the capabilities of the Polly Python library.

3.1. Copying authentication token
==================================
To get this token, follow the steps below:

#. Go to `Polly <https://polly.elucidata.io/>`_
#. Click the User Options icon from the left-most panel
#. Click on Authentication on the panel that appears
#. Click on Copy to copy the authentication token

3.2 Using the token
====================
The following code is required to add the authentication function in the Polly Python library

.. code::


        AUTH_TOKEN = "[authentication_token_copied]"
        Polly.auth(AUTH_TOKEN)
        # after that you can make class objects without using token
        omixatlas = OmixAtlas()
        cohort1 = Cohort()
        cohort2 = Cohort()
        workspaces = Workspaces()




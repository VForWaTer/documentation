===========
WPS Toolbox
===========

Overview
========

The Tools in V-FOR-WaTer are implemented as a standalone server. It uses the `WPS Protocol <https://de.wikipedia.org/wiki/Web_Processing_Service>`_ to receive
inputs, data and configuration and returns processing output. Most data, including input data from the database, but also processing results and intermediate results
are saved to a temporary folder on the processing server. The WPS just exchange configuration and file-locations. For this to work, a set of *data types* are defined.
Each tool can only handle a specific, or a set of specified data types and has defined outputs. These data-types are hierachical. That means there are tools that 
can handle any type of timeseries data, while other will expect a timeseries of discharge data.

Data Types
==========

.. list-table:: Overview
  :widths: 20 20 20 40
  :header-rows: 1
  
  * - type name
    - file type (MIME, name)
    - reader function
    - description
  * - array
    - ``.npy``, ``.npz``
    - `numpy.load <https://numpy.org/doc/1.18/reference/generated/numpy.load.html>`_
    - 1D array, without any index information
  * - iarray
    - ``.csv``
    - `pandas.read_csv().iloc[:,0] <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - 1D array, index by any kind of pandas supported index, except ``DatetimeIndex``
  * - timeseries
    - ``.csv``
    - `pandas.read_csv().iloc[:,0] <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - 1D array, index by a ``DatetimeIndex``
    
Hierachical Order
=================

.. code-block:: 

  array
    - iarray
    - timeseries

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
  :widths: 20 20 30 30
  :header-rows: 1
  
  * - type name
    - file type
    - reader function
    - description
  * - array
    - ``.npy``, ``.npz``
    - `numpy.load <https://numpy.org/doc/1.18/reference/generated/numpy.load.html>`_
    - 1D array, without any index information
  * - iarray
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]).iloc[:,0] <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - 1D array, indexed by any kind of pandas supported index, except ``DatetimeIndex``.
  * - varray
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]).iloc[:,0] <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - 1D array, indexed by any kind of pandas supported index, except ``DatetimeIndex``. The ``pd.Series.name`` is a valid V-FOR-WaTer variable, e.g. the index may be a depth and the variable name ``'bulk density'``  
  * - timeseries
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]).iloc[:,0] <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - 1D array, indexed by a ``DatetimeIndex``
  * - vtimeseries
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]).iloc[:,0] <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - 1D array, indexed by a ``DatetimeIndex``. The ``pd.Series.name`` is a valid V-FOR-WaTer variable, e.g. ``'discharge'``
    
Hierachical Order
=================

.. code-block:: 

  array
    - iarray
      - varray
    - timeseries
      - vtimeseries

The hierachy is *upward* compatible. That means a named variable ``vtimeseries`` is also a ``timeseries`` and an ``array``, but not a `varray`. A tool might still accept ``varray`` **and** ``vtimeseries``.

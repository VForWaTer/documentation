===========
WPS Toolbox
===========

Overview
========

The Tools in V-FOR-WaTer are implemented as a standalone server. It uses the `WPS Protocol <https://de.wikipedia.org/wiki/Web_Processing_Service>`_ to receive
inputs, data and configuration and returns processing output. Most data, including input data from the database, but also processing results and intermediate results
are saved to a temporary folder on the processing server. The WPS just exchanges configuration and file-locations. For this to work, a set of *data types* are defined.
Each tool can only handle a specific, or a set of specified data types and has defined outputs. These data-types are hierachical. That means there are tools that 
can handle any type of timeseries data, while other will expect a timeseries of discharge data.

Data Types
==========

.. list-table:: Overview
  :widths: 20 20 35 25
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
  * - 2darray
    - ``.npy``, ``.npz``
    - `numpy.load <https://numpy.org/doc/1.18/reference/generated/numpy.load.html>`_
    - 2D array, without any index information
  * - ndarray
    - ``.npy``, ``.npz``
    - `numpy.load <https://numpy.org/doc/1.18/reference/generated/numpy.load.html>`_
    - N-dimensional array, without any index information
  * * - idataframe
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]) <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - multiple arrays, indexed by any kind of pandas supported index, except ``DatetimeIndex``.
  * - vdataframe
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]) <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - multiple arrays, indexed by any kind of pandas supported index, except ``DatetimeIndex``. All ``pd.DataFrame.columns`` have to be of same V-ForWater variable.
  * - time-dataframe
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]) <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - multiple arrays, indexed by a ``DatetimeIndex``.
  * - vtime-dataframe
    - ``.csv``
    - `pandas.read_csv(... index_col=[0]) <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_
    - multiple arrays, indexed by a ``DatetimeIndex``. All ``pd.DataFrame.columns`` have to be of same V-ForWater variable
  * - raster
    - ``gtiff``, ``.geotiff``, ``.asc``
    - `rasterio.open() `<https://rasterio.readthedocs.io/en/latest/api/rasterio.html?highlight=rasterio.open#rasterio.open>`_
    - Georeferenced raster image. Any file format supported by GDAL is supported by ``rasterio`` and therefore also here.
  * - vraster
    - ?
    - I have no idea
    - Variable bound georeferenced raster image. No idea how to do that.
   
    
Hierachical Order
=================

.. code-block:: 

  array
    - iarray
      - varray
    - timeseries
      - vtimeseries
   ndarray
    - raster
      - vraster
    - 2darray
    - idataframe
      - vdataframe
    - time-dataframe
      - vtime-dataframe

The hierachy is *downward* compatible. That means a named variable ``vtimeseries`` is also a valid ``timeseries`` and an ``array``, but not a `varray`. It is possbile that tools might accept data types from different branches. A tool that accepts ``array`` **and** ``ndarray`` will literally take *any* input.

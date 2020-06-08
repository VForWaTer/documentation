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
  * - idataframe
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
    - ``.gtiff``, ``.geotiff``, ``.asc``
    - `rasterio.open() <https://rasterio.readthedocs.io/en/latest/api/rasterio.html?highlight=rasterio.open#rasterio.open>`_
    - Georeferenced raster image. Any file format supported by GDAL is supported by ``rasterio`` and therefore also here.
  * - vraster
    - ?
    - I have no idea
    - Variable bound georeferenced raster image. No idea how to do that.
  * - html
    - ``.html``
    - ``open()``
    - generic HTML output. Tools that output text or HTML tables can use this *data-type*
  * - plot
    - ``.script.html``, ``.div.html``
    - ``open()``
    - `Bokeh components <https://docs.bokeh.org/en/latest/docs/user_guide/embed.html#components>`_ ``<script>`` and ``<div>`` tag for tools that output a Bokeh figure


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
   html
    - plot

The hierachy is *downward* compatible. That means a named variable ``vtimeseries`` is also a valid ``timeseries`` and an ``array``, but not a `varray`. It is possbile that tools might accept data types from different branches. A tool that accepts ``array`` **and** ``ndarray`` will literally take *any* input.

Defining Data-Types
===================

The definition of input and output data is done using a ``LiteralInput`` and ``LiteralOutput`` of type ``string``.
The actual data is stored in temporary files as specified in the table above.
All files of one output/input will use the same file name and only differ in file ending. The file name is a `UUID version 4 <https://de.wikipedia.org/wiki/Universally_Unique_Identifier#(Pseudo)zuf%C3%A4llig_generierte_UUIDs_(Version_4)>`_.
They will always consist of at least two files, one for the data and a metadata file of type ``.json``

WPS Input
---------

If a tool needs any of the file-based data types from above, the WPS process just needs to define an input of type string and awaits the UUID of
the given data source.

.. code-block:: python

  class MyTool(Process):
    def __init__(self):
      inputs = [
        LiteralInput(
          'timeseries',
          'UUID of the timeseries to be used',
          data_type='string',
          min_occurence=2,
          max_occurence=5
        )
      ]

The definition of inputs follows ``LiteralInput(identifier, title=None, data_type=None, abstract='', uoms=None, min_occurs=1, max_occurs=1, allowed_values=None, default=None)``

* identifier - Name of input and definition of more specific datatypes, e.g. ``input1__timeseries__discharge``.
* title - Title of input field exposed to the user.
* data_type - General data type of input. **Standard input is a textfield**. Adaptations for special cases are in preparation. E.g. ``string`` exposes a textfield; ``string`` **in combination with a specific datatype defined in the identifier exposes a dropdown menu**. Allowed are the basic data types ``'float', 'boolean', 'integer', 'string', 'positiveInteger', 'anyURI', 'time', 'date', 'dateTime', 'scale', 'angle', 'nonNegativeInteger'``.
* abstract - Information for the user about the data.
* uoms - units (not used yet)
* min_occurs - minimum occurrence of the input. ``default = 1``
* max_occurs - maximum occurrence of the input. ``default = 1``

.. list-table:: Use of min_occurs and max_occurs to define input types in web portal
  :widths: 15 15 35 25
  :header-rows: 1

  * - min_occurs
    - max_occurs
    - implication for wps input
    - element
  * - ``1``
    - ``1``
    - single value & required ``default``
    - dropdown
  * - ``0``
    - ``1``
    - single value & **not** required
    - dropdown
  * - ``0``
    - ``>1``
    - one or more values & **not** required
    - multi select dropdown
  * - ``>0``
    - ``>1``
    - one or more values & required
    - multi select dropdown

* allowed_values - defined values exposed to the user as **radio buttons**.
* default - value used when the user selects nothing for this input.

The definition of the file-based data types is supposed to be done in ``Keywords``, when they are implemented in ``pywps`` and ``owslib``.

The example above results in a dropdown that shows ``'UUID of the timeseries to be used'`` when the user hovers over the element. The user can select two to five timeseries data-sets from the dropdown (``min_occurence=2`` forces a ``*required`` flag to the input field, and ``max_occurence=5`` forces a multiple select option).

More information how to describe the inputs of a wps in general is given in the `pywps documentation <https://pywps.readthedocs.io/en/stable/api.html>`_.

WPS Output
----------

The WPS Process output will always be a string named after the output type from the table above containing the UUID **without file type**.
A single WPS might have more than one output.
An additional output called ``'error'`` is appended to each WPS Process. It contains a JSON-serialized string of the form:

.. code-block:: json

  {
    "error": True | False,
    "message": "error message if any",
    "type": "error type if any"
  }

The error-object will also be returned, if there is no error. Then, the ``error['error']==False``. The error type can be one of:

* bug - unexpected Exceptions that are not handled in the Toolbox. These should be reported to the developer
* userWarning - mainly due to wrong options passed.
* processError - expected errors that are specific to the tool. These errors need to be reported to the user.

An example of a processError would be a ``numpy.LinAlgError`` that is raised during Kriging, if the kriging matrix is bad conditioned.
This is an expected error that should be reported to the user, as it could indicate that the Kriging results are incorrect.
These kind of errors cannot be handled in the Toolbox.

Metadata file
-------------

Each tool will also write a metadata file in ``.json`` format. This metadata file contains
metadata about the initial datasets, that might be required by some tools and a collection of all tools that already were applied.
That means, for a specific tool, the corresponding ``.json`` will conain the UUIDs of other tool runs.
This way a toolchain can be traced. If you re-run a tool, new ``.json`` will be written.

The ``.json`` file will contain all information about the tool processed. This includes metadata like the tool name and the
inputs given, but also parameters like the processing time. For any input, that was the result of another tool, the UUID is
given anyway and a toolchain can be reconstructed.
If a tool loaded data into the workspace, either by loading it from the database or by producing mdelling output, the
necessary metadata will be contained in a special key ``'meta'`` or ``'entry_id'`` of the file.
This way, if a tool needs the geolocation of a dataset, but is the not the first tool in line, it can reconstruct the
toolchain until any of the other ``.json`` contains a special key.
``'meta'`` is expected to have the same structure as the metadata in the V-FOR-WaTer database, while ``'entry_id'`` can
be used to query the needed information and is thus the preferred way.

A description of the ``.json`` is shown below:

.. code-block:: json

  {
    "identifier": "Identifier of the WPS process",
    "title": "Title of the WPS process",
    "version": "Version of the WPS process",
    "inputs": [
      {
        "uuid": "UUID if the input was a tool output | optional",
        "entry_id": "ID in the database if data was loaded | optional",
        "meta" : {} # the metadata itself if the two others are not applicable
      }
    ],
    "args": {}, # any other LiteralInput, that is not in inputs
    "startUTC": "UTC timestamp when process tool was started",
    "endUTC": "UTC timestamp when process tool ended",
    "error": {} # error object as described above
  }


.. todo::

  fill the exapmple above

Example
-------

If you run a tool that produces a ``timeseries`` without error the WPS output will look similar to:

.. code-block::

  OUTPUTS['timeseries']: 'd5e98fdb-f212-42ce-8011-50e64a0a3c16'
  OUTPUTS['error']: '{"error": False, "message": "", "type": ""}'

On the drive, you will find the following files:

.. code-block::

  d5e98fdb-f212-42ce-8011-50e64a0a3c16.csv
  d5e98fdb-f212-42ce-8011-50e64a0a3c16.json

If you run a tool that produces a ``plot`` and the result ``vtimeseries`` that is visualized in the plot without error the WPS output will look similar to:

.. code-block::

  OUTPUTS['vtimeseries'] = 'ec97a39e-933c-411f-b4f5-7716871bb64f'
  OUTPUTS['plot'] = '6b23c7b6-4457-4ff2-82a3-e3c5136abfe0'
  OUTPUTS['error']: '{"error": False, "message": "", "type": ""}'

On the drive, you will find the following files:

.. code-block::

  ec97a39e-933c-411f-b4f5-7716871bb64f.csv
  ec97a39e-933c-411f-b4f5-7716871bb64f.json
  6b23c7b6-4457-4ff2-82a3-e3c5136abfe0.script.html
  6b23c7b6-4457-4ff2-82a3-e3c5136abfe0.div.html
  6b23c7b6-4457-4ff2-82a3-e3c5136abfe0.json

.. note::

  At this point the two json files essentially contain the same information, but as the ``vtimeseries`` might be further processed.

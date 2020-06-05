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

.. todo::

  Add them here, once discussed.

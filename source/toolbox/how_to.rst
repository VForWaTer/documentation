=======================
How to integrate a tool into the workspace
=======================

Collect info
=======

A list of available toolsets is available at `https://github.com/VForWaTer/curated-tools/blob/main/tool-list.txt <https://github.com/VForWaTer/curated-tools/blob/main/tool-list.txt>`_, where every toolset can contain several tools.

Collect information about tool parameters (using the example of tbr_skgstat)
===========
When you're lucky you can find a documentation (e.g. 'https://mmaelicke.github.io/scikit-gstat/reference/variogram.html#skgstat.Variogram <https://mmaelicke.github.io/scikit-gstat/reference/variogram.html#skgstat.Variogram>'_).
If not, you have to get information directly from the code (and hope for inline documentation).

**Important:** Compare the needs of the tool with the definitions from the container (see below). 


In future, all of this might be possible through the Docker tools:

The tools are bundled in Docker containers. Information is given in the respective containerized toolbox 
`https://github.com/VForWaTer/tool_geostatistics <https://github.com/VForWaTer/tool_geostatistics>`_

Accordingly, to get the parameters, do:

.. code-block:: 

    docker pull ghcr.io/vforwater/tbr_skgstat
    docker run --rm -it tbr_skgstat bash cat /srv/tool.yml
    
or **in case of problems** use toolbox runner:

.. code-block:: python

  from toolbox_runner import list_tools
  tools = list_tools() # dict with tool names as keys

  krige_tool = tools.get('kriging')  # it has to be present there...
  krige_tool.title
  krige_tool.description
  krige_toool.parameters
  
**Extremely helpful** are examples given without the overhead of installing docker or toolbox_runner. These might be found directly in the tool: `https://github.com/VForWaTer/tool_geostatistics/blob/main/tutorial.ipynb <https://github.com/VForWaTer/tool_geostatistics/blob/main/tutorial.ipynb>`_


Define Tool
=======
The given parameters can now be used to fill the tool description in a python file. For details see existing tools in 
`https://github.com/VForWaTer/vforwater-processes/tree/main/processes <https://github.com/VForWaTer/vforwater-processes/tree/main/processes>`_

How to use the tool in your ``process_run`` function is hopefully also explained in the tool example (e.g. `https://github.com/VForWaTer/tool_geostatistics/blob/main/tutorial.ipynb <https://github.com/VForWaTer/tool_geostatistics/blob/main/tutorial.ipynb>`_)

Define Output
========
Define output here ...


.. todo::

    Define the sections here

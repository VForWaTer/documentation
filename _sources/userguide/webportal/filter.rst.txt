==============
Filter in Web Portal
==============

Overview
========
The V-FOR-WaTer web portal offers data and tools for environmental sciences or any other interested. The following
describes how to make use of the V-FOR-WaTer web portal.

Find and Select Data
========

Find:
-----
One can filter data from the V-FOR-WaTer server on the start page (``Home``). One can always come back there with a
click on the V-FOR-WaTer Logo on the top left or accessed from the top bar.

Filtering is implemented inclusive, which means what you select is an requirement for the filtered data. In case of
timerange and area, not only data within is selected, but all datasets that has data within the selected range in time and space.

To filter data one can start in the ``filter menu`` on the sidebar or by drawing on the map. While the order of clicking
in the ``filter menu`` or on the map doesn't influence the result, it is advisable to start filtering in the
``filter menu``, as selecting on the map can be very restrictive; yet there is no indication which of the filter options
have results in the selected area.

To select an area, one can:

1. upload own ``GeoJSON`` files, with the ``upload`` button on top of the sidebar.

2. zoom into the map until the Merit Catchments appear. The amount of data strongly reduces the performance on the website,
   so the layer is hidden until a certain level of zoom. To us the Merit Catchments click in one, an the respective
   upstream catchments will be selected as well.

3. use the ``Draw menu``, that can be opened on top of the left sidebar, or by clicking on the ``+`` in the box in the
   upper left corner of the map. In the ``Draw menu``, one has different options to define an area:

- The ``finger`` symbol lets one click anywhere on the map. The request is processed on the server, and only a polygon
  with a strongly restricted amount of edge points is used.
- The ``square`` lets one draw a square on the map.
- The ``XXXX`` symbol lets one draw a polygon.
- The ``square with pen`` lets one manipulate an already existing contour.
- The ``shopping cart`` lets one store the actual area for later use.
- With the ``wastebin`` one can remove the current contour from the map.

Please note, as default, meta information is only shown for data the user is allowed to access (``is FAIR``). To show
other datasets as well, remove the checkmark from the respective filter option. Then one can send a request to the data
owner to get access to restricted data.

The filtered result is visible on the map (default), or in a tabular view, that can be accessed through the button
``XXXX`` above the map.


Datasets on the map are primarily visualized as points. When many points are close to each other, they are visualized as
a cluster. The number on the cluster points shows the datasets within each cluster, the size of the point also increases
with the number of datasets in the cluster. In some cases there are two datasets of the same variable on the same
location. These differ in the timerange they represent and usually have a different resolution in time.

Areal datasets are visualized with their central point. On the map, in the upper right is a box to show/hide layers.
There is also a layer to show areal data. There, the bounding boxes of the areal datasets are shown.

Select:
-----

When you click on the point in the map you see the meta information of the respective dataset. On the bottom of this list
you find several buttons. To get a preview of the dataset, to see all datasets directly related to the one you see the
preview, and one to select the dataset and bring it to the ``data store``.

Another option is to select a single dataset in the tabular view (``send to datastore``).

When less then **100** datasets are selected, there appears another button underneath the filter menu to select all the
filtered datasets.


=======
What to do with data in the data store
=======
Your selections appear as buttons on the left in the ``data store``. Besides the dataset name you find a colored
indications what datatype you selected (hovering over the indicator shows you a textual representation). A click on the
triangle on the right opens an ``context menu``. The content of the ``context menu`` depends on the datatype. In
general, from the ``context menu`` one can preview the data, see the metadata, download the dataset or remove it from
the datastore.

Main usage of the data is within the ``workspace``, that you can reach through the button in the ``top bar``.

For more read **using the workspace**.


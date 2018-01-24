---
layout: post
title: Viewing Electric Power Infrastructure with OpenStreetMap and Mapbox
---
To get a good view of power data in OpenStreetMap you need to view it with some
decent styles and free from mapping feature that are not important.

We use [Overpass Turbo](http://overpass-turbo.eu/) to export the power data
in a way that Mapbox can import it.

If you want to select everything related to power use ``["power"=""]``.  This
may return too much data - probably all the tower data.

Overpass shows the query results, but this view is only useful for debugging.

![Overpass Query Results](/assets/overpass_power_line.png)

Mapbox recommends saving the results in **GeoJSON** Save the query results

## Overpass Turbo Limitation
For larger datasets the Overpass Turbo tool has trouble.  OSM recommends using
the Overpass API this case.  However, the OverPass API doesn't return data is JSON,
but not geoJSON.  It up to another piece of code to create geoJSON.

## QGIS and OSM data
Export OSM data in XML file.  Save the file with the ``.osm`` extension.
It's best to set a custom bounding box
```
[out:xml][timeout:600];
// gather results
(
  node["power"="line"]({{bbox}});
  way["power"="line"]({{bbox}});
  relation["power"="line"]({{bbox}});
);
// print results
out body;
>;
out skel qt;
```

In QGIS:

  - Vector -> Open StreetMap -> Import Topology from XML.
  - Vector -> Export OpenStreetMap topology to SpatialLite

    - Export type: Polylines (open ways)
    - Exported tags: Load from DB
    - Tag: power, voltage, line, name, power:line

  - Select the new layer and Save as geoJSON

The geoJSON file loads right into MapBox.  It takes mapbox a while to process
the file after upload

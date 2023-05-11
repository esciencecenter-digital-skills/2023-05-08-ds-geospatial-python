![](https://i.imgur.com/iywjz8s.png)


# Collaborative Document

2023-05-09 Introduction to Geospatial Raster and Vector Data with Python.

Welcome to The Workshop Collaborative Document.

This Document is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents.

----------------------------------------------------------------------------

This is the Document for today: [link](https://tinyurl.com/2023-05-08-geospatial-python-2)

Collaborative Document day 1: [link](https://tinyurl.com/2023-05-08-geospatial-python-1)

Collaborative Document day 2: [link](https://tinyurl.com/2023-05-08-geospatial-python-2)

Collaborative Document day 3: [link](https://tinyurl.com/2023-05-08-geospatial-python-3) 


## 👮Code of Conduct

Participants are expected to follow these guidelines:
* Use welcoming and inclusive language.
* Be respectful of different viewpoints and experiences.
* Gracefully accept constructive criticism.
* Focus on what is best for the community.
* Show courtesy and respect towards other community members.
 
## 🎓 Certificate of attendance

If you attend the full workshop you can request a certificate of attendance by emailing to training@esciencecenter.nl .

## ⚖️ License

All content is publicly available under the Creative Commons Attribution License: [creativecommons.org/licenses/by/4.0/](https://creativecommons.org/licenses/by/4.0/).

## 🙋Getting help

To ask a question, raise your hand in zoom. Click on the icon labeled "Reactions" in the toolbar on the bottom center of your screen,
then click the button 'Raise Hand ✋'. For urgent questions, just unmute and speak up!

You can also ask questions or type 'I need help' in the chat window and helpers will try to help you.
Please note it is not necessary to monitor the chat - the helpers will make sure that relevant questions are addressed in a plenary way.
(By the way, off-topic questions will still be answered in the chat)


## 🖥 Workshop website

[link](https://esciencecenter-digital-skills.github.io/2023-05-08-ds-geospatial-python/)

## 🛠 Setup

🐍 Python & Environment:

[link](https://carpentries-incubator.github.io/geospatial-python/setup.html) 

📚 Download files:
- [brpgewaspercelen_definitief_2020_small.gpkg](https://figshare.com/ndownloader/files/37729413)
- [brogmwvolledigeset.zip](https://figshare.com/ndownloader/files/37729416)
- [status_vaarweg.zip](https://figshare.com/ndownloader/files/37729419)

Satellite imagery search results: [search.json](https://raw.githubusercontent.com/esciencecenter-digital-skills/2023-05-08-ds-geospatial-python/main/files/search.json)


## 👩‍🏫👩‍💻🎓 Instructors

Francesco Nattino, Ou Ku

## 🧑‍🙋 Helpers

Johan Hidding, Pranav Chandramouli  


## 🗓️ Agenda

**Day 1**
 
| Time  | Topic                                         |
| ----- | --------------------------------------------- |
| 09:00 | Welcome and icebreaker, setup check           |
| 09:30 | Introduction to raster, vector, and CRS       |
| 10:00 | Access satellite imagery using Python         |
| 10:15 | *Coffee break*                                |
| 10:30 | Access satellite imagery using Python         |
| 11:30 | *Coffee break*                                |
| 11:45 | Read and visualize raster data                |
| 12:45 | Wrap-up                                       |
| 13:00 | END                                           |
 
**Day 2**

| Time  | Topic                                         |
| ----- | --------------------------------------------- |
| 09:00 | Welcome and icebreaker                        |
| 09:15 | Read and visualize raster data                |
| 10:15 | *Coffee break*                                |
| 10:30 | Vector data in Python                         |
| 11:30 | *Coffee break*                                |
| 11:45 | Crop raster data with rioxarray and geopandas |
| 12:45 | Wrap-up                                       |
| 13:00 | END                                           |

**Day 3**

| Time  | Topic                                         |
| ----- | --------------------------------------------- |
| 09:00 | Welcome and icebreaker                        |
| 09:15 | Raster Calculations in Python                 |
| 10:15 | *Coffee break*                                |
| 10:30 | Calculating Zonal Statistics on Rasters       |
| 11:30 | *Coffee break*                                |
| 11:45 | Parallel raster computations using Dask       |
| 12:45 | Wrap-up                                       |
| 13:00 | END                                           |


## 🔧 Exercises

## 🧠 Collaborative Notes
Remember, yesterday we saved a query to `json`. We then loaded the raster data of the first matched object as follows:

```python=
import pystac
items = pystac.ItemCollection.from_file("search.json")
nir09_href = items[0].assets["nir09"].href
import rioxarray
raster = rioxarray.open_rasterio(nir09_href)
```

## Coordinates (CRS)
We can look at the coordinate reference system that our data is in:

```python=
print(raster.rio.crs)
```

This displays a code of an international CRS reference identifier (EPSG in this case). We need to use another library to project our data to other coordinate systems: `pyproj`. This is a front-end for PROJ4, a well known and much used C++ library.

```python=
print(type(raster.rio.crs))

import pyproj

epsg_code = raster.rio.crs.to_epsg()
crs = pyproj.CRS(epsg_code)

print(type(crs))
```

Now we have an object that PyProj will understand.


#### Exercise: find the axes units of the CRS

What units are our coordinates in? See if you can find a method to examine this information using `help(crs)` or `dir(crs)`

When we run `dir(crs)` we see a lot of methods of the `CRS` class, but also many `__class__` like attributes. These are so called *magic* methods and tell Python how a lot of standard operations should be performed on this object; think of: creating an object, comparison with `==`, conversion to strings, you name it. The `help(crs)` command shows more documentation. This is usually the same documentation as is normally listed in [API documentation](https://pyproj4.github.io/) online. See: [CRS](https://pyproj4.github.io/pyproj/stable/api/crs/crs.html).

We can either run `crs.axis_info`, `crs.coordinate_system` or just show `crs` in Jupyter.

### Continuing

```python=
raster_reproject = raster.rio.reproject(4326)
raster_reproject.plot(robust=True)
```

You can lookup any CRS in [epsg.io](epsg.io). This is WGS84.

### Missing values

We may suppose that the `0` values at the edges of our data are actually missing data. It would be better to actually label these values as `missing`. Then subsequent analysis can deal with those in a more proper way.

```python=
raster.rio.nodata
```

This tells us that `0` is actually the code for missing data!

Numpy gives us access to `NaN` values that we can use to mark missing data.

```python=
import numpy as np
np.nan
```

```python=
raster_masked = rioxarray.open_rasterio(nir09_href, masked=True)
```

Now the zeros in our data have been replaced with `NaN`.

We can now do better statistics on the raster data:

```python=
raster_masked.min()
raster_masked.max()
raster_masked.mean()
# ... etc
```

Why is this info in `xarray` format? We can use these functions to run the operations along (one or more) specified axes. Then the return value is actually an array.

```python=
raster_masked.max(dim=("x",))
```

## Multiband rasters

```python=
visual_href = items[0].assets["visual"].href
visual = rioxarray.open_rasterio(visual_href, masked=True)
visual
```

Downloading this data, for example by calling `visual.plot()` is possible but will take (a lot of) time.

```python=
visual_overview_0 = rioxarray.open_rasterio(visual_href, overview_level=0)
visual_overview_0
```

This is a down-sampled version by a factor of 2, so only a quarter of the values.

```python=
visual_overview_1 = rioxarray.open_rasterio(visual_href, overview_level=1)
visual_overview_1
```

Now we get four-times smaller resolution: only one sixteenth of the amount of data.

When we try to plot an `overview_level=2` image, we only get a histogram. That's a bit disappointing.

```python=
visual_overview_2.rio.reproject(4326).plot.imshow(robust=True, figsize=(12, 12))
```

That's better!

## Vector data

```python=
import geopandas as gpd

fields = gpd.read_file("data/brpgewaspercelen_definitief_2020_small.gpkg")
fields.plot(figsize=(12, 16))
```

A good method to work with paths in Python:

```python=
from pathlib import Path

data_path = Path("../data")  # your path to your data
fields = gpd.read_file(data_path / "brpgewaspercelen_definitief_2020_small.gpkg")
fields
```

We get a table overview that you might be used to from Pandas.

```python=
fields["category"]   # or alt: fields.category
```

```python=
fields[fields["category"] == "Grasland"]
```

We can look at only the metadata, or the first 10 rows, to save time

```python=
fields_1row = gpd.read_file(data_path / "brpgewaspercelen_definitief_2020_small.gpkg", rows=1)
fields_10rows = gpd.read_file(data_path / "brpgewaspercelen_definitief_2020_small.gpkg", rows=10)
```

```python=
fields_1row.crs
fields.total_bounds
```

We can specify a bounding box at loading the data.

```python=
xmin, xmax = (120_000, 130_000)  # use underscores to make numbers nicer!
ymin, ymax = (490_000, 500_000)
fields_bbox = gpd.read_file(
    data_path / "brpgewaspercelen_definitief_2020_small.gpkg",
    bbox=(xmin, ymin, xmax, ymax))
```

You can retrieve the bounds of this file without actually reading any values. `fiona` is a lower level library that `geopandas` actually uses to read data.

```python=
import fiona

with fiona.open(data_path / "brpgewaspercelen_definitief_2020_small.gpkg") as f:
    bounds = f.bounds
    crs = f.crs
```

See the [Fiona documentation](https://fiona.readthedocs.io/en/stable/).

### Spatial query
We can use the `cx` indexer to find objects within a given bounding box.

```python=
xmin, xmax, ymin, ymax = (120_000, 135_000, 485_000, 500_000)
fields_cx = fields.cx[xmin:xmax, ymin:ymax]
fields_cx.plot()
```

We may save our selection to a file.

```python=
fields_cx.to_file(data_path / "fields_cropped.shp")
```

Other file formats are also supported: `.gpkg` for instance. (see `fiona.supported_drivers` to get a list)

```python=
wells = gpd.read_file(data_path / "brogmwvolledigeset.zip")
wells.plot(markersize=0.1)
```

We can select only the wells that are covered by the `fields` dataset.

```python=
wells_clip = wells.clip(fields_cx)
```

This gives us an error: these datasets don't have the same coordinate system. We first need to convert the wells to RD coordinates.

```python=
wells_clip = wells.to_crs(fields_cx.crs).clip(fields_cx)
```

Let's combine the two data sets in one plot.

```python=
from matplotlib import pyplot as plt

# create empty canvas with a single plot inside
fig, ax = plt.subplots()
fields_cx.plot(ax=ax)
wells_clip.plot(ax=ax, color='r')
```

These are only the wells that are exactly inside a field. Many will be just outside a field. We use *buffer areas* to select a radius around the fields.

```python=
buffer = fields_cx.buffer(50)
buffer.plot()
```

```python=
wells_clip_buffer = wells.clip(buffer)
fig, ax = plt.subplots(figsize=(12, 12))
fields_cx.plot(ax=ax)
wells_clip.plot(ax=ax, color='r')
```

For multiple subplots:

```python=
fig, axes = plt.subplots(2,1)

for ax, geom in zip(axes, [fields_cx, wells_clip_buffer]):
    geom.plot(ax=ax)
```

Buffer when used on a geodata frame returns a geoseries which cannot be acted upon by the dissolve function. The dissolve function works only on a geodata frame.

```python=
fields_buffer = fields_cx.copy()
fields_buffer["geometry"] = buffer
```

Now we have the same data frame as before, but the geometries are those of the buffer. We can create a single polygon from the buffer.

```python=
fields_buffer_dissolve = fields_buffer.dissolve()
```

```python=
fields_buffer_dissolve["geometry"][0]
```

Now we have a ShapeLy object. We can call `explode()` to get all connected components.

```python
fields_buffer_dissolve.explode()
```

We can clip again, this should be faster.

```python=
wells_clip_buffer_dissolved = wells.clip(fields_buffer_dissolve)
wells_clip_buffer_dissolved.plot()
```

#### Exercise: clip fields within 500m from the wells
This time, we will do a selection the other way around. Can you clip `fields` with the 500m buffer of `wells`? Please visualize the results.

If you would like to load the two datasets again, use:

```python
fields = gpd.read_file("fields_cropped.shp")
wells = gpd.read_file("data/brogmwvolledigeset.zip")
```

- Hint 1: The `wells` object contains all the wells in the Netherlands. It might be too big to call `.buffer()` function directly on it. To improve the performance, first crop it with the bounding box of the fields.

- Hint 2: If you load the data again, do not forget the CRS conversion. The file `brogmwvolledigeset.zip` is in CRS 4326.


## 📚 Resources

- Plotting basemap together with data:
    * [folium](https://python-visualization.github.io/folium/index.html): 
        * [examples](https://python-visualization.github.io/folium/quickstart.html) of the nice things you can do with it
        * Specific [examples](https://stacspec.org/en/tutorials/access-sentinel-2-data-aws/) using Sentinel-2 data from STAC catalog
    * [leaflet](https://leafletjs.com/index.html) (JavaScript library for maps, internally used by folium)
- Working with higher dimensional data (e.g. time, bands):
    * [Xarray tutorials](https://tutorial.xarray.dev/intro.html) (reading/writing files, working with labeled data, computations, plotting, etc.)

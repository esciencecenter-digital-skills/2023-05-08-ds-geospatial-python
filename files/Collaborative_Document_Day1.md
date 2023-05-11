![](https://i.imgur.com/iywjz8s.png)


# Collaborative Document

2023-05-08 Introduction to Geospatial Raster and Vector Data with Python.

Welcome to The Workshop Collaborative Document.

This Document is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents.

----------------------------------------------------------------------------

This is the Document for today: [link](https://tinyurl.com/2023-05-08-geospatial-python-1)

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
| 09:00 | Welcome and icebreaker                       |
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

Is the environment setup working on your computer?


```sh
conda activate -n geospatial
jupyter lab
```

```python
import rioxarray
```

- Introduction on raster data and data formats

Python libraries:

- STAC: `pystac-client` and `pystac`
- Raster data: `rioxarray` which is `xarray` with `rasterio` (based on GDAL)
- Tabular data: `geopandas`: all of `pandas` with geospatial querying

### Accessing Satellite data

Sentinel satellite, data access through [Copernicus](https://dataspace.copernicus.eu). Uses web-interface, lots of clicking, not reproducible, slow. We can also use an Application Programming Interface (API). Copernicus supports four protocols: OData, STAC, OpenSearch and Sentinel Hub. STAC (SpatioTemporal Asset Catalogs) is a common standard for geospatial data interchange. STAC is documented at [stacspec.org](https://stacspec.org). A list of publicly available data sets is available at [stacindex.org](https://stacindex.org).


### Exercise: Discover a STAC catalog

Let’s take a moment to explore the Earth Search STAC catalog, which is the catalog indexing the Sentinel-2 collection that is hosted on AWS. We can interactively browse this catalog using the STAC browser at [this link](https://radiantearth.github.io/stac-browser/#/external/earth-search.aws.element84.com/v1?.language=en).

1. Open the link in your web browser. Which (sub-)catalogs are available?
2. Open the "Sentinel-2 Level 2A" collection, and select one item from the list. Each item corresponds to a satellite "scene", i.e. a portion of the footage recorded by the satellite at a given time. Have a look at the metadata fields and the list of assets. What kind of data do the assets represent?

 Q1 subcatalogs of aerial, satellite and radar raster data - DEM, DSM but also spectral & multispectral imagery
 Q2 the bifferent bands and collected information of the Satellite

Q : it is not clear to me what the data represents, you have to click a dataset to see what it is?

Q : do all the assets belong to one image (multibands in the same image at the same time)?
    - What do you mean when you say "image"? 
    - when I download image from a website, e.g. Landsat, when I choose time and area, there is one image, but includes multibands.
    - The image you see is made from three bands, rendered as red green and blue, but there are many more bands.
    - so all assests are from same time?
    - yes
    - I see, Thanks

Q How can I access older images? 


Q, Is there any trick to install all the geospatial-python packages in the Google Colab? I don't want to install all manually.
    - In Collab this is not possible, but you might like [binder](mybinder.org). You put your notebook in a Github repo, then with a single URL you can have this notebook running.

## Lecture resumed

:::info
Jupyter short-cuts:
- press Escape to go into Cell mode, Enter to go to Edit mode
- In cell mode, use "M" to change a cell to Markdown, "Y" to set it back to a code cell.
- In cell mode, "A" to add cell above, "B" below current cell.
:::

```python=
api_url = "https://earth-search.aws.element84.com/v1/"
```

```python=
from pystac_client import Client
client = Client.open(api_url)
client
```

Inspect the `client` object.

```python=
for collection in client.get_collections():
    print(collection)
```

We choose our collection:

```python=
collection="sentinel-2-l2a"
```

To handle geometric information we use `shapely`.

```python=
import shapely
point = shapely.Point(4.89, 52.37) # longitude, lattitude of Amsterdam: https://maps.google.com/?q=52.37,4.89
point
```

Now we can search around this point.

```python=
search = client.search(
    collections=[collection],
    intersects=point,
    max_items=10,
)
```

How many scenes matched?

```python=
search.matched()
```

Retrieve *metadata* for all of them

```python=
items = search.get_all_items()
```

This returns a custom STAC specific collection of items ([iterable](https://docs.python.org/3/glossary.html#term-iterable))

```python=
for item in items:
    print(item)
```

We can index like any normal list. Each item has a datetime, geometry and properties associated

```python
item = items[0]
```

```python
item.datetime
item.geometry

for k, v in item.properties.items():
    print(f"{k:>50}: {v}")
```

More advanced search:

```python=
bbox = point.buffer(0.01).bounds    # bounding box of circle of 0.01 (deg) around point
datetime = "2020-03-20/2020-03-30"  # special syntax for date range
query = "eo:cloud_cover<15"         # special query syntax, check out the "eo:cloud_cover" property in item

search = client.search(
    collections=[collection],
    bbox=bbox,
    datetime=datetime,
    query=[query])
```

Good question: howto create more involved queries? Look at [pystac documentation](pystac-client.readthedocs.io). A deep search turns out that "OR" is not possible with the query API; there is also a `filter` argument that has more advanced possibilities using CQL2 syntax.

You can always save the result of a query.

```python
items = search.get_all_items()
items.save_object("search.json")
```

### Access assets

```python=
item = items[0]
item.assets
```

We see a dictionary with links to the actual data in `Asset` objects. You can list all available asset names using

```python
item.assets.keys()
```

```python
assets = item.assets
for key, asset in assets.items():
    print(key, ":", asset.title)
    # alternatively: print(f"{key:>20}: {asset.title}")
```

To look at an image:

```python
assets["thumbnail"].href
```

And follow the link.

:::spoiler Use `rioxarray` to download raster data
We can use `rioxarray` to open the remote raster files.

```python=
import rioxarray
coastal = rioxarray.open_rasterio(assets["coastal"].href)
```
:::


#### Exercise: Search Landsat 8 scenes on the Microsoft Planetary Computer
In this exercise we put in practice all the skills we have learned in this episode to search for images from [the Landsat program](https://landsat.gsfc.nasa.gov). In particular, we look for scenes from the [Landsat 8 mission](https://www.usgs.gov/landsat-missions/landsat-8), as hosted on the [Microsoft Planetary Computer](https://planetarycomputer.microsoft.com/dataset/landsat-c2-l2). This collection can be accessed from the Planetary Computer STAC API endpoint at the following URL: https://planetarycomputer.microsoft.com/api/stac/v1

- Using `pystac_client`, search for all assets of the Landsat 8 collection (`landsat-8-c2-l2`) from February to March 2021, intersecting the point with longitude/latitute coordinates (-73.97, 40.78) deg, and with a cloud cover percentage smaller than 10.
- Visualize the item’s thumbnail (asset key `rendered_preview`).

```python=
pc_stac_url = "https://planetarycomputer.microsoft.com/api/stac/v1"
pc_client = Client.open(pc_stac_url)
for collection in pc_client.get_collections():
    print(collection)  # Lots available!
```

```python=
search = pc_client.search(
    collections=["landsat-8-c2-l2"],
    datetime="2021-02-01/2021-03-30",
    intersects=shapely.Point(-73.97, 40.78),
    query=["eo:cloud_cover<10"])
items = search.get_all_items()
```

```python
search.matched()  # gives error
```

```python
items[0].assets["rendered_preview"].href
```

Not all datasets are publicly available. You need to setup authentication in those cases.


## Raster datasets
We retrieve the search results from `search.json` that we saved in the previous episode.

```python=
import pystac
items = pystac.ItemCollection.from_file("search.json")
nir09_href = items[0].assets["nir09"].href
import rioxarray
raster = rioxarray.open_rasterio(nir09_href)
```

A lot of information is accessible from `raster.rio`

```python
raster.rio.crs        # coordinate reference system
raster.rio.nodata     # any missing data?
raster.rio.bounds()   # units dependent on CRS
```

```python
raster.values         # ndarray
```

After reading the values, summary information about `raster` changed: data is accessed [**lazily**](https://en.wikipedia.org/wiki/Lazy_evaluation).

To visualize, the easiest way is to call `plot` method.

```python
raster.plot()
```

Note that x/y axes are not yet transformed to other coordinate system. How can we improve the colors of this image?

```python
raster.plot(robust=True)
```

This shows colors only from 2-98 percentiles. So, for the purpose of visualisation, the lowest and highest values are dropped if they are rare. If you get small images, try to add `figsize=(12,12)` to the arguments.

## 📚 Resources


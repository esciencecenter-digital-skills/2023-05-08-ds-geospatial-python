![](https://i.imgur.com/iywjz8s.png)


# Collaborative Document

2023-05-10 Introduction to Geospatial Raster and Vector Data with Python.

Welcome to The Workshop Collaborative Document.

This Document is synchronized as you type, so that everyone viewing this page sees the same text. This allows you to collaborate seamlessly on documents.

----------------------------------------------------------------------------

This is the Document for today: [link](https://tinyurl.com/2023-05-08-geospatial-python-3)

Collaborative Document day 1: [link](https://tinyurl.com/2023-05-08-geospatial-python-1)

Collaborative Document day 2: [link](https://tinyurl.com/2023-05-08-geospatial-python-2)

Collaborative Document day 3: [link](https://tinyurl.com/2023-05-08-geospatial-python-3) 

## Zoom link

[link](https://us02web.zoom.us/j/83698284199?pwd=cHJjdUNhenlPcDVIVzhleFprRE5Ydz09)

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

Cropped fields polygons: [fields_cropped.zip](https://raw.githubusercontent.com/esciencecenter-digital-skills/2023-05-08-ds-geospatial-python/main/files/fields_cropped.zip)

NDVI raster output file: [NDVI.tif](https://raw.githubusercontent.com/esciencecenter-digital-skills/2023-05-08-ds-geospatial-python/main/files/NDVI.tif)

## 👩‍🏫👩‍💻🎓 Instructors

Francesco Nattino, Ou Ku

## 🧑‍🙋 Helpers

Sander van Rijn, Pranav Chandramouli  


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


## 🧠 Collaborative Notes

### (continue from yesterday) Vector data in Python

#### Exercise: clip fields within 500m from the wells
This time, we will do a selection the other way around. Can you clip `fields` with the 500m buffer of `wells`? Please visualize the results.

If you would like to load the two datasets again, use:

```python
import geopandas as gpd
fields_cx = gpd.read_file("fields_cropped.shp")
wells = gpd.read_file("data/brogmwvolledigeset.zip")
```

- Hint 1: The `wells` object contains all the wells in the Netherlands. It might be too big to call `.buffer()` function directly on it. To improve the performance, first crop it with the bounding box of the fields.

- Hint 2: If you load the data again, do not forget the CRS conversion. The file `brogmwvolledigeset.zip` is in CRS 4326.


```python
# align crs
wells = wells.to_crs(fields_cx.crs)

# crop wells to extent of the fields
xmin, ymin, xmax, ymax = fields_cx.total_bounds
wells_cx = wells.cx[xmin-500:xmax+500, ymin-500:ymax+500]
# Note that we added a margin of +/-500 to make sure we don't exclude
# any wells of which only the buffer is inside the fields extent 

# create buffer
buffer_wells_cx = wells_cx.buffer(500)

# clip fields
fields_wells_cx_buffer_clip = fields_cx.clip(buffer_wells_cx)
fields_wells_cx_buffer_clip.plot()
```

This has cropped the fields to the shape of the buffer. What if we still want the whole fields?

```python
fields_cx.sjoin(buffer_wells_cx)  # Error! buffer_wells_cx is not a dataframe

# Make an explicit dataframe for use with sjoin
gdf_buffer_wells_cx = wells_cx.copy()
gdf_buffer_wells_cx['geometry'] = buffer_wells_cx

fields_cx_wells_buffer_cx = fields_cx.sjoin(gdf_buffer_wells_cx)  # no error! 🎉

# Note: All fields matching multiple well buffers are duplicated!
fields_cx_wells_buffer_cx.shape
# (11420, 46)  

# de-duplicate fields
idx = fields_cx_wells_buffer_cx.index.unique()
fields_in_buffer = fields_cx.loc[idx]
fields_in_buffer.plot()
```

### Crop raster data with rioxarray and geopandas

```python
import pystac
import rioxarray
items = pystac.ItemCollection.from_file('search.json')

item = items[1]  # 2nd item
item.assets.keys()

# lazily loads the data
raster = rioxarray.open_rasterio(item.assets['visual'].href)

# import gpd and load field_cx again if needed
import geopandas as gpd
fields_cx = gpd.read_file("fields_cropped.shp")

# raster and fields_cx are in different crs
raster.rio.crs
# > EPSG 32631
fields_cx.crs
# > EPSG 28992

fields_cx = fields_cx.to_crs(raster.rio.crs)

# clip_box makes good use of the lazy loading to limit data size
# directly unpack fields_cx.total_bounds into clip_box
raster_clip_box = raster.rio.clip_box(*fields_cx.total_bounds)
raster_clip_box.plot.imshow(figsize=(4,4))  # force square view with figsize

raster_clip = raster_clip_box.rio.clip(fields_cx)  # Error! Does not automatically understand geometry
raster_clip = raster_clip_box.rio.clip(fields_cx['geometry'])  # Explicit geometry
raster_clip.plot.imshow(figsize=(4,4))
```

**Q: So clip box is less of a performance hit than clipping by elements?**
A: clip_box prevents data from being loaded in the first place

**Q: Do the raster values outside the polygons get assigned a NaN or something else?**
A: everything outside the polygons is given the `nodata` value
```python
raster_clip.rio.nodata
# 0
```

**Q: I'm trying to restrict files to a certain bounding box, but I don't know in advance if there is _any_ data within that box for each file. If I try to clip when no data is present, I get a NoDataInBounds error, how do I deal with this?**
A: `NoDataInBounds` is a specific rasterio exception, so you can check for it in your code using a try-except block:

```python
from rasterio.exceptions import NoDataInBounds

valid_files = []
for file in files:
    ... # load file
    try:
        clipped_file = ...  # clip loaded file with bounding box
    except NoDataInBounds:  # if the NoDataInBounds error occurs ...
        continue            # ... then skip the rest of the code in this loop
    valid_files.append(clipped_file)  # i.e., this gets skipped if the error happens

# valid_files now holds all files for which NoDataInBounds did not occur
```

### Raster calculations

```python
import pystac
items = pystac.ItemCollection.from_file("search.json")
item = items[1]  # 2nd item

# get data for red and near-infrared (nir) bands
red_href = item.assets["red"].href
nir_href = item.assets["nir"].href

import rioxarray
# lazy loading
red = rioxarray.open_rasterio(red_href, masked=True)
nir = rioxarray.open_rasterio(nir_href, masked=True)

red
# xarray.DataArray  band: 1  y: 10980  x: 10980
# [120560400 values with dtype=float32]
# Coordinates:
# band (band) int64    1
# x    (x)    float64  6e+05 6e+05 ... 7.098e+05 7.098e+05
# y    (y)    float64  5.9e+06 5.9e+06 ... 5.79e+06
# ...
```

**Q: Which values represent the red/nir reflectance, the 'x' or 'y' array?**
A: The 'x' and 'y' are the _coordinates_ of this array, the data are not yet loaded


```python
bbox = (629_000, 5_804_000, 639_000, 5_814_000)

# still lazily loaded, clip what we refer to
red_clip = red.rio.clip_box(*bbox)
nir_clip = nir.rio.clip_box(*bbox)

# Fields are dark: green plants absorb red
red_clip.plot(robust=True)
# fields are light: green plants reflect near-infrared
nir_clip.plot(robust=True)

# with 'reproject_match' we can ensure the rasters match so we can do calculations
red_clip_projected = red_clip.rio.reproject_match(nir_clip)
# but not technically needed since our red and nir data comes from the same source
```

Using the red and near-infrared reflectance, we can calculate the Normalized Difference Vegetation Index (NDVI)

```python
ndvi = (nir_clip - red_clip) / (nir_clip + red_clip)
ndvi.plot()  # no need for robust, all values are between -1 and 1

# a histogram of the different values
ndvi.plot.hist()
```

#### Exercise: Explore NDVI Raster Values

It’s often a good idea to explore the range of values in a raster dataset just like we might explore a dataset that we collected in the field. The histogram we just made is a good start but there’s more we can do to improve our understanding of the data.
1. What is the min and maximum value for the NDVI raster (`ndvi`) that we just created? Are there missing values?
2. Plot a histogram with 50 bins instead of 8. What do you notice that wasn’t clear before?
3. Plot the `ndvi` raster using breaks that make sense for the data.

**Hints:**
- for plotting the histogram with different bins, the documentation `ndvi.plot.hist?` also refers to the documentation of `matplotlib.pyplot.hist`
- missing data in this case are `NaN`s

**Q: why does `print(ndvi.rio.nodata)` show `None` instead of `NaN`?**
A: `rio.nodata` shows which 'regular' value should be interpreted as missing value, such as 0 in the previous example. `NaN` is explicitly a missing value by definition, so `rio.nodata` does not show it.

#### Solutions

```python
# 1.
ndvi.min()
ndvi.max()
ndvi.isnull.sum()  # .isnull() performs the check for NaN, i.e. missing, values

# 2.
ndvi.plot.hist(bins=50)
# observe e.g. that most values are above -0.5 and below 0.9

# 3.
class_bins = (-1, 0, 0.2, 0.7, 1)
ndvi.plot(levels=class_bins)
```

How to get rid of missing values by interpolation?
```python
# xarray supports 1D interpolation out-of-the-box
ndvi_interpolated = ndvi.interpolate_na(dim='x')  # can also choose any other dimension 
ndvi_interpolated.isnull().sum()
# 0  # all NaN values have been replaced

# Can finally save the output to a new .tif file
ndvi_interpolated.rio.to_raster("NDVI.tif")
```

### Zonal statistics

```python
import geopandas as gpd
fields_cx = gpd.read_file("fields_cropped.shp")

fields_cx['gewascode'].unique()
# fields_cx contains a set of different crop codes

import rioxarray
ndvi = rioxarray.open_rasterio("NDVI.tif")  # lazily loaded from file
```

We can calculate certain statistics for a certain zone, e.g. gewascode 265, using logical indexing:
```python
fields_cx[fields_cx["gewascode"]==265]
```

But using this is slow if repeated for many zones. There's a better way:

```python
# convert crs to prepare for zone statistics on ndvi
fields_cx = fields_cx.to_crs(ndvi.rio.crs)

# create a list like [[geom1, code1], [geom2, code2], [...], ...]
geom_list = fields_cx[['geometry', 'gewascode']].values.tolist()

# ndvi was loaded as multi-band data with 1 band, resulting in a dimension of size 1:
print(ndvi.shape)
# (1, 1000, 1000)
# to rasterize, we need to have a 2D shape, so get use squeeze:
ndvi_squeezed = ndvi.squeeze()
print(ndvi_squeezed.shape)
# (1000, 1000)

# rasterize the fields polygons to match the ndvi data
from rasterio import features
fields_rasterized = features.rasterize(
    geom_list,
    out_shape=ndvi_squeezed.shape,
    transform=ndvi.rio.transform()
)

# Now we can make an xarray object of the crop codes from the rasterized fields
fields_rasterized_xarr = ndvi_squeezed.copy()
fields_rasterized_xarr.data = fields_rasterized

# now we can easily calculate all statistics in one go
from xrspatial import zonal_stats
results_stats = zonal_stats(fields_rasterized_xarr, ndvi_squeezed)
```

## Tips

- If you publish the code before ahead. then we will test it before
- I would like to see more exercises. I think a lot of concepts are valuable but having some (more) practical experience could be good. 
- The scrolling up and down went a bit too fast today, so it was hard to follow some times -- especially with so similar variable names :slightly_smiling_face:  :+1: 
-

## Tops
+ Clear explanations, relevant techniques, good examples, good pace
+ Very useful information, both from the workshop content and side information
+ Good build-up process of the knowledge and tools
+

## 📚 Resources

- [Post-workshop survey](https://www.surveymonkey.com/r/8TJMM8N)
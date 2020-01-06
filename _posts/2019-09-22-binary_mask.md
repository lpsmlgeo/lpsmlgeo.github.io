---
layout: post
title: Converting Shapefiles into a Binary Raster
subtitle: 
tags: [AI, Python, rms, Remote Sensing, Binary Mask, Tutorial]
---

This tutorial shows how to generate a binary raster file, broadly used in semantic segmentation problems, with python.

![](/img/binary_mask.gif)

### Workflow


- [0. Import the libraries](#0-load-the-libraries) 

- [1. load .tif image file](#1-load-tif-image-file)

- [2. Load Shapefile or GeoJson](#2-load-shapefile-or-geojson)

- [3. Check if the Coordinate Reference System (CRS) are the same](#3-verify-the-coordinate-reference-system-crs)

- [4. Generate Binary Mask](#4-generate-the-binary-mask)

- [5. Save](#5-save)

- [6. Defining a Function that Generates Binary Masks](#6-defining-a-function-that-generates-binary-masks)

&nbsp;

#### 0. Load the libraries


``` python

import os

import rasterio
from rasterio.plot import reshape_as_image
import rasterio.mask
from rasterio.features import rasterize

import pandas as pd
import geopandas as gpd
from shapely.geometry import mapping, Point, Polygon
from shapely.ops import cascaded_union

import numpy as np
import cv2
import matplotlib.pyplot as plt

```
&nbsp;

#### 1. load .tif image file

Define the raster_path variable with the path to the .tif image. The binary raster will have the same dimensions as this imagem.

``` python

raster_path = "/Users/...../imagem.tif"
with rasterio.open(raster_path, "r") as src:
    raster_img = src.read()
    raster_meta = src.meta

```

&nbsp;


#### 2. Load Shapefile or GeoJson


Define the shape_path with the path to the shapefile or the geojson file.


``` python

shape_path = "/Users/.../poligono.geojson"
train_df = gpd.read_file(shape_path)

```

<br/><br/>


#### 3.Verify the Coordinate Reference System (CRS)


The CRS of the image and the shapefile must be the same. If they aren`t, use QGIS to convert both files to the same CRS.

```python
 
 print("CRS Raster: {}, CRS Vector {}".format(train_df.crs, src.crs))

```

&nbsp;


#### 4. Generate the binary mask

The following code genates the binary mask and plot it.

```python
#Generate polygon
def poly_from_utm(polygon, transform):
    poly_pts = []
    
    poly = cascaded_union(polygon)
    for i in np.array(poly.exterior.coords):
        
        # Convert polygons to the image CRS
        poly_pts.append(~transform * tuple(i))
        
    # Generate a polygon object
    new_poly = Polygon(poly_pts)
    return new_poly

# Generate Binary maks

poly_shp = []
im_size = (src.meta['height'], src.meta['width'])
for num, row in train_df.iterrows():
    if row['geometry'].geom_type == 'Polygon':
        poly = poly_from_utm(row['geometry'], src.meta['transform'])
        poly_shp.append(poly)
    else:
        for p in row['geometry']:
            poly = poly_from_utm(p, src.meta['transform'])
            poly_shp.append(poly)

mask = rasterize(shapes=poly_shp,
                 out_shape=im_size)

# Plot the mask

plt.figure(figsize=(15,15))
plt.imshow(mask)

```
&nbsp;

#### 5. Save

```python
mask = mask.astype("uint16")
save_path = "/Users/.../mascaras/train.tif"
bin_mask_meta = src.meta.copy()
bin_mask_meta.update({'count': 1})
with rasterio.open(save_path, 'w', **bin_mask_meta) as dst:
    dst.write(mask * 255, 1)

```
&nbsp;

#### 6. Defining a Function that Generates Binary Masks


The function generate_mask have the following parameters:

**raster_path** = path to the .tif;

**shape_path** = path to the shapefile or GeoJson;

**output_path** = Path to save the binary mask.

**file_name** = Name of the file.


```python

def generate_mask(raster_path, shape_path, output_path, file_name):
    
    """Function that generates a binary mask from a vector file (shp or geojson)
    
    raster_path = path to the .tif;

    shape_path = path to the shapefile or GeoJson.

    output_path = Path to save the binary mask.

    file_name = Name of the file.
    
    """
    
    #load raster
    
    with rasterio.open(raster_path, "r") as src:
        raster_img = src.read()
        raster_meta = src.meta
    
    #load o shapefile ou GeoJson
    train_df = gpd.read_file(shape_path)
    
    #Verify crs
    if train_df.crs != src.crs:
        print(" Raster crs : {}, Vector crs : {}.\n Convert vector and raster to the same CRS.".format(src.crs,train_df.crs))
        
        
    #Function that generates the mask
    def poly_from_utm(polygon, transform):
        poly_pts = []

        poly = cascaded_union(polygon)
        for i in np.array(poly.exterior.coords):

            poly_pts.append(~transform * tuple(i))

        new_poly = Polygon(poly_pts)
        return new_poly
    
    
    poly_shp = []
    im_size = (src.meta['height'], src.meta['width'])
    for num, row in train_df.iterrows():
        if row['geometry'].geom_type == 'Polygon':
            poly = poly_from_utm(row['geometry'], src.meta['transform'])
            poly_shp.append(poly)
        else:
            for p in row['geometry']:
                poly = poly_from_utm(p, src.meta['transform'])
                poly_shp.append(poly)

    mask = rasterize(shapes=poly_shp,
                     out_shape=im_size)
    
    #Salve
    mask = mask.astype("uint16")
    
    bin_mask_meta = src.meta.copy()
    bin_mask_meta.update({'count': 1})
    os.chdir(output_path)
    with rasterio.open(file_name, 'w', **bin_mask_meta) as dst:
        dst.write(mask * 255, 1)


```

&nbsp;

#### References

- <https://medium.com/datadriveninvestor/preparing-aerial-imagery-for-crop-classification-ce05d3601c68>

- <https://rasterio.readthedocs.io/en/stable/api/rasterio.mask.html>

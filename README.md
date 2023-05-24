# Index_Calc
# Python Code for Calculate BAI,NDVI,MSAVI,NBR

This code is for calculate several vegetation indices, such as BAI (Burn Area Index), NBR (Normalized Burn Ratio), NDVI (Normalized Difference Vegetation Index), and MSAVI (Modified Soil-Adjusted Vegetation Index), for a Landsat 8 satellite image in Python

## Prerequisites

Before running this code in your system you have to install `numpy` and `rasterio` library in your system. To install the `numpy` library follow this [link](https://numpy.org/install/). 

Geographic information systems use GeoTIFF and other formats to organize and store gridded raster datasets such as satellite imagery and terrain models. `rasterio` reads and writes these formats and provides a Python API based on `Numpy` N-dimensional arrays and `GeoJSON`. To install the `rasterio`, [clik here.](https://rasterio.readthedocs.io/en/stable/installation.html)

## Breakdown of Codes
Now we will breakdown and see how to execute the code: 

```bash
import numpy as np
import rasterio
```
These are import statements. The `numpy` library is imported with the alias `np`, and the `rasterio` library is imported. These libraries will be used to perform mathematical operations and read/write geospatial raster data, respectively.
```bash
landsat_image = r"E:/Python_Tutorial/Landsat/landsat_2023.tif"
```
This line defines the path to the input raster file, which is a Landsat-8 image. You replace the path as per your desired location.

```bash
bai_output = r"E:/Python_Tutorial/Landsat/bai_2023.tif"
nbr_output = r"E:/Python_Tutorial/Landsat/nb_2023.tif"
ndvi_output = r"E:/Python_Tutorial/Landsat/ndvi_2023.tif"
msavi_output = r"E:/Python_Tutorial/Landsat/msavi_2023.tif"
```
These lines define the output file paths for the various indices that will be calculated. For indices are being calculated: **BAI**, **NBR**, **NDVI**, and **MSAVI**. For each index, a separate output file is created.

 ```bash

with rasterio.open(landsat_image) as src:
    #read image bands
    red = src.read(4).astype(np.float32)
    nir = src.read(5).astype(np.float32)
    swir = src.read(6).astype(np.float32)
    
```
In this section, we open the Landsat stack image using the `rasterio` library. The `landsat_image` variable should contain the path to your Landsat 8 stack image. We read the specific bands required for calculating the vegetation indices: Band 4 (red), Band 5 (near-infrared/NIR), and Band 6 (shortwave infrared/SWIR). These bands are accessed using the read function of the `src` object and stored as `NumPy` arrays.

 ```bash
    bai = ((swir + red) - (nir + 0.1)) / ((swir + red) + (nir + 0.1))    
    bai = (bai - np.min(bai)) / (np.max(bai) - np.min(bai)) 
```
Here, we calculate the BAI (Burn Area Index) using the formula: `BAI = ((SWIR + Red) - (NIR + 0.1)) / ((SWIR + Red) + (NIR + 0.1))`. The BAI is then normalized to the range [0, 1] by subtracting the minimum value and dividing by the range of values.

```bash
   nbr = (nir - swir) / (nir + swir)
```
For NBR (Normalized Burn Ratio), we use the formula: `NBR = (NIR - SWIR) / (NIR + SWIR)`. This index represents burned areas and can be useful in assessing fire damage.
```bash
   ndvi = (nir - red) / (nir + red)
```
The NDVI (Normalized Difference Vegetation Index) is calculated as `(NIR - Red) / (NIR + Red)`. It measures the presence and health of vegetation, with higher values indicating healthier vegetation.

```bash
   msavi = (2 * nir + 1 - np.sqrt((2 * nir + 1) ** 2 - 8 * (nir - red))) / 2
```
The MSAVI (Modified Soil-Adjusted Vegetation Index) is computed using the formula: `MSAVI = (2 * NIR + 1 - sqrt((2 * NIR + 1) ** 2 - 8 * (NIR - Red))) / 2`. It is an improvement over NDVI, providing better results in areas with dense vegetation and reducing the soil background influence.

```bash
   profile = src.profile
   profile.update(dtype=rasterio.float32, count=1)
```
In this code, the `profile` variable is created to store the metadata of the source raster (Landsat image). The `src.profile` is used to access the profile information of the source raster. The `update()` function is then called to modify the profile by setting the data type `(dtype)` to `rasterio.float32` and the band count (count) to 1. This ensures that the output files will have the appropriate data type and single-band configuration.

```bash
    with rasterio.open(bai_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(bai, axis=0))
```
This code block uses `rasterio.open()` to create a new raster file for the BAI output `(bai_output)` using the specified profile. The `'w'` parameter indicates that the file will be opened in write mode. The with statement ensures that the file is properly closed after writing. The `dst.write()` function is then called to write the BAI array (bai) to the file. `np.expand_dims`(bai, axis=0) is used to add an additional dimension to the bai array, as `dst.write()` expects a 3-dimensional array (band, row, column).

The same process is repeated for the `NBR`, `NDVI`, and `MSAVI` outputs

```bash
    with rasterio.open(nbr_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(nbr, axis=0))

    with rasterio.open(ndvi_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(ndvi, axis=0))

    with rasterio.open(msavi_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(msavi, axis=0))
```

**Here is the full code:**
```bash
#import the libraries
import rasterio
import numpy as np
#input of Landsat image
landsat_image = r"E:/Python_Tutorial/Landsat/landsat_2023.tif"
#set output folder for indices
bai_output = r"E:/Python_Tutorial/Landsat/bai_2023.tif"
nbr_output = r"E:/Python_Tutorial/Landsat/nb_2023.tif"
ndvi_output = r"E:/Python_Tutorial/Landsat/ndvi_2023.tif"
msavi_output = r"E:/Python_Tutorial/Landsat/msavi_2023.tif"
#open the landsat stack image using rasterio
with rasterio.open(landsat_image) as src:
    #read image bands
    red = src.read(4).astype(np.float32)
    nir = src.read(5).astype(np.float32)
    swir = src.read(6).astype(np.float32)
    
    # Calculate BAI (Burn Area Index)
    bai = ((swir + red) - (nir + 0.1)) / ((swir + red) + (nir + 0.1))
    # Normalize BAI to the range [0, 1]
    bai = (bai - np.min(bai)) / (np.max(bai) - np.min(bai))
    # Calculate NBR (Normalized Burn Ratio)
    nbr = (nir - swir) / (nir + swir)
    # Calculate NDVI (Normalized Difference Vegetation Index)
    ndvi = (nir - red) / (nir + red)
    # Calculate MSAVI (Modified Soil-Adjusted Vegetation Index)
    msavi = (2 * nir + 1 - np.sqrt((2 * nir + 1) ** 2 - 8 * (nir - red))) / 2

    # Prepare metadata for the output files
    profile = src.profile
    profile.update(dtype=rasterio.float32, count=1)
    
    # Save the vegetation indices to the output paths
    with rasterio.open(bai_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(bai, axis=0))

    with rasterio.open(nbr_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(nbr, axis=0))

    with rasterio.open(ndvi_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(ndvi, axis=0))

    with rasterio.open(msavi_output, 'w', **profile) as dst:
        dst.write(np.expand_dims(msavi, axis=0))
        
print("complete the process")

```


# Benefit of using RemoteSens_INdices

Calculating multiple vegetation indices (such as NDVI, NDWI, NDMI, EVI, SAVI) in a single time using Python can provide several benefits for remote sensing analysis:

1. **Efficiency**: By processing multiple vegetation indices in a single script, you can save time and processing power by avoiding redundant calculations and loading data into memory multiple times.

2. **Comparative analysis**: Having multiple vegetation indices calculated in one script allows for a more comprehensive analysis of vegetation conditions, which can help in comparative studies and change detection analysis.

3. **Diverse data sources**: Different vegetation indices respond to different vegetation characteristics, allowing for a more diverse set of data to be used in remote sensing analysis. With Python, you can calculate all of these indices from a single dataset, enabling a more holistic analysis.

4. **Visualization**: Python has many tools for data visualization, and having multiple vegetation indices calculated in a single script allows for easy visualization and comparison of the different indices.

5. **Flexibility**: Python is a flexible programming language that can be used to automate and customize workflows for remote sensing analysis. By processing multiple vegetation indices in one script, you can easily modify the workflow to suit your research question and the properties of the input data.

In summary, calculating multiple vegetation indices in a single Python script can help to save time, provide a more comprehensive analysis, enable the use of diverse data sources, facilitate visualization, and offer flexibility in workflow customization

## Contact ME
* Email: koushikghosh1a2s@gmail.com.

* LinkedIn: [Koushik Ghosh](https://www.linkedin.com/in/koushik-ghosh-490761192/)

* ResearchGate: [Koushik Ghosh](https://www.researchgate.net/profile/Koushik-Ghosh-23)




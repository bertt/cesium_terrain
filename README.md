# cesium_terrain

Procedure to create a Cesium quantized mesh terrain from a GeoTiff file.
As input we'll use a AHN4 download from Texel - The Netherlands

Online demo Texel: https://bertt.github.io/cesium_terrain/texel/ahn4/

Online demo Voorhout: https://bertt.github.io/cesium_terrain/voorhout/

Blog: https://bertt.wordpress.com/2018/11/26/visualizing-terrains-with-cesium-ii/

![image](https://user-images.githubusercontent.com/538812/208296434-39bb50ec-7acf-4969-9f0a-546ee08138d9.png)

Clone this repository

```
$ git clone https://github.com/bertt/cesium_terrain.git
```

AHN4 downloads viewer:

https://www.arcgis.com/apps/mapviewer/index.html?layers=77da2e9eeea8427aab2ac83b79097b1a

```
$ cd texel\ahn4
```

Download zip file AHN 4 - Texel DTM 0.5m (245 MB)

```
$ wget https://ns_hwh.fundaments.nl/hwh-ahn/ahn4/02a_DTM_0.5m/M_09BZ1.zip
Length: 251791382 (240M) [application/x-zip-compressed]
Saving to: ‘M_09BZ1.zip’

M_09BZ1.zip                                            100%[=========================================================================================================================>] 240.13M   593KB/s    in 6m 44s  

2022-12-18 10:44:27 (609 KB/s) - ‘M_09BZ1.zip’ saved [251791382/251791382]
```

Uzip the file

```
$ unzip M_09BZ1.zip
Archive:  M_09BZ1.zip
  inflating: M_09BZ1.TIF
```
Result: file M_09BZ1.TIF (502 MB)

Warp the file to epsg:3857, result file  M_09BZ1_3857.tif(497 MB)

```
$ gdalwarp -t_srs EPSG:3857 M_09BZ1.tif M_09BZ1_3857.tif
```

Create output 'tiles' directory:

```
$ mkdir tiles
```

Tile to quantized mesh using ctb-tile using Docker image tumgis/ctb-quantized-mesh

```
$ docker run -it -v D:\dev\github.com\bertt\cesium_terrain\texel\ahn4:/data tumgis/ctb-quantized-mesh ctb-tile -f Mesh -C -o /data/tiles /data/M_09bz1_3857.tif
```
A 'tiles' subdirectory will be created with levels 0-19 (130 MB)

Copy layer.json to the tiles subdirectory:

```
$ cp ../../layer.json ./tiles/
```

Copy index.html to current directory:

```
$ cp ../../index.html .
```

Unzip the terrain tiles, result tileset is 343 MB

```
$ cp ../../unzip_terrain_tiles.sh .
$ sh unzip_terrain_tiles.sh
```

Serve the directory

```
$ npx serve
```

In browser go to http://localhost:3000

Note, there is some missing data:

![image](https://user-images.githubusercontent.com/538812/208296521-6d1da1e1-4e2e-43a2-b8ec-fa9b01465a0d.png)

This can be fixed by applying tool gdal_fillnodata (https://gdal.org/programs/gdal_fillnodata.html), it is installed by QGIS (default directory D:\Program Files\QGIS 3.22.3\apps\Python39\Scripts).




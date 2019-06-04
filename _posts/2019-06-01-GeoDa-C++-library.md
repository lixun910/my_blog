---
layout: post
title: GeoDa Library (Dev notes)
date:   2019-06-01 13:50:39
categories: c++ swig gdal geoda
author: Xun Li
---

# libgeoda: GeoDa C++ library

by Xun Li

GeoDa library (or libgeoda) is a C++ library that provides core functionalities of spatial data analysis and modeling in GeoDa, which is a desktop software focuses more on user interface.  The main purpose of this GeoDa library is to wrap GeoDa's C++ code into a library, and expose GeoDa's functionalities to C++ applications or other programming languages via SWIG/Rcpp.  By doing so,  other research projects can easily integrate the latest and fast (thanks to the C++ implementation) algorithms of spatial data analysis in GeoDa no matter what programming language they are using.

# Architecture 

The design of the architecture is shown in the following Figure. The major modules, such as `spatial regression`/`spatial analysis`/`clustering`, will be first separated from the user interface code. The functionalities of these modules will be exposed via some public APIs. These APIs will be fianlly wrapped by using SWIG for interfacing with other languages,  such as Python or Java. (Note: It is possible that we need to use Rcpp to wrap libgeoda for R.)
![](https://github.com/lixun910/images/blob/master/geoda_features/libgeoda-arch.png?raw=true)


## I/O module

The I/O module is the entry point when calling libgdal functions. We design this module to be flexible enough that it can not only take advantage of static linked gdal library to read and write spatial dataset of different formats (see discussion below), but also has the ability to interoperate with existing geo-spatial libraries, such as: [GeoPandas](http://geopandas.org/)/[Pysal](http://pysal.org/) in Python environment, [sf](https://r-spatial.github.io/sf/index.html)/[rgdal](https://cran.r-project.org/web/packages/rgdal/index.html) in R environment, [Geotools](https://geotools.org/) in Java environment.

### 1. Data I/O using libgdal

GeoDa uses GDAL/OGR to read/write vector data. That means GeoDa's public APIs will take OGR geometries as input values. Therefore, libgdal should be embeded inside GeoDa C++ library as well, so that libgeoda can either read and write spatial dataset that libgdal supports, or interface with GDAL/OGR objects from other C++ applications.

When build GeoDa Library, libgal should be static linked so that there is no need to install libgdal manually if it's not existed. The problem is that libgdal has many dependencies (e.g. for different drivers: sqlite, mysql, libgeos, libproj etc.), so GeoDa library needs a "minimal" libgdal with no dependencies or, if any, dependencies should be statically linked to libgdal to avoid any issues of installation or compatibale issues.

```
libgeoda
  |
  |_____ libgdal (static linking)
  |        |
  |        |_____ libgeos (static linking)
  |        |_____ libproj (static linking)
  |
  |______ boost (static linking)
  |______ ANN (static linking)
  |______ wxWidgets (non-gui, static linking)
  |______ CLAPACK/BLAS (static linking)
```

By default, GeoDa library will be able to support some popular vector file formats because of using libgdal internally. These file formats include:

 ```
  ESRI Shapefile -vector- (rw+v): ESRI Shapefile
  MapInfo File -vector- (rw+v): MapInfo File
  CSV -vector- (rw+v): Comma Separated Value (.csv)
  GML -vector- (rw+v): Geography Markup Language (GML)
  GPX -vector- (rw+v): GPX
  KML -vector- (rw+v): Keyhole Markup Language (KML)
  GeoJSON -vector- (rw+v): GeoJSON
  TopoJSON -vector- (rov): TopoJSON
  OpenFileGDB -vector- (rov): ESRI FileGDB
  GFT -vector- (rw+): Google Fusion Tables
  CouchDB -vector- (rw+): CouchDB / GeoCouch
  Carto -vector- (rw+): Carto
 ```

 The filename or URL (e.g. for carto) of data source can be used directly as an input parameter to call the functions of GeoDa library.  For example,  if using GeoDa Library in Python:

```Python
import geoda

gda = geoda.read_file('/path/to/natregimes.shp')
w = gda.create_queen_weights(poly_id="fipsno")
```
 

### 2. Interoperation using WKB

The libgdal's I/O module is also designed to interface with existing libraries in different programming environment, such as [GeoPandas](http://geopandas.org/)/[Pysal](http://pysal.org/) in Python environment, [sf](https://r-spatial.github.io/sf/index.html)/[rgdal](https://cran.r-project.org/web/packages/rgdal/index.html) in R environment, [Geotools](https://geotools.org/) in Java environment. 

Well-known text (WKT) is a text markup language for representing vector geometry objects on a map. A binary equivalent, known as well-known binary (WKB), is used to transfer and store the same information on many popular databases, such as Postgres/PostGIS extension, Sqlite/spatialite. 

It is also supported by all the geo-spatial libraries mentioned above. For example, [sf](https://r-spatial.github.io/sf/index.html) in R uese WKB serialisations written in C++/Rcpp for fast I/O with GDAL and GEOS. Therefore, we use WKB to exchange geometry objects between `libgeoda` and other geo-spatial libraries. 

The attributes (table) data are exchanged directly in-memory between libgeoda and other programming languages. libgeoda uses STL vector to store numeric or string values. SWIG supports the conversion of data between C++ (libgdal) and other programming languages. 

Here is a list of geo-spatial libraries that libgeoda is designed to interface with:

| library name | programming language |
| ------------ | -------------------- |
| GeoPandas | Python |
| Shapely | Python |
| PySAL | Python |
| RGDAL | R |
| SF | R |
| GeoTools | Java |
| (py)GDAL | Python |

### Interfacing with GeoPandas

GeoPandas uses Shapely to for its geometry column.

```python
import geopandas

nat = geopanda.read_file('/path/to/natregimes.shp')
# nat is a geodatafrom object
gda = geoda.read_geopandas(nat)

w = gda.create_queen_weights(poly_id="fipsno")
```
### Interfacing with PySAL
```python
import pysal

shp = pysal.open('/path/to/natregimes.shp')
dbf = pysal.open('/path/to/natregimes.dbf')

gda = geoda.read_pysal(shp, dbf)

w = gda.create_queen_weights(poly_id="fipsno")
```


### Interfacing with RGDAL in R
```R
library(rgdal)
library(rgeoda)
nat <- readOGR('/path/to/natregimes.shp')

gda <- rgeoda(nat)
```

### Interfacing with GeoTools in Java

```Java
import java.io.File;
import java.util.Map;
import org.locationtech.jts.io.WKBReader;
import org.locationtech.jts.io.WKBWriter;
import io.github.GeoDa

File file = new File("mayshapefile.shp");
Map<String, String> connect = new HashMap();
connect.put("url", file.toURI().toString());

DataStore dataStore = DataStoreFinder.getDataStore(connect);
String[] typeNames = dataStore.getTypeNames();
String typeName = typeNames[0];

FeatureSource featureSource = dataStore.getFeatureSource(typeName);
FeatureCollection collection = featureSource.getFeatures();
FeatureIterator iterator = collection.features();

Vector<String> collection = new Vector<String>();
while (iterator.hasNext()) {
  Feature feature = iterator.next();
  GeometryAttribute geom = feature.getDefaultGeometryProperty();

  // Geometry to WKB string
  WKBWriter wkbWriter = new WKBWriter();
  String wkb = WKBWriter.bytesToHex(wkbWriter.write(geom));
  // WKB string to Geometry
  //WKBReader wkbReader = new WKBReader();
  //Geometry geom = wkbReader.read(WKBReader.hexToBytes(wkb));
  collection.add(wkb);
}

GeoDa gda = new GeoDa(collection);
```

### Interfacing with GDAL/OGR in Python


```Python
import os
import ogr

# using ogr to read a layer from a ESRI Shapefile
daShapefile = 'data/natregimes.shp'
driver = ogr.GetDriverByName('ESRI Shapefile')
dataSource = driver.Open(daShapefile, 0) 
layer = dataSource.GetLayer()
# featureCount = layer.GetFeatureCount()
# for feature in layer:
#   geom = feature.GetGeometryRef()
#   print(geom.Centroid().ExportToWkb())

gda = geoda.read_gdal(layer)
w = gda.create_queen_weights(poly_id="fipsno")
```

### Interfacing with PostGIS using psycopg2 in Python

```python
import psycopg2
from shapely import wkb

conn = psycopg2.connect('...')
curs = conn.cursor()

shps = {}  # key: gid, value: Shapely geom (wkb)
curs.execute('select gid, geom as geom from natregimes;')
for gid, geom in curs:
    shps[gid] = wkb.loads(geom, hex=True)

gda = geoda.read_pysal(shp, dbf)
w = gda.create_queen_weights(poly_id="fipsno")
```

# Appendix

## Setup on Mac OSX

Install libgdal using `brew`. The latest version of libgdal on `brew` is **2.4.1**.

```
brew install gdal
```

Please note: when install GDAL for python using pip, we need to specify version 2.4.0. Otherwise, pip install GDAL will choose version 3.0, which is not compatible with the libgdal 2.4.1 installed by brew. Or, you can manually compile and install GDAL 3.0.

```
export LDFLAGS=-L/usr/local/Cellar/gdal/2.4.1_1/lib
export CPPFLAGS=-I/usr/local/Cellar/gdal/2.4.1_1/include
pip3 install GDAL==2.4.0
```


## Build a minimal libgdal

### libgeos


### libproj

After running `./confgiure` libgdal, you can see there is a GDALmake.opt file, and in this file, the line start with `LIBS = ` shows how the libgeos and libproj4 are linked (e.g. `-lgeos_c)

libgdal needs C++11 since 2.4.0. To compatible with existing GeoDa project, we use libgdal 2.2.4 to disable C++11 using flag `--without-cpp11`.

For example
```Bash
./configure --prefix=/Users/xunli/Downloads/test \
            --without-cpp11 \
            --with-pg=no \
            --with-xml2=no \
            --without-mrf \
            --with-libz=internal \
            --with-jpeg=internal \
            --without-grib \
            --without-openjpeg \
            --with-libiconv-prefix="-L/usr/lib" \
            --without-ld-shared \
            CFLAGS="-Os -arch x86_64" CXXFLAGS="-Os -arch x86_64" LDFLAGS="-arch x86_64"

```

In file `GDALmake.opt`, change the dynamic linking flags of libgeos and libproj to static linking flags. For example:

| Old | New |
| --- | --- |
| `LIBS = $(SDE_LIB)  -L/usr/local/opt/proj/lib -lproj -L/usr/local/Cellar/geos/3.7.2/lib -lgeos_c -lpthread -ldl` | `LIBS = $(SDE_LIB)  /usr/local/opt/sqlite3/lib/libsqlite3.a /usr/local/opt/proj/lib/libproj.a /usr/local/Cellar/geos/3.7.2/lib/libgeos.a /usr/local/Cellar/geos/3.7.2/lib/libgeos_c.a -L/usr/lib -liconv -lpthread -ldl` |


```
*** Warning: Linking the shared library libgdal.la against the
*** static library /usr/local/opt/sqlite3/lib/libsqlite3.a is not portable!

*** Warning: Linking the shared library libgdal.la against the
*** static library /usr/local/opt/proj/lib/libproj.a is not portable!

*** Warning: Linking the shared library libgdal.la against the
*** static library /usr/local/Cellar/geos/3.7.2/lib/libgeos.a is not portable!

*** Warning: Linking the shared library libgdal.la against the
*** static library /usr/local/Cellar/geos/3.7.2/lib/libgeos_c.a is not portable!
```

### wxWidgets 3.1.2 non-ui build

```Bash
./configure --with-cocoa \
            --disable-shared \
            --enable-monolithic \
            --disable-gui \
            --prefix=/Users/xunli/Downloads/test/wx \
            --with-macosx-version-min=10.13
```





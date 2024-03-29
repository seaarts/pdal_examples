# pdal basics
Snippets of pdal pipelines for processing `.las` and `.laz` point cloud files into rasters, such as DSMs, DEMs etc. Saved for my own records.


# Basic usage instructions

## pdal pipelines
The processing of one `.laz` file into a DSM `.tif` is written in a pdal pipeline ([docs](https://pdal.io/apps/pipeline.html)).

The pipeline is saved as a `.json` file, e.g. `do_thing.json` and called by

> `pdal pipeline do_thing.json`

## substitutions
In the pipeline, either implicitly or explicitly, there is a reader and a writer. The reader takes as an argument an input `filename`; the writer takes an output `filename`. These can be substituted for in the command line. For instance, to call pipeline `get_dsm.json` with input `input_cloud.laz` and output `output_dsm.tif` one can call

> `pdal pipeline get_dsm.json --writers.gdal.filename=output_dsm.tif --readers.las.input=input_cloud.laz`

Note that the pipeline `get_dsm.json` is special in that it transforms a point cloud  `.laz` into a raster `.tif`. As a result the reader and writer are different; the reader is `reader.las` and the writer `writer.gdal`.

## processing many files

See pdal batch processing ([docs](https://pdal.io/workshop/exercises/batch_processing/batch-processing.html)). To process all files in a directory we can use bash and GNU parallel ([docs](https://www.gnu.org/software/parallel/)) (which one should cite). The syntax is relatively simple and builds on substitutios. For instance to make DSMs of all `.laz` files in `input_dir` and create files `dsm_<filename>.tif` in `output_dir` we issue the command

> `ls input_dir/*.laz | parallel -I{} pdal pipeline get_dsm.json \` <br>
>  `--readers.las.filename={} --writers.gdal.filename=output_dir/dsm_{/.}.tif`

Please see the docs for additional information on e.g. specifying the nr. of jobs to be processes in parallel.

## Example - Making DSMs from USGS point cloud data
You can download extensitve LiDAR point cloud data from USGS's [data portal](https://apps.nationalmap.gov/downloader/). The data is tiled in relatively small files, so to collect data over a larger area many files are needed. The files are in `.laz` format. The following example details how to convert these into Digital Surface Model (DSM) raster files, stored as `.tif`-files with the same name except with a `dsm`-prefix.

### Issuing the processing command in pdal
If you directly use this repo and do not want to know what dpal does, you only need to to the following.

1. Save the `.laz` files in a driectory `pointclouds` (or one of any other name).
2. Create a directory `rasters` for the output.
3. Call `pdal` pipeline `get_dsm.json` in parallel on all the files with

> `ls <path_to_pointclouds>/*.laz | parallel -I{} pdal pipeline get_dsm.json \` <br>
> `--readers.las.filename={} --writers.gdal.filename=<path_to_rasters>/dsm_{/.}.tif`

### Understanding the pipeline
What does the `get_dsm.json` pipeline do?  It is based loosely on the "Identifying ground" [tutorial](https://pdal.io/workshop/exercises/analysis/ground/ground.html). The file contains a sequence of data manipulations:

1. A point cloud in .laz format is read as input.
2. The point cloud is reprojected into CRS EPSG:26918
3. All points classified as vegetation (Classes 2 - 5) are dropped.
4. Remaining points are reclassified as "0".
5. Extended local minima ELM and outliers are removed.
6. An SMRF filter is applied to roughly identify ground. Tuning of the parameters may be necessary so as not to delete buildings. "Ground" points are given Class 2.
7. Only points classified as ground points are retained.
8. The point cloud is rasterized and saved as a .tif file.

For details on individual steps the pdal documentation is recommended.

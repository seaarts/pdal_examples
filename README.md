# pdal_ithaca
Snippets of pdal for making DSMs from LAZ files


# Basic usage instructions

## pdal pipelines
The processing of one `.laz` file into a DSM `.tif` is written in a pdal pipeline ([docs](https://pdal.io/apps/pipeline.html)).

The pipeline is saved as a `.json` file, e.g. `do_thing.json` and called by

> `pdal pipeline do_thing.json`

## substitutions
In the pipeline, either implicitly or explicitly, there is a reader and a writer. The reader takes as an argument an input `filename`; the writer takes an output `filename`. These can be substituted for in the command line. For instance, to call pipeline `get_dsm.json` with input `input_laz.laz` and output `output_dsm.tif` one can call

> `pdal pipeline get_dsm.json --writers.gdal.filename=output_dsm.tif --readers.las.input=input_laz.laz`

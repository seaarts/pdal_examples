# pdal_ithaca
Snippets of pdal for making DSMs from LAZ files



The processing of one `.laz` file into a DSM `.tif` is written in a pdal pipeline ([docs](https://pdal.io/apps/pipeline.html)).

The pipeline is saved as a `.json` file, e.g. `do_thing.json` and called by

> `pdal pipeline do_thing.json`

asd

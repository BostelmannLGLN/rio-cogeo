**rio-cogeo** modules adds a `cogeo` sub-command to your rasterio (rio) CLI. Three (3) commands are then available: `create`, `info` and `validate`.

```
$ rio cogeo --help
  Usage: rio cogeo [OPTIONS] COMMAND [ARGS]...

  Rasterio cogeo subcommands.

  Options:
    --version  Show the version and exit.
    --help     Show this message and exit.

  Commands:
    create    Create COGEO
    info      Lists information about a raster dataset.
    validate  Validate COGEO
```

### Create

```
$ rio cogeo create --help
  Usage: rio cogeo create [OPTIONS] INPUT OUTPUT

    Create Cloud Optimized Geotiff.

  Options:
    -b, --bidx BIDX                  Band indexes to copy.
    -p, --cog-profile [jpeg|webp|zstd|lzw|deflate|packbits|lzma|lerc|lerc_deflate|lerc_zstd|raw]
                                     CloudOptimized GeoTIFF profile (default: deflate).
    --nodata NUMBER|nan              Set nodata masking values for input dataset.
    --add-mask                       Force output dataset creation with an internal mask (convert alpha band or nodata to mask).
    --blocksize INTEGER             Overwrite profile's tile size.
    -t, --dtype [ubyte|uint8|uint16|int16|uint32|int32|float32|float64] Output data type.
    --overview-level INTEGER         Overview level (if not provided, appropriate overview level will be selected until the
                                     smallest overview is smaller than the value of the internal blocksize)
    --overview-resampling [nearest|bilinear|cubic|cubic_spline|lanczos|average|mode|gauss]
                                     Overview creation resampling algorithm (default: nearest).
    --overview-blocksize TEXT        Overview's internal tile size (default defined by GDAL_TIFF_OVR_BLOCKSIZE env or 128)
    -w, --web-optimized              Create COGEO optimized for Web.
    --latitude-adjustment / --global-maxzoom
                                     Use dataset native mercator resolution for MAX_ZOOM calculation (linked to dataset
                                     center latitude, default) or ensure MAX_ZOOM equality for multiple dataset accross latitudes.
    -r, --resampling [nearest|bilinear|cubic|cubic_spline|lanczos|average|mode|gauss]
                                     Resampling algorithm (default: nearest). Will only be applied with the `--web-optimized` option.
    --in-memory / --no-in-memory     Force processing raster in memory / not in memory (default: process in memory if smaller than 120 million pixels)
    --allow-intermediate-compression Allow intermediate file compression to reduce memory/disk footprint.
    --forward-band-tags              Forward band tags to output bands.
    --threads THREADS                Number of worker threads for multi-threaded compression (default: ALL_CPUS)
    --co, --profile NAME=VALUE       Driver specific creation options. See the documentation for the selected output driver for more information.
    --config NAME=VALUE              GDAL configuration options.
    -q, --quiet                      Remove progressbar and other non-error output.
    --help                           Show this message and exit.
```

### Validate

```
$ rio cogeo validate --help
Usage: rio cogeo validate [OPTIONS] INPUT

  Validate Cloud Optimized Geotiff.

Options:
  --strict  Treat warnings as errors.
  --help    Show this message and exit.
```

The `strict` options will treat warnings (e.g missing overviews) as errors.


### Info
(extented version or `rio info`).

```
$ rio cogeo info --help
Usage: rio cogeo info [OPTIONS] INPUT

  Dataset info.

Options:
  --json  Print as JSON.
  --help  Show this message and exit.
```


### Examples

```bash
# Create a COGEO with DEFLATE compression (Using default `Deflate` profile)
$ rio cogeo create mydataset.tif mydataset_jpeg.tif

# Validate COGEO
$ rio cogeo validate mydataset_jpeg.tif

# Create a COGEO with JPEG profile and the first 3 bands of the data and add internal mask
$ rio cogeo create mydataset.tif mydataset_jpeg.tif -b 1,2,3 --add-mask --cog-profile jpeg

# List Raster info
$ rio cogeo info mydataset_jpeg.tif
Driver: GTiff
File: mydataset_jpeg.tif
COG: True
Compression: DEFLATE
ColorSpace: None

Profile
    Width:            10980
    Height:           10980
    Bands:            1
    Tiled:            True
    Dtype:            uint16
    NoData:           0.0
    Alpha Band:       False
    Internal Mask:    False
    Interleave:       BAND
    Colormap:         False

Geo
    Crs:              EPSG:32634
    Origin:           (699960.0, 3600000.0)
    Resolution:       (10.0, -10.0)
    BoundingBox:      (699960.0, 3490200.0, 809760.0, 3600000.0)
    MinZoom:          10
    MaxZoom:          19

IFD
    Id      Size           BlockSize     Decimation
    0       10980x10980    1024x1024     0
    1       5490x5490      128x128       2
    2       2745x2745      128x128       4
    3       1373x1373      128x128       8
    4       687x687        128x128       16
```

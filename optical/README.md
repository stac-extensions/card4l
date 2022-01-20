# STAC CARD4L Optical Extension Specification

- **Title:** CARD4L Optical
- **Identifier:** <https://stac-extensions.github.io/card4l/v1.0.0/optical/schema.json>
- **Field Name Prefix:** card4l (shared with the CARD4L SAR Extension)
- **Scope:** Item
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr

This extension specifies how to create [STAC](https://github.com/radiantearth/stac-spec) Items that
comply to the [CEOS CARD4L](http://ceos.org/ard/) product family specification for either
- *Optical Surface Reflectance* (SR) products in version 5.0
  ([PDF](https://ceos.org/ard/files/PFS/SR/v5.0/CARD4L_Product_Family_Specification_Surface_Reflectance-v5.0.pdf),
  [Word](https://ceos.org/ard/files/PFS/SR/v5.0/CARD4L_Product_Family_Specification_Surface_Reflectance-v5.0.docx))
  *or*
- *Optical Surface Temperature* (ST) products in version 5.0
  ([PDF](https://ceos.org/ard/files/PFS/ST/v5.0/CARD4L_Product_Family_Specification_Surface_Temperature-v5.0.pdf),
  [Word](https://ceos.org/ard/files/PFS/ST/v5.0/CARD4L_Product_Family_Specification_Surface_Temperature-v5.0.docx)).

**Document structure:** In general, the fields required in this extension are required to either meet
the *threshold (minimum) requirements* by the CEOS CARD4L specification *or* are required fields in STAC.
Any additional optional field provided may lead to a higher percentage for the CARD4L *target (desired) requirements*.

The column *Field Name* refers to the STAC field names. The column *Req.* refers to the requirement number in the CARD4L specification.

**STAC Extensions:** This extension makes use of a number of existing STAC extensions:

- [EO](https://github.com/stac-extensions/eo)
- [File](https://github.com/stac-extensions/file) (optional)
- [Processing](https://github.com/stac-extensions/processing) (optional)
- [Projection](https://github.com/stac-extensions/projection)
- [Raster](https://github.com/stac-extensions/raster)
- [View](https://github.com/stac-extensions/view)

You have to read the STAC extensions in combination with this extension as this extension just provides
the mapping between the STAC fields and the CARD4L requirements, but this extension doesn't give information
on the data type or an actual detailed description about the fields.

**Additional resources:**

- [Examples](examples/)
- [JSON Schema](json-schema/)
  
  *Please note that the schema gives only a first indication on whether your metadata is correctly formatted
   as we can't provide a full schema for validation at the moment.*
  *For example, the assets are not fully validated yet. Passing the schema also does not imply that you are CARD4L compliant!*

## STAC Collections

CARD4L lists a lot of requirements (and fields) that have common values across all generated STAC Items and assets.
Thus, it is **recommended** to provide a STAC Collection for the Items and put common fields (in the STAC Item `properties`)
into [Collection `summaries`](https://github.com/radiantearth/stac-spec/tree/v1.0.0/collection-spec/collection-spec.md#collection-fields).
While the STAC Item fields still need to be in the Item, too, you can de-duplicate links and assets by putting common
links once into the STAC Collection links. Also, common assets can be just put once into the STAC Collection using the
STAC extension [Collection Assets](https://github.com/radiantearth/stac-spec/tree/v1.0.0/collection-spec/collection-spec.md#assets).
All this is still CARD4L compliant as CARD4L doesn't require all information to be in a single file.

## STAC Items

STAC Items must always be valid, but not all STAC Item requirements are covered here,
only additional requirements and mappings to fulfill the CARD4L requirements are listed here:

| Field Name      | Req.  | Description                                                  |
| --------------- | ----- | ------------------------------------------------------------ |
| stac_extensions | *n/a* | **REQUIRED.** Must contain all extensions used.  See below for details \[1]. |
| geometry        | 1.4   | **REQUIRED.** The geometry of the acquisition.               |
| bbox            | 1.4   | **REQUIRED.** The bounding box of the acquisition.           |

\[1] The following values for `stac_extensions` apply:

| Value                                                                 | Required |
| --------------------------------------------------------------------- | -------- |
| `https://stac-extensions.github.io/card4l/v1.0.0/optical/schema.json` | ✓ |
| `https://stac-extensions.github.io/eo/v1.0.0/schema.json`             | ✓ |
| `https://stac-extensions.github.io/file/v2.0.0/schema.json`           | ✗ |
| `https://stac-extensions.github.io/processing/v1.1.0/schema.json`     | ✗ |
| `https://stac-extensions.github.io/projection/v1.0.0/schema.json`     | ✓ |
| `https://stac-extensions.github.io/raster/v1.1.0/schema.json`         | ✓ |
| `https://stac-extensions.github.io/view/v1.0.0/schema.json`           | ✓ |

### STAC Item Properties

#### CARD4L

| Field Name                            | Data Type | Req.  | Description                                                  |
| ------------------------------------- | --------- | ----- | ------------------------------------------------------------ |
| card4l:specification                  | string    | *n/a* | **REQUIRED.** The CARD4L specification implemented, either `SR` (Optical, Surface Reflectance) or `ST` (Optical, Surface Temperature). |
| card4l:specification_version          | string    | *n/a* | **REQUIRED.** The CARD4L specification version. Currently always `5.0`. |
| card4l:northern_geometric_accuracy    | [Geometric Accuracy Object](#geometric-accuracy-object) | 1.8 | An estimate of the northern geometric accuracy. |
| card4l:eastern_geometric_accuracy     | [Geometric Accuracy Object](#geometric-accuracy-object) | 1.8 | An estimate of the eastern geometric accuracy. |
| card4l:geometric_accuracy_radial_rmse | number    | 1.8   | Radial root mean square error (rRMSE) for sub-sample accuracy, in meters. |

Note that all these fields here are aligned with the STAC CARD4L SAR extension.

##### Geometric Accuracy Object

| Field Name | Data Type | Description              |
| ---------- | --------- | ------------------------ |
| bias       | number    | **REQUIRED.** Bias, in meters. |
| stddev     | number    | **REQUIRED.** Standard deviation, in meters. |

#### Common Metadata

| Field Name     | Req.  | Description                                                  |
| -------------- | ----- | ------------------------------------------------------------ |
| license        | *n/a* | Recommended to be specified in a STAC Collection.            |
| datetime       | 1.3   | **REQUIRED.** The time of the acquisition, usually the central timestamp between `start_datetime` and `end_datetime`. |
| start_datetime | 1.3   | Start time of the acquisition.                               |
| end_datetime   | 1.3   | End time of the acquisition.                                 |
| instruments    | 1.9   | **REQUIRED.**                                                |
| constellation  | (1.9) | Constellation name in lower-case. Only if part of a constellation, e.g. `sentinel-2` for Sentinel 2A and 2B. Can often be derived from `platform`. |
| platform       | (1.9) | Platform name in lower-case. Use a specific name such as `sentinel-2a` if part of constellation. MUST NOT duplicate `constellation`. |

#### EO (Electro-Optical)

| Field Name     | Req.   | Description                                                  |
| -------------- | ------ | ------------------------------------------------------------ |
| eo:cloud_cover | 1.17   | Cloud cover as one of the quality flags.                     |
| eo:bands       | (1.10) | You may include all bands specified in the assets again as an overview. |

1.17:  More data quality flags should be set. Just providing eo:cloud_cover is not enough,
but other quality flags such as [snow cover](https://github.com/stac-extensions/eo/pull/9)
are not tandardized in STAC yet.

#### Processing

| Field Name            | Req.        | Description                                                  |
| --------------------- | ----------- | ------------------------------------------------------------ |
| processing:facility   | 1.15        |                                                              |
| processing:software   | 1.13 / 1.15 | String likely needs to be split into software name and version number. |
| processing:lineage    | 1.15        | Additional processing information and parameters, e.g. a human-readable description of the processing chain. |
| processing:expression | 1.15        | A more machine-readable processing chain description such as a Dask graph, an openEO process or a SNAP graph. Alternatively, you can also link to a processing chain with the relation type `processing-expression` (see below). |

See also the [note](#notes) regarding the requirement 1.13 for a better understanding of how to use the fields best.

#### Projection

The metadata MUST specify the coordinate reference system (1.5) and MAY specify the map projection (1.6) through
either `proj:epsg` or one of the alternatives.

| Field Name                | Req.      | Description                                                  |
| ------------------------- | --------- | ------------------------------------------------------------ |
| proj:epsg                 | 1.5 / 1.6 | **REQUIRED.** This is required by STAC. If there's no suitable EPSG code, set this to `null` and add either `proj:wkt2` or `proj:projjson`. |
| proj:wkt2 / proj:projjson | 1.5 / 1.6 | One of `proj:wkt2` or `proj:projjson` is **required** if `proj:epsg` is set to `null`. |

#### View

| Field Name           | Req.  | Description                                                  |
| -------------------- | ----- | ------------------------------------------------------------ |
| view:off_nadir       | *n/a* | The average off-nadir angle, for per-pixel angles. In degrees. |
| view:incidence_angle | \[2]  | **REQUIRED.** The average incidence angle, for per-pixel angles, refer to the asset with the key `incidence-angle`. In degrees. |
| view:azimuth         | \[2]  | **REQUIRED.** The average azimuth angle, for per-pixel angles, refer to the asset with the key `azimuth`. In degrees. |
| view:sun_azimuth     | \[2]  | **REQUIRED.** The average sun azimuth angle, for per-pixel angles, refer to the asset with the key `sun-azimuth`. In degrees. |
| view:sun_elevation   | \[2]  | **REQUIRED.** The average sub elevation angle, for per-pixel angles, refer to the asset with the key `sun-elevation`. In degrees. |

\[2] Requirement 2.8 for Surface Temperature (ST) / 2.11 for Surface Reflectance (SR)

### STAC Item Links

| Relation Type             | Req.                | Description                                                  |
| ------------------------- | ------------------- | ------------------------------------------------------------ |
| card4l-document           | *n/a*               | **REQUIRED.** Provides at least one link to the CARD4L specification document. Word (media type: `application/vnd.openxmlformats-officedocument.wordprocessingml.document`) and/or PDF (media type: `application/pdf`). |
| derived_from              | 1.15                | Points back to the source's STAC Item. May be multiple items, if the product is derived from multiple acquisitions. |
| about                     | 1.13                | URL to algorithms used in the generation process. See also the [notes](#notes) regarding req. 1.13. |
| related                   | 1.14                | URL to the sources of auxiliary data used in the generation process. This is **required** if auxiliary data used in the generation process. Excludes DEMs, which use the relation `elevation-model` instead. |
| access                    | 1.16                | STRONGLY RECOMMENDED. URL to data access information.       |
| sensor-calibration        | 1.11                | URL to the sensor calibration parameters.                   |
| radiometric-accuracy      | 1.12                | URL describing the assessed absolute radiometric uncertainty of the version of the data or product. |
| geometric-correction      | 1.7                 | URL to the Geometric Correction algorithm details.          |
| elevation-model           | 1.14                | URLs to the Digital Elevation Model (DEM). Preferably URLs to a STAC Item with additional metadata for the DEM. |
| surface-model             | 1.14                | URL to the Digital Surface Model (DSM). Preferably URLs to a STAC Item with additional metadata for the DSM. |
| geometric-accuracy        | 1.8                 | URL to documentation of estimate of absolute localization error. |
| cloud                     | 2.5                 | URL to documentation about the cloud detection.             |
| cloud-shadow              | 2.6                 | URL to documentation about the cloud shadow detection.      |
| snow-ice                  | 2.7 (ST) / 2.8 (SR) | URL to documentation about the snow and ice mask.           |
| land-water                | 2.7 (SR)            | URL to documentation about the land and water mask (SR only). |
| atmosphere-emissivity     | 3.2 (ST)            | **REQUIRED.** URL to documentation about corrections for atmosphere and emissivity (ST only). |
| measurement-normalization | 3.3 (SR)            | URL to documentation about measurement normalization (SR only). |
| atmospheric-scattering    | 3.4 (SR)            | **REQUIRED.** URL to documentation about the directional atmospheric scattering algorithms (SR only). |
| water-vapor               | 3.5 (SR)            | **REQUIRED.** URL to documentation about the water vapour corrections (SR only). |
| ozone                     | 3.6 (SR)            | URL to documentation about the ozone corrections (SR only). |
| processing-expression     | 1.15                | A processing chain (or script) that describes how the data has been processed. |

Note: CARD4L XML files also allow DOIs to be given instead of URLs. DOIs must be converted to URLs for STAC!

### STAC Item Assets

Whether the metadata are provided in a single record relevant to all pixels, or separately for each pixel,
is at the discretion of the data provider. 

The role names specify the values to be used in the Asset's `roles`.
Each of the assets can either be exposed individually or grouped together in any form.
In the latter case the role names can simply be merged to a set of unique role names.
Roles can also be combined for a single file.
For example, a cloud mask which is also including cloud shadows 
can use the roles `cloud` and `cloud-shadow` for a single file.
The `values` in `raster:bands` property can the specify which value(s) correspond to clouds and
which value(s) correspond to cloud shadows respectively.
The *italic* role names could be used as the asset's key.

 The **bold** additional properties are required.

| Role Name(s)                           | Req.                 | Additional properties                                        | Description                                                  |
| -------------------------------------- | -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| (*reflectance* or *temperature*), data | 3.1 / 2.2            | `type`, `created`, **`eo:bands`**, **`raster:bands`** (with `data_type`, `bits_per_sample` and **`nodata`**), `file:byte_order` | **REQUIRED.** Points to the actual measurements. The value(s) for pixels that do not correspond to an observation must be provided in the property `nodata` in `raster:bands`. |
| *date*, metadata                       | 1.3                  | `type`, `raster:bands` (with `data_type` and `bits_per_sample`), `file:byte_order` | Points to a file with per-pixel acquisition timestamps.      |
| *incomplete-testing*, metadata         | 2.3                  | `type`, **`raster:bands`** (with **`values`**)               | **REQUIRED.** Points to a file that identifies pixels for which the per-pixel tests have not all been successfully completed. See CARD4L req. 2.3 for details. |
| *saturation*, metadata                 | 2.4                  | `type`, `eo:bands`, **`raster:bands`** (with **`values`**)   | **REQUIRED.** Points to a file that indicates where pixels in the input spectral bands are saturated. If the saturation is given per band, either `eo:bands` or `values` in `raster:bands` is **required** to indicate which pixels are saturated for each spectral band. |
| *cloud*, metadata                      | 2.5                  | `type`, **`raster:bands`** (with **`values`**)               | **REQUIRED.** Points to a file that indicates whether a pixel is assessed as being cloud. |
| *cloud-shadow*, metadata               | 2.6                  | `type`, **`raster:bands`** (with **`values`**)               | **REQUIRED.** Points to a file that indicates whether a pixel is assessed as being cloud shadow. |
| *snow-ice*, metadata                   | 2.7 (ST) / 2.8 (SR)  | `type`, **`raster:bands`** (with **`values`**)               | Points to a file that indicates whether a pixel is assessed as being snow/ice or not. |
| *land-water*, metadata                 | 2.7 (SR)             | `type`, **`raster:bands`** (with **`values`**)               | Points to a file that indicates whether a pixel is assessed as being land or water. |
| *incidence-angle*, metadata            | 2.8 (ST) / 2.11 (SR) | `type`, **`raster:bands`** (with `data_type`, `bits_per_sample` and **`unit`**), `file:byte_order` | Points to a file with per-pixel incidence angles. `unit` is usually `degree`. |
| *azimuth*, metadata                    | 2.8 (ST) / 2.11 (SR) | `type`, **`raster:bands`** (with `data_type`, `bits_per_sample` and **`unit`**), `file:byte_order` | Points to a file with per-pixel azimuth angles. `unit` is usually `degree`. |
| *sun-azimuth*, metadata                | 2.8 (ST) / 2.11 (SR) | `type`, **`raster:bands`** (with `data_type`, `bits_per_sample` and **`unit`**), `file:byte_order` | Points to a file with per-pixel sun azimuth angles. `unit` is usually `degree`. |
| *sun-elevation*, metadata              | 2.8 (ST) / 2.11 (SR) | `type`, **`raster:bands`** (with `data_type`, `bits_per_sample` and **`unit`**), `file:byte_order` | Points to a file with per-pixel sun elevation angles. `unit` is usually `degree`. |
| *terrain-shadow*, metadata             | 2.9 (SR)             | `type`, **`raster:bands`** (with **`values`**)               | Points to a file that indicates whether a pixel is not directly illuminated due to terrain shadowing. |
| *terrain-occlusion*, metadata          | 2.10 (SR)            | `type`, **`raster:bands`** (with **`values`**)               | Points to a file that indicates whether a pixel is not visible to the sensor due to terrain occlusion during off-nadir viewing. |
| *terrain-illumination*, metadata       | 2.12 (SR)            | `type`, `raster:bands` (with `data_type`, `bits_per_sample`), `file:byte_order` | Points to a file with coefficients used for terrain illumination correction are provided for each pixel. |

Note: `raster:bands[*].values` is not standardized yet in STAC, this could change to
`file:values` or something different with a similar structure in the future.

#### Additional Asset Properties

The following table lists properties that may occur in the assets.
The list doesn't specify which fields apply to which asset and it also doesn't specify which fields are required.
For those details please refer to the ["Additional properties" column in the table above](#stac-item-assets).

| Field Name      | Req.      | Data Type                                                    | Description                                                  |
| --------------- | --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| type            | *n/a*     | string                                                       | STRONGLY RECOMMENDED. The media type of the file format.     |
| created         | *n/a*     | string                                                       | The time of the processing is specified via the `created` property of the asset as specified in the [STAC Common metadata](https://github.com/radiantearth/stac-spec/tree/v1.0.0/item-spec/common-metadata.md#date-and-time). |
| eo:bands        | 1.10      | \[[Band Object](https://github.com/stac-extensions/eo/blob/v1.0.0/README.md#band-object)\] | Bands with at least the following fields included: `name` and `center_wavelength`. Add additional fields such as `full_width_half_max` to better meet the *target (desired) requirements*. See the CARD4L requirement 1.10 for further details. |
| raster:bands    | see below | \[[Raster Band Object](https://github.com/stac-extensions/raster/blob/v1.1.0/README.md#raster-band-object)\] | Bands with at least the required fields for the corresponding asset role (see above and below). |
| file:byte_order | *n/a*     | string                                                       | One of `big-endian` or `little-endian`.                      |

##### raster:bands

| Field Name      | Req.  | Data Type                                                    | Description                                                  |
| --------------- | ----- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| data_type       | *n/a* | string                                                       | One of the [Data Types](https://github.com/stac-extensions/raster/blob/v1.1.0/README.md#data-types). |
| unit            | *n/a* | string                                                       | The unit of the values in the asset, preferably compliant to [UDUNITS-2](https://ncics.org/portfolio/other-resources/udunits2/). |
| bits_per_sample | *n/a* | integer                                                      | Bits per sample                                              |
| nodata          | 2.2   | \[any]                                                       | for data. Value(s) for no-data.                              |
| values          | n/a   | \[[Mapping Object](https://github.com/stac-extensions/file/blob/v2.0.0/README.md#mapping-object)\] | Lists the value that are in the file and describes their meaning. |

## Notes

- 1.13: The algorithms can be given either in `processing:software` or as link with relation type `about`. 
  One of them is **required** by CARD4L.
- 2.13 (SR): CARD4L lists no specific requirements thus it's missing in this document.
- 3.2 (SR) / 3.3 (ST): Measurement Uncertainty is not required and it was not clear in which form this should be provided. 
  Also the CARD4L specification states for SR that
  
  > "\[i]n current practice, users determine fitness for purpose based on knowledge of the lineage of the data,
  > rather than on a specific estimate of measurement uncertainty."
  
  Thus this requirement is not captured in this document.
- 3.3 (SR): Measurement Normalisation is not required and it was not clear in which form this should be provided. 
  Thus this requirement is not captured in this document, but we have added the link relation type
  `measurement-normalization` to link to information on measurement normalisation.
- 4.1 (SR): CARD4L lists no specific requirements thus it's missing in this document.

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
  ([PDF](http://ceos.org/ard/files/PFS/SR/v5.0/CARD4L_Product_Family_Specification_Surface_Reflectance-v5.0.pdf),
  [Word](http://ceos.org/ard/files/PFS/SR/v5.0/CARD4L_Product_Family_Specification_Surface_Reflectance-v5.0.docx))
  *or*
- *Optical Surface Temperature* (ST) products in version 5.0
  ([PDF](http://ceos.org/ard/files/PFS/ST/v5.0/CARD4L_Product_Family_Specification_Surface_Temperature-v5.0.pdf),
  [Word](http://ceos.org/ard/files/PFS/ST/v5.0/CARD4L_Product_Family_Specification_Surface_Temperature-v5.0.docx)).

**Document structure:** In general, the fields required in this extension are required to either meet
the *threshold (minimum) requirements* by the CEOS CARD4L specification *or* are required fields in STAC.
Any additional optional field provided may lead to a higher percentage for the CARD4L *target (desired) requirements*.

The column *Field Name* refers to the STAC field names. The column *Req.* refers to the requirement number in the CARD4L specification.

**STAC Extensions:** This extension makes use of a number of existing STAC extensions:

- [EO](https://github.com/stac-extensions/eo)
- [File](https://github.com/stac-extensions/file)
- [Processing](https://github.com/stac-extensions/processing) (optional)
- [Projection](https://github.com/stac-extensions/projection)
- [View](https://github.com/stac-extensions/view)

You have to read the STAC extensions in combination with this extension as this extension just provides
the mapping between the STAC fields and the CARD4L requirements, but this extension doesn't give information
on the data type or an actual detailed description about the fields.

**Additional resources:**

- [Examples](examples/)
  *Please note that parts of the examples are made-up as we can't fulfill all CARD4L requirements yet.*
- [JSON Schema](json-schema/)
  *Please note that assets don't get validated yet.*

## STAC Collections

CARD4L lists a lot of requirements (and fields) that have common values across all generated STAC Items and assets.
Thus, it is **recommended** to provide a STAC Collection for the Items and put common fields (in the STAC Item `properties`)
into [Collection `summaries`](https://github.com/radiantearth/stac-spec/tree/v1.0.0-rc.2/collection-spec/collection-spec.md#collection-fields).
While the STAC Item fields still need to be in the Item, too, you can de-duplicate links and assets by putting common
links once into the STAC Collection links. Also, common assets can be just put once into the STAC Collection using the
STAC extension [Collection Assets](https://github.com/radiantearth/stac-spec/tree/v1.0.0-rc.2/collection-spec/collection-spec.md#assets).
All this is still CARD4L compliant as CARD4L doesn't require all information to be in a single file.

## STAC Items

STAC Items must always be valid, but not all STAC Item requirements are covered here,
only additional requirements and mappings to fulfill the CARD4L requirements are listed here:

| Field Name      | Description                                                  | Req.  |
| --------------- | ------------------------------------------------------------ | ----- |
| stac_extensions | **REQUIRED.** Must contain all extensions used.  See below for details\*. | *n/a* |
| geometry        | **REQUIRED.** The geometry of the acquisition.               | 1.4   |
| bbox            | **REQUIRED.** The bounding box of the acquisition.           | 1.4   |

\* The following values for `stac_extensions` apply:

| Value                                                        | Required  |
| ------------------------------------------------------------ | ---- |
| `https://stac-extensions.github.io/card4l/v1.0.0/optical/schema.json` | ✓    |
| `https://stac-extensions.github.io/eo/v1.0.0/schema.json` | ✓ |
| `https://stac-extensions.github.io/file/v1.0.0/schema.json`  | ✓   |
| `https://stac-extensions.github.io/processing/v1.0.0/schema.json` | ✗   |
| `https://stac-extensions.github.io/projection/v1.0.0/schema.json` | ✓  |
| `https://stac-extensions.github.io/view/v1.0.0/schema.json`  | ✓    |

### STAC Item Properties

#### CARD4L

| Field Name                         | Data Type | Description                                                  | Req.  |
| ---------------------------------- | --------- | ------------------------------------------------------------ | ----- |
| card4l:specification               | string    | **REQUIRED.** The CARD4L specification implemented, either `SR` (Optical, Surface Reflectance) or `ST` (Optical, Surface Temperature). | *n/a* |
| card4l:specification_version       | string    | **REQUIRED.** The CARD4L specification version. Currently always `5.0`. | *n/a* |
| card4l:northern_geometric_accuracy | number    | An estimate of the northern geometric accuracy in meters.    | 1.8   |
| card4l:eastern_geometric_accuracy  | number    | An estimate of the eastern geometric accuracy in meters.     | 1.8   |

Note that all these fields here are aligned with the CARD4L SAR extension.

#### Common Metadata

| Field Name     | Description                                                  | Req.  |
| -------------- | ------------------------------------------------------------ | ----- |
| license        | Recommended to be specified in a STAC Collection.            | *n/a* |
| datetime       | **REQUIRED.** The time of the acquisition, usually the central timestamp between `start_datetime` and `end_datetime`. | 1.3   |
| start_datetime | Start time of the acquisition.                               | 1.3   |
| end_datetime   | End time of the acquisition.                                 | 1.3   |
| instruments    | **REQUIRED.**                                                | 1.9   |
| constellation  | Constellation name in lower-case. Only if part of a constellation, e.g. `sentinel-2` for Sentinel 2A and 2B. Can often be derived from `platform`. | (1.9) |
| platform       | Platform name in lower-case. Use a specific name such as `sentinel-2a` if part of constellation. MUST NOT duplicate `constellation`. | (1.9) |

#### EO (Electro-Optical)

| Field Name     | Description                                                  | Req.   |
| -------------- | ------------------------------------------------------------ | ------ |
| eo:cloud_cover |                                                              | 1.17   |
| eo:bands       | You may include all bands specified in the assets again as an overview. | (1.10) |

#### Processing

| Field Name          | Description                                                  | Req.        |
| ------------------- | ------------------------------------------------------------ | ----------- |
| processing:facility |                                                              | 1.15        |
| processing:software | String likely needs to be split into software name and version number. | 1.13 / 1.15 |
| processing:lineage  | Additional processing information and parameters, e.g. a description of the processing chain. | 1.15        |

See also the [notes](#notes) regarding the requirements 1.13 and 1.15 for a better understanding of how to use the fields best.

#### Projection

The metadata MUST specify the coordinate reference system (1.5) and MAY specify the map projection (1.6) through
either `proj:epsg` or one of the alternatives.

| Field Name                | Description                                                  | Req.      |
| ------------------------- | ------------------------------------------------------------ | --------- |
| proj:epsg                 | **REQUIRED.** This is required by STAC. If there's no suitable EPSG code, set this to `null` and add either `proj:wkt2` or `proj:projjson`. | 1.5 / 1.6 |
| proj:wkt2 / proj:projjson | One of `proj:wkt2` or `proj:projjson` is **required** if `proj:epsg` is set to `null`. | 1.5 / 1.6 |

#### View

| Field Name           | Description                                                  | Req. |
| -------------------- | ------------------------------------------------------------ | ---- |
| view:off_nadir       | The average off-nadir angle, for per-pixel angles. Convert to degree, if required. | *n/a* |
| view:incidence_angle | **REQUIRED.** The average incidence angle, for per-pixel angles, refer to the asset with the key `incidence-angle`. Convert to degree, if required. | \*   |
| view:azimuth         | **REQUIRED.** The average azimuth angle, for per-pixel angles, refer to the asset with the key `azimuth`. Convert to degree, if required. | \*   |
| view:sun_azimuth     | **REQUIRED.** The average sun azimuth angle, for per-pixel angles, refer to the asset with the key `sun-azimuth`. Convert to degree, if required. | \*   |
| view:sun_elevation   | **REQUIRED.** The average sub elevation angle, for per-pixel angles, refer to the asset with the key `sun-elevation`. Convert to degree, if required. | \*   |

\* = Requirement 2.8 for Surface Temperature (ST) / 2.11 for Surface Reflectance (SR)

### STAC Item Links

| Relation Type             | Description                                                  | Req.                |
| ------------------------- | ------------------------------------------------------------ | ------------------- |
| card4l-document           | **REQUIRED.** Provides at least one link to the CARD4L specification document. Word (media type: `application/vnd.openxmlformats-officedocument.wordprocessingml.document`) and/or PDF (media type: `application/pdf`). | *n/a*               |
| derived_from              | Points back to the source's STAC Item. May be multiple items, if the product is derived from multiple acquisitions. | 1.15                |
| about                     | Link to algorithms used in the generation process. See also the [notes](#notes) regarding req. 1.13. | 1.13                |
| related                   | Link to the sources of auxiliary data used in the generation process. This is **required** if auxiliary data used in the generation process. Excludes DEMs, which use the relation `elevation-model` instead.   | 1.14                |
| access                    | STRONGLY RECOMMENDED. Link to data access information.       | 1.16                |
| sensor-calibration        | Link to the sensor calibration parameters.                   | 1.11                |
| radiometric-accuracy      | Link describing the assessed absolute radiometric uncertainty of the version of the data or product. | 1.12                |
| geometric-correction      | Link to the Geometric Correction algorithm details.          | 1.7                 |
| elevation-model           | Links to the Digital Elevation Models. Preferably links to a STAC Item with additional metadata for the DEMs. | 1.14                |
| geometric-accuracy        | Link to documentation of estimate of absolute localization error. | 1.8                 |
| cloud                     | Link to documentation about the cloud detection.             | 2.5                 |
| cloud-shadow              | Link to documentation about the cloud shadow detection.      | 2.6                 |
| snow-ice                  | Link to documentation about the snow and ice mask.           | 2.7 (ST) / 2.8 (SR) |
| land-water                | Link to documentation about the land and water mask (SR only). | 2.7 (SR)            |
| atmosphere-emissivity     | **REQUIRED.** Link to documentation about corrections for atmosphere and emissivity (ST only). | 3.2 (ST)            |
| measurement-normalization | Link to documentation about measurement normalization (SR only). | 3.3 (SR)            |
| atmospheric-scattering    | **REQUIRED.** Link to documentation about the directional atmospheric scattering algorithms (SR only). | 3.4 (SR)            |
| water-vapor               | **REQUIRED.** Link to documentation about the water vapour corrections (SR only). | 3.5 (SR)            |
| ozone                     | Link to documentation about the ozone corrections (SR only). | 3.6 (SR)            |

### STAC Item Assets

Whether the metadata are provided in a single record relevant to all pixels, or separately for each pixel,
is at the discretion of the data provider. 

The role names specify the values to be used in the Asset's `roles`.
Each of the assets can either be exposed individually or grouped together in any form.
In the latter case the role names can simply be merged to a set of unique role names.
Roles can also be combined for a single file.
For example, a cloud mask which is also including cloud shadows 
can use the roles `cloud` and `cloud-shadow` for a single file.
The `file:values` property can the specify which value(s) correspond to clouds and which value(s) correspond to cloud shadows respectively.
The *italic* role names could be used as the asset's key.

 The **bold** additional properties are required.

| Role Name(s)                           | Additional properties                                        | Description                                                  | Req.                 |
| -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------- |
| (*reflectance* or *temperature*), data | `type`, `created`, **`eo:bands`**, `file:data_type`, `file:byte_order`, `file:bits_per_sample`, **`file:nodata`** | **REQUIRED.** Points to the actual measurements. The value(s) for pixels that do not correspond to an observation must be provided in the property `file:nodata`. | 3.1 / 2.2            |
| *date*, metadata                       | `type`, `file:data_type`, `file:byte_order`, `file:bits_per_sample` | Points to a file with per-pixel acquisition timestamps.      | 1.3                  |
| *incomplete-testing*, metadata         | `type`, **`file:values`**                                    | **REQUIRED.** Points to a file that identifies pixels for which the per-pixel tests have not all been successfully completed. See CARD4L req. 2.3 for details. | 2.3                  |
| *saturation*, metadata                 | `type`, **`file:values`**, `eo:bands`                        | **REQUIRED.** Points to a file that indicates where pixels in the input spectral bands are saturated. If the saturation is given per band, either `eo:bands` or `file:values` is **required** to indicate which pixels are saturated for each spectral band. | 2.4                  |
| *cloud*, metadata                      | `type`, **`file:values`**                                    | **REQUIRED.** Points to a file that indicates whether a pixel is assessed as being cloud. | 2.5                  |
| *cloud-shadow*, metadata               | `type`, **`file:values`**                                    | **REQUIRED.** Points to a file that indicates whether a pixel is assessed as being cloud shadow. | 2.6                  |
| *snow-ice*, metadata                   | `type`, **`file:values`**                                    | Points to a file that indicates whether a pixel is assessed as being snow/ice or not. | 2.7 (ST) / 2.8 (SR)  |
| *land-water*, metadata                 | `type`, **`file:values`**                                    | Points to a file that indicates whether a pixel is assessed as being land or water. | 2.7 (SR)             |
| *incidence-angle*, metadata            | `type`, `file:data_type`, `file:byte_order`, `file:bits_per_sample`, **`file:unit`** | Points to a file with per-pixel incidence angles. `file:unit` is usually `degree`. | 2.8 (ST) / 2.11 (SR) |
| *azimuth*, metadata                    | `type`, `file:data_type`, `file:byte_order`, `file:bits_per_sample`, **`file:unit`** | Points to a file with per-pixel azimuth angles. `file:unit` is usually `degree`. | 2.8 (ST) / 2.11 (SR) |
| *sun-azimuth*, metadata                | `type`, `file:data_type`, `file:byte_order`, `file:bits_per_sample`, **`file:unit`** | Points to a file with per-pixel sun azimuth angles. `file:unit` is usually `degree`. | 2.8 (ST) / 2.11 (SR) |
| *sun-elevation*, metadata              | `type`, `file:data_type`, `file:byte_order`, `file:bits_per_sample`, **`file:unit`** | Points to a file with per-pixel sun elevation angles. `file:unit` is usually `degree`. | 2.8 (ST) / 2.11 (SR) |
| *terrain-shadow*, metadata             | `type`, **`file:values`**                                    | Points to a file that indicates whether a pixel is not directly illuminated due to terrain shadowing. | 2.9 (SR)             |
| *terrain-occlusion*, metadata          | `type`, **`file:values`**                                    | Points to a file that indicates whether a pixel is not visible to the sensor due to terrain occlusion during off-nadir viewing. | 2.10 (SR)            |
| *terrain-illumination*, metadata       | `type`, `file:data_type`, `file:byte_order`, `file:bits_per_sample` | Points to a file with coefficients used for terrain illumination correction are provided for each pixel. | 2.12 (SR)            |

#### Additional Asset Properties

The following table lists properties that may occur in the assets.
The list doesn't specify which fields apply to which asset and it also doesn't specify which fields are required.
For those details please refer to the ["Additional properties" column in the table above](#stac-item-assets).

| Field Name           | Data Type                                              | Description                                                  | Req.  |
| -------------------- | ------------------------------------------------------ | ------------------------------------------------------------ | ----- |
| type                 | string                                                 | STRONGLY RECOMMENDED. The media type of the file format.     | *n/a* |
| created              | string                                                 | The time of the processing is specified via the `created` property of the asset as specified in the [STAC Common metadata](https://github.com/radiantearth/stac-spec/tree/v1.0.0-rc.2/item-spec/common-metadata.md#date-and-time). | *n/a* |
| eo:bands             | \[[Band Object](https://github.com/stac-extensions/eo/blob/v1.0.0/README.md#band-object)\]         | Bands with at least the following fields included: `name` and `center_wavelength`. Add additional fields such as `full_width_half_max` to better meet the *target (desired) requirements*. See the CARD4L requirement 1.10 for further details. | 1.10  |
| file:data_type       | string                                                 | One of the [Data Types](https://github.com/stac-extensions/file/blob/v1.0.0/README.md#data-types).       | *n/a* |
| file:byte_order      | string                                                 | One of `big-endian` or `little-endian`.                      | *n/a* |
| file:unit            | string                                                 | The unit of the values in the asset, preferably compliant to [UDUNITS-2](https://ncics.org/portfolio/other-resources/udunits2/). | *n/a* |
| file:bits_per_sample | integer                                                | Bits per sample                                              | *n/a* |
| file:nodata          | \[any]                                                 | for data. Value(s) for no-data.                 | 2.2   |
| file:values          | \[[Mapping Object](https://github.com/stac-extensions/file/blob/v1.0.0/README.md#mapping-object)\] | Lists the value that are in the file and describes their meaning. | n/a   |

## Notes

- 1.13: The algorithms can be given either in `processing:software` or as link with relation type `about`.
  One of them is **required** by CARD4L.
- 1.15: STAC only mandates to use `processing:lineage` to describe processing chains,
  but you may also include or link to a more machine-readable processing chain description such as
  a Dask graph, an openEO process or a SNAP graph.
  It is recommended to use the field name `card4l:processing_chain`.
- 1.17: Other data quality flags than `eo:cloud_cover` should be set.
- 2.13 (SR): CARD4L lists no specific requirements thus it's missing in this document, too.
- 3.2 (SR) / 3.3 (ST): Measurement Uncertainty is not required and it was not clear in which form this should be provided.
  Also the CARD4L specification states for SR that
  
  > "\[i]n current practice, users determine fitness for purpose based on knowledge of the lineage of the data,
  > rather than on a specific estimate of measurement uncertainty."
  
  Thus this requirement is not captured in this document.
- 3.3 (SR): Measurement Normalisation is not required and it was not clear in which form this should be provided.
  Thus this requirement is not captured in this document, but we have added a link relation type to link to information on measurement normalisation.
- 4.1 (SR): This requirement doesn't list any direct requirements for the metadata.

# STAC CARD4L SAR Extension Specification

- **Title:** CARD4L SAR
- **Identifier:** <https://stac-extensions.github.io/card4l/v1.0.0/sar/schema.json>
- **Field Name Prefix:** card4l (shared with the CARD4L Optical Extension)
- **Scope:** Item
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr

This extension specifies how to create [STAC](https://github.com/radiantearth/stac-spec) Items that
comply to the [CEOS CARD4L](http://ceos.org/ard/) product family specification for either
- *SAR Normalized Radar Backscatter* (NRB) products in version 5.5
  ([PDF](https://ceos.org/ard/files/PFS/NRB/v5.5/CARD4L-PFS_NRB_v5.5.pdf),
  [Word](https://ceos.org/ard/files/PFS/NRB/v5.5/CARD4L-PFS_NRB_v5.5.docx))
  *or*
- *SAR Polarimetric Radar* (POL) products in version 3.5
  ([PDF](https://ceos.org/ard/files/PFS/POL/v3.5/CARD4L-PFS_Polarimetric_Radar-v3.5.pdf),
  [Word](https://ceos.org/ard/files/PFS/POL/v3.5/CARD4L-PFS_Polarimetric_Radar-v3.5.docx)).

The following CARD4L metadata specifications lists requirements and maps them to a proposal of XML tags.
We will refer to the included XML Tags throughout this document:

- *SAR Normalized Radar Backscatter* (NRB) metadata specification
  ([XLSX](https://ceos.org/ard/files/PFS/NRB/v5.5/CARD4L_METADATA-spec_NRB-v5.5.5.xlsx))
- *SAR Polarimetric Radar* (POL) metadata specification
  ([XLSX](https://ceos.org/ard/files/PFS/POL/v3.5/CARD4L_METADATA-spec_POL-v3.5.5.xlsx)).

**Document structure:** In general, the fields required in this extension are required to either meet
the *threshold (minimum) requirements* by the CEOS CARD4L metadata specification *or* are required fields in STAC.
Any additional optional field provided may lead to a higher percentage for the CARD4L *target (desired) requirements*.

The column *Field Name* refers to the STAC field names.
The column *XML Tag* refers to the XML tag proposed in the CARD4L metadata specification documents.
*Src* and *Prod* define whether they apply for the STAC Item of the Source and Product respectively
and give the requirement number in the CARD4L documents.

- ✓ = Applies
- (✓) = Not mentioned in CEOS CARD4L requirements, but recommended to be added, for example for better data discovery through STAC.
- ✗ = Does not apply

**STAC Extensions:** This extension makes use of a number of existing STAC extensions:

- [File](https://github.com/stac-extensions/file)
- [Processing](https://github.com/stac-extensions/processing)
- [Projection](https://github.com/stac-extensions/projection)
- [Raster](https://github.com/stac-extensions/raster)
- [SAR](https://github.com/stac-extensions/sar)
- [Satellite](https://github.com/stac-extensions/sat)
- [View](https://github.com/stac-extensions/view) (optional)

You have to read the STAC extensions in combination with this extension as this extension just provides
the mapping between the STAC fields and the CARD4L requirements, but this extension doesn't give information
on the data type or an actual detailed description about the fields.

**Additional resources:**

- [Examples](examples/)

  *Please note that the examples are made-up and as such are not real-world examples.*
- JSON Schemas:
  - [Source](json-schema/source.json)
  - [Product](json-schema/product.json)
  
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

CARD4L requires metadata for both source data (*Src*) and the product (*Prod*) which STAC can better deliver in two (or more) separate files.
This extension assumes that at least one STAC Item for the source is available and follows this extension or
at least one new STAC Item for the source data will be created following this extension.
The number of source files (i.e. the number in `NumberOfAcquisitions` / the number of `SourceAttributes` tags in XML)
indicate the number of source STAC Items required. See also the [`derived_from` link relation type](#stac-item-links) below.
In principle, all metadata for the source data could also be provided in a (proprietary) format like XML and linked to from the product STAC Item,
but this case will not be explained in-depth in this extension.

STAC Items must always be valid, but not all STAC Item requirements are covered here,
only additional requirements and mappings to fulfill the CARD4L requirements are listed here:

| Field Name      | Src     | Prod    | XML Tag                                                      | Description                                                  |
| --------------- | ------- | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| stac_extensions | ✓       | ✓       | *n/a*                                                        | **REQUIRED.** Must contain all extensions used. See below for details\*. |
| id              | ✓ 1.6.6 | ✓       | `ProductID`                                                  | **REQUIRED.**                                                |
| geometry        | ✓ 1.6.7 | ✓ 1.7.6 | `SourceGeographicalExtent` (Src), `ProductGeographicalExtent` (Prod) | **REQUIRED.**                                                |
| bbox            | ✓ 1.6.7 | ✓ 1.7.5 | derived from `geometry` (Src), `ProductBoundingBox` (Prod)   | **REQUIRED.** This is the WGS84 variant of the bbox. If the product is provided in another CRS, `proj:bbox` must provide the bounding box in the native CRS (see [below](#projection)). |

\* The following values for `stac_extensions` apply:

| Value                                                              | Src  | Prod |
| ------------------------------------------------------------------ | ---- | ---- |
| `https://stac-extensions.github.io/card4l/v1.0.0/sar/source.json`  | ✓    | ✗    |
| `https://stac-extensions.github.io/card4l/v1.0.0/sar/product.json` | ✗    | ✓    |
| `https://stac-extensions.github.io/file/v2.0.0/schema.json`        | ✗    | ✓    |
| `https://stac-extensions.github.io/processing/v1.1.0/schema.json`  | ✓    | ✓    |
| `https://stac-extensions.github.io/projection/v1.0.0/schema.json`  | (✓)  | ✓    |
| `https://stac-extensions.github.io/raster/v1.1.0/schema.json`      | ✗    | ✓    |
| `https://stac-extensions.github.io/sar/v1.0.0/schema.json`         | ✓    | ✓    |
| `https://stac-extensions.github.io/sat/v1.0.0/schema.json`         | ✓    | (✓)  |
| `https://stac-extensions.github.io/view/v1.0.0/schema.json`        | ✓    | ✗    |

### STAC Item Properties

#### CARD4L

| Field Name                             | Src         | Prod        | XML Tag                                                      | Data Type                                               | Description                                                  |
| -------------------------------------- | ----------- | ----------- | ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ |
| card4l:specification                   | ✓ 1.4 / 1.3 | ✓ 1.4 / 1.3 | `DocumentIdentifier` / `Product`, attribute `type` (Prod)    | string                                                  | **REQUIRED.** The CARD4L specification implemented, either `NRB` (SAR, Normalized Radar Backscatter) or `POL` (SAR, Polarimetric Radar). |
| card4l:specification_version           | ✓ 1.4       | ✓ 1.4       | `DocumentIdentifier`                                         | string                                                  | **REQUIRED.** The CARD4L specification version. Currently always `5.5` for `NRB` and `3.5` for `POL`. |
| card4l:beam_id                         | ✓ 1.6.4     | ✗           | `BeamID`                                                     | string                                                  | **REQUIRED.**                                                |
| card4l:orbit_data_source               | ✓ 1.6.5     | (✓)         | `OrbitDataSource`                                            | string                                                  | **REQUIRED for *Src*.** For example `predicted`, `definitive`, `precise` or `downlinked`. Applies to *Prod*, if additional orbit correction has been applied. |
| card4l:orbit_mean_altitude             | ✓ 1.6.5     | ✗           | `OrbitMeanAltitude`                                          | number                                                  | Platform (mean) altitude in meters (m). |
| card4l:source_processing_parameters    | ✓ 1.6.6     | ✗           | *various, see below*                                         | [Source Processing Object](#source-processing-object)   | Additional relevant processing parameters, e.g., Range- and Azimuth Look Bandwidth and LUT applied. If not available in machine-readable form, can also be specified in `processing:lineage`. |
| card4l:source_geometry                 | ✓ 1.6.7     | ✗           | `SourceDataGeometry`                                         | string                                                  | **REQUIRED.** One of `slant-range` or `ground-range`. |
| card4l:incidence_angle_near_range      | ✓ 1.6.7     | ✗           | `IncAngleNearRange`                                          | number                                                  | **REQUIRED.** In degrees. |
| card4l:incidence_angle_far_range       | ✓ 1.6.7     | ✗           | `IncAngleFarRange`                                           | number                                                  | **REQUIRED.** In degrees. |
| card4l:noise_equivalent_intensity      | ✓ 1.6.9     | ✗           | `Estimates` in `NoiseEquivalentIntensity`                    | [Statistics Object](#statistics-object)                 | **REQUIRED.** Fill the object with statistics that are available for the source data, e.g. min, max and mean. Convert each to decibel, if required. |
| card4l:noise_equivalent_intensity_type | ✓ 1.6.9     | ✗           | `NoiseEquivalentIntensity`, attribute `type`                 | string                                                  | **REQUIRED.** One of `beta0`, `sigma0`, or `gamma0`. |
| card4l:noise_removal_applied           | (✓)         | ✓ 3.3       | `NoiseRemovalApplied`                                        | boolean                                                 | **REQUIRED for *Prod*.** Specifies whether noise removal has been applied (`true`) or not (`false`). If set to `true`, a [link with relation type](#stac-item-links) `noise-removal` is **required**, too. |
| card4l:mean_faraday_rotation_angle     | ✓ 1.6.11    | ✗           | `MeanFaradayRotationAngle`                                   | number                                                  | In degrees. |
| card4l:ionosphere_indicator            | ✓ 1.6.12    | ✗           | `IonosphereIndicator`                                        | boolean                                                 | Flag indicating whether the imagery is “significantly impacted” by the ionosphere (`false` - no, `true` – yes). |
| card4l:speckle_filtering               | ✗           | ✓ 1.7.4     | `Filtering`, `FilterApplied`                                 | [Speckle Filter Object](#speckle-filter-object) \| null | **REQUIRED.** Set to `null` if `FilterApplied` would be set to `false`. Otherwise, provide a [Speckle Filter Object](#speckle-filter-object). |
| card4l:pixel_coordinate_convention     | ✗           | ✓ 1.7.8     | `PixelCoordinateConvention`                                  | string                                                  | **REQUIRED.** One of `center` (pixel center), `upper-left` (pixel ULC) or `lower-left` (pixel LLC) |
| card4l:measurement_type                | ✗           | ✓ 3.1       | `BackscatterMeasurement` (NRB)                               | string                                                  | **REQUIRED.** Must be set to `gamma0`.                       |
| card4l:measurement_convention          | ✗           | ✓ 3.1       | `BackscatterConvention` (NRB)                                | string                                                  | **REQUIRED.** Must be set to `linear amplitude` or `linear power`. |
| card4l:conversion_eq                   | ✗           | ✓ 3.2 / 3.1 | `BackscatterConversionEq` (NRB), `ScalingConversionEq` (POL) | string                                                  | **REQUIRED.** Indicate equation to convert from digital numbers (`DN`) to logarithmic decibel scale, see the CARD4L specification (3.1/3.2) for details. |
| card4l:relative_radiometric_accuracy   | ✗           | ✓ 3.5       | `Relative` in `RadiometricAccuracy`                          | number                                                  | Relative accuracy of the Radiometric Terrain Correction in decibel. |
| card4l:absolute_radiometric_accuracy   | ✗           | ✓ 3.5       | `Absolute` in `RadiometricAccuracy`                          | number                                                  | Absolute accuracy of the Radiometric Terrain Correction in decibel. |
| card4l:resampling_method               | ✗           | ✓ 4.1       | `ResamplingMethod`                                           | string                                                  | The resampling method used, e.g. `near`, `bilinear`, `cubic`, etc. (see [GDAL](https://gdal.org/programs/gdalwarp.html#cmdoption-gdalwarp-r) for more) |
| card4l:dem_resampling_method           | ✗           | ✓ 4.2       | `DEMResamplingMethod`                                        | string                                                  | The resampling method used for the DEM, see above for example values. |
| card4l:egm_resampling_method           | ✗           | ✓ 4.2       | `EGMResamplingMethod`                                        | string                                                  | The resampling method used for the EGM, see above for example values. |
| card4l:northern_geometric_accuracy     | ✗           | ✓ 4.3       | `NorthernSTDev` / `NorthernBias` in `GeoCorrAccuracy`        | number                                                  | **REQUIRED.** An estimate of the northern geometric accuracy in meters. |
| card4l:eastern_geometric_accuracy      | ✗           | ✓ 4.3       | `EasternSTDev` / `EasternBias` in `GeoCorrAccuracy`          | number                                                  | **REQUIRED.** An estimate of the eastern geometric accuracy in meters. |

##### Statistics Object

| Field Name    | Type   | Description                                        |
| ------------- | ------ | -------------------------------------------------- |
| mean          | number | mean value of all the pixels in the band           |
| minimum       | number | minimum value of the pixels in the band            |
| maximum       | number | maximum value of the pixels in the band            |
| ...           | number | Additional statistics (see below)                  |

The three properties above are the fields for mean, min and max. values.
You can choose to provide other fields depending on what is available for your source data.
Nevertheless, you have to provide at least one property in the Statistics Object. 

##### Speckle Filter Object

The following fields are all specified in CARD4L requirement 1.7.4. 
It is **required** to add all speckle filter parameters to this object.

| Field Name       | Data Type | XML Tag           | Description                        |
| ---------------- | --------- | ----------------- | ---------------------------------- |
| type             | string    | `FilterType`      | **REQUIRED.**                      |
| window_size_col  | integer   | `WindowSizeCol`   |                                    |
| window_size_line | integer   | `WindowSizeLine`  |                                    |
| ...              | ...       | `OtherParameters` | Add all speckle filter parameters. |

##### Source Processing Object

Additional relevant processing parameters, 
e.g., Range- and Azimuth Look Bandwidth and LUT applied. 
If not available in machine-readable form, can also be specified in `processing:lineage`. 
The following fields are all suggested in CARD4L requirement 1.6.6. 
None of the fields is required.

| Field Name             | Data Type | XML Tag                | Description                                                  |
| ---------------------- | --------- | ---------------------- | ------------------------------------------------------------ |
| lut_applied            | string    | `lutApplied`           |                                                              |
| range_look_bandwidth   | \[number] | `RangeLookBandwidth`   | Range Look Bandwidth per swath in hertz (Hz).                |
| azimuth_look_bandwidth | \[number] | `AzimuthLookBandwidth` | Azimuth Look Bandwidth per swath in hertz (Hz).              |
| ...                    | ...       | *n/a*                  | Add all source data processing parameters.                   |

#### Common Metadata

| Field Name     | Src     | Prod    | XML Tag                                         | Description                                                  |
| -------------- | ------- | ------- | ----------------------------------------------- | ------------------------------------------------------------ |
| license        | ✓ 1.3   | ✓ 1.3   | `Product`, attribute `Copyright`                | Recommended to be specified in a STAC Collection.            |
| datetime       | ✓       | ✓       | *n/a*                                           | **REQUIRED.** Recommended to set to the central timestamp between `start_datetime` and `end_datetime`. |
| start_datetime | ✓ 1.6.3 | ✓ 1.5   | `FirstAcquistionDate` (Prod), `StartTime` (Src) | **REQUIRED.** Start time of the first acquisition.           |
| end_datetime   | ✓ 1.6.3 | ✓ 1.5   | `LastAcquistitionDate` (Prod), `EndTime` (Src)  | **REQUIRED.** End time of the last acquisition.              |
| instruments    | ✓ 1.6.2 | (✓)     | `Instrument`                                    | **REQUIRED for *Src*.** Check STAC for potential values, example: `c-sar` for Sentinel-1 |
| constellation  | ✓ 1.6.2 | (✓)     | *n/a*                                           | Constellation name in lower-case. Only if part of a constellation, e.g. `sentinel-1` for Sentinel 1A and 1B. Can often be derived from `platform`. |
| platform       | ✓ 1.6.2 | (✓)     | `Satellite`                                     | **REQUIRED for *Src*.** Platform name in lower-case. Use a specific name such as `sentinel-1a` if part of constellation. MUST NOT duplicate `constellation`. |
| gsd            | ✗       | ✓ 1.7.3 | `ProductColumnSpacing` / `ProductRowSpacing`    | **REQUIRED.** Convert to meters, if required. Currently, there's no way to express separate ground sample distances for x and y. |

#### Processing

| Field Name            | Src     | Prod    | XML Tag              | Description                                                  |
| --------------------- | ------- | ------- | -------------------- | ------------------------------------------------------------ |
| processing:facility   | ✓ 1.6.6 | ✓ 1.7.1 | `ProcessingFacility` | **REQUIRED.**                                                |
| processing:level      | ✓ 1.6.6 | ✓ 1.7.1 | *n/a*                | **REQUIRED.** String likely needs to be split into software name and version number. |
| processing:software   | ✓ 1.6.6 | ✓ 1.7.1 | `SoftwareVersion`    | **REQUIRED.** String likely needs to be split into software name and version number. |
| processing:lineage    | ✓       | ✓       | *n/a*                | A human-readable description of the full processing workflow and parameters, e.g. algorithms and corrections applied. |
| processing:expression | ✓       | ✓       | *n/a*                | A machine-readable description of the full processing workflow and parameters, e.g. a Dask graph, an openEO process or a SNAP graph. Alternatively, you can also link to a processing chain with the relation type `processing-expression` (see below). |

#### Projection

| Field Name                | Src  | Prod    | XML Tag                     | Description                                  |
| ------------------------- | ---- | ------- | --------------------------- | -------------------------------------------- |
| proj:epsg                 | (✓)  | ✓ 1.7.9 | `CoordinateReferenceSystem` | **REQUIRED for *Prod*.** See comment below*. |
| proj:wkt2 / proj:projjson | (✓)  | ✓ 1.7.9 | `CoordinateReferenceSystem` | **REQUIRED for *Prod* if `proj:epsg` is `null`.** See comment below*. |
| proj:bbox                 | (✓)  | ✓ 1.7.5 | `ProductBoundingBox`        | **REQUIRED for *Prod* if the native CRS is not WGS84.** This is the bounding box in the native CRS of the product. Can be omitted if the native CRS is WGS84. |

\* For *Prod* the metadata must specify the map projection (see 1.7.9).
`proj:epsg` is **required** by STAC. If there's no suitable EPSG code, set the field to `null`,
which then requires one of `proj:wkt2` or `proj:projjson` to be specified.

#### SAR

| Field Name                  | Src     | Prod  | XML Tag                                                      | Description                                                  |
| --------------------------- | ------- | ----- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| sar:instrument_mode         | ✓ 1.6.4 | (✓)   | `ObservationMode`                                            | **REQUIRED for *Src*.**                                      |
| sar:frequency_band          | ✓ 1.6.4 | (✓)   | `RadarBand`                                                  | **REQUIRED for *Src*.**                                      |
| sar:center_frequency        | ✓ 1.6.4 | ✗     | `RadarCenterFrequency`                                       | **REQUIRED.** In hertz (Hz).                                 |
| sar:polarizations           | ✓ 1.6.4 | (✓)   | `Polarizations`                                              | **REQUIRED for *Src*.**                                      |
| sar:product_type            | ✓ 1.6.6 | ✓ 3.1 | `ProductLevel` (Src), `Measurements`, attribute `type` (Prod, POL only) | **REQUIRED.** *Src*: Find suitable [product type in the SAR extension](https://github.com/stac-extensions/sar/blob/v1.0.0/README.md#item-properties). *Prod*: `NRB` for Normalized Radar Backscatter products, `COVMAT` for Normalized Radar Covariance Matrix products or `PRD` for Polarimetric Radar Decomposition products. |
| sar:observation_direction   | ✓ 1.6.4 | ✗     | `AntennaPointing`                                            | **REQUIRED.** Lower-case                                     |
| sar:looks_azimuth           | ✓ 1.6.6 | ✗     | `AzimuthNumberOfLooks`                                       | **REQUIRED.**                                                |
| sar:looks_range             | ✓ 1.6.6 | ✗     | `RangeNumberOfLooks`                                         | **REQUIRED.**                                                |
| sar:pixel_spacing_azimuth   | ✓ 1.6.7 | ✗     | `AzimuthPixelSpacing`                                        | **REQUIRED.** In meters (m).                                 |
| sar:pixel_spacing_range     | ✓ 1.6.7 | ✗     | `RangePixelSpacing`                                          | **REQUIRED.** In meters (m).                                 |
| sar:resolution_azimuth      | ✓ 1.6.7 | ✗     | `AzimuthResolution`                                          | **REQUIRED.** In meters (m).                                 |
| sar:resolution_range        | ✓ 1.6.7 | ✗     | `RangeResolution`                                            | **REQUIRED.** In meters (m).                                 |
| sar:looks_equivalent_number | ✓ 1.6.9 | ✗     | `EquivalentNumberOfLooks`                                    |                                                              |

#### Satellite

| Field Name         | Src     | Prod | XML Tag         | Description                        |
| ------------------ | ------- | ---- | --------------- | ---------------------------------- |
| sat:orbit_state    | ✓ 1.6.5 | (✓)  | `PassDirection` | **REQUIRED for *Src*.** Lower-case |
| sat:relative_orbit | ✓       | (✓)  | *n/a*           |                                    |
| sat:absolute_orbit | ✓       | (✓)  | *n/a*           |                                    |

#### View

| Field Name           | Src     | Prod | XML Tag           | Description                                                  |
| -------------------- | ------- | ---- | ----------------- | ------------------------------------------------------------ |
| view:azimuth         | ✓ 1.6.5 | ✗    | `PlatformHeading` | In degrees. STAC uses the range 0 to 360°, so if you use the range -180 - 180, you need to add +180 for conversion. |
| view:incidence_angle | ✓ 1.6.5 | ✗    | *n/a*             | In degrees. Center between `card4l:incidence_angle_near_range` and `card4l:incidence_angle_far_range`. This is the sensor incidence angle. For per-pixel incidence angles, refer to the asset with the role `local-incidence-angle`. |

### STAC Item Links

| Relation Type                  | Src      | Prod                | XML Tag                    | Description                                                  |
| ------------------------------ | -------- | ------------------- | -------------------------- | ------------------------------------------------------------ |
| card4l-document                | ✓        | ✓ 1.4               | `DocumentIdentifier`       | **REQUIRED.** Instead of the document identifier, provide URLs to the Word (media type: `application/vnd.openxmlformats-officedocument.wordprocessingml.document`) and PDF (media type: `application/pdf`) document. |
| derived_from                   | ✗        | ✓ 1.6               | *n/a*                      | **REQUIRED.** Points back to the source's STAC Item, which must comply to the *Src* requirements. May be multiple items, if the product is derived from multiple acquisitions. The number of acquisitions (`NumberOfAcquisitions`) is the number of links with this relation type. |
| about                          | (✓)      | (✓)                 | *n/a*                      | URL to other documentation, e.g. algorithms used in the generation process. |
| related                        | ✗        | ✓ 1.7.2             | `AncillaryData`            | URL to the sources of ancillary or auxiliary data used in the generation process. Excludes DEMs, which use the relation `elevation-model` instead. |
| access                         | ✓ 1.6.1  | ✓ 1.7.1             | *n/a*                      | URL to data access information.                             |
| satellite                      | ✓ 1.6.2  | ✗                   | `SatelliteReference`       | URL to information about the satellite (constellation) the data originates from. |
| performance-indicators         | ✓ 1.6.9  | ✗                   | `PerformanceIndicators`    | URL to performance indicators on data intensity mean noise level. |
| state-vectors                  | ✓ 1.6.5  | ✗                   | `StateVector`              | URL to an orbit data file containing at least 5 state vectors. |
| sensor-calibration             | ✓ 1.6.8  | ✗                   | `SensorCalibration`        | URL to the sensor calibration parameters.                   |
| pol-cal-matrices               | ✓ 1.6.10 | ✗                   | `PolCalMatrices`           | URL to the complex-valued polarimetric distortion matrices with the channel imbalance and the cross-talk applied for the polarimetric calibration. |
| referenced-faraday-rotation    | ✓ 1.6.11 | ✗                   | `ReferenceFaradayRotation` | URL to the method or paper used to derive the estimate for the mean Faraday rotation angle. |
| noise-removal                  | ✗        | ✓ 3.3               | `NRAlgorithm`              | **REQUIRED if noise removal has been applied.** URL to the noise removal algorithm details. |
| radiometric-terrain-correction | ✗        | ✓ 3.4               | `RTCAlgorithm`             | **REQUIRED.** URL to the Radiometric Terrain Correction algorithm details. |
| radiometric-accuracy           | ✗        | ✓ 3.5               | `RadAccuracyReference`     | URL describing the radiometric uncertainty of the product. |
| geometric-correction           | ✗        | ✓ 4.1               | `GeoCorrAlgorithm`         | URL to the Geometric Correction algorithm details.          |
| elevation-model                | ✗        | ✓ 4.2               | `DEMReference`             | **REQUIRED.** URL to the Digital Elevation Model (DEM) used for Geometric Terrain Correction. Preferably URLs to a STAC Item with additional metadata for the DEM such as the line-spacing, column-spacing, horizontal and vertical accuracy. |
| surface-model                  | ✗        | ✓ 4.2               | `DEMReference`             | **REQUIRED.** URL to the Digital Surface Model (DSM) used for Geometric Terrain Correction. Preferably URLs to a STAC Item with additional metadata for the DSM (see above). |
| earth-gravitational-model      | ✗        | ✓ 4.2               | `EGMReference`             | **REQUIRED.** URL to the Earth Gravitational Model (EGM) used for Geometric Correction. Preferably URLs to a STAC Item with additional metadata for the EGM (see above). |
| geometric-accuracy             | ✗        | ✓ 4.3               | `GeometricCorrAccuracy`    | URL to documentation of estimate of absolute localization error. |
| gridding-convention            | ✗        | ✓ 4.4               | `GriddingConvention`       | URL that describes the gridding convention used. |
| processing-expression          | ✓ 1.6.6  | ✓ 1.7.1 / 4.1 / ... | *n/a*                      | A processing chain (or script) that describes how the data has been processed. |

Note: CARD4L XML files also allow DOIs to be given instead of URLs. DOIs must be converted to URLs for STAC!

### STAC Item Assets

Whether the metadata are provided in a single record relevant to all pixels, or separately for each pixel, is at the discretion of the data provider. 

The role names specify the values to be used in the Asset's `roles`.
Each of the assets can either be exposed individually or grouped together in any form.
In the latter case the role names can simply be merged to a set of unique role names.
Roles can also be combined for a single file.
The *italic* role names could be used as the asset's key.

**All additional properties are required, except the properties in *italic*.**

| Role Name(s)                          | Src  | Prod        | XML Tag                      | Additional properties                                        | Description                                                  |
| ------------------------------------- | ---- | ----------- | ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| *card4l*, metadata                    | ✗    | ✓ 2.1       | *n/a*                        | `type`                                                       | Points to a metadata XML file that follows the CARD4L metadata specification. Media type: `application/xml` |
| *data-mask*, metadata                 | ✗    | ✓ 2.2       | `DataMask`                   | `type`, `raster:bands` (with `values` \[see notes\], `nodata`, `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`* | **REQUIRED.** Points to the data mask file.                  |
| *contributing-area*, metadata         | ✗    | ✓ 2.3       | `LocalContributingArea`      | `type`, `raster:bands` (with `unit`, `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`* | Points to the normalized scattering area file. |
| *local-incidence-angle*,  metadata    | ✗    | ✓ 2.4       | `LocalIncAngle`              | `type`, `raster:bands` (with `unit`, `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`* | **REQUIRED.** Points to the DEM-based local incidence angle file. `unit` is usually `degree`. |
| *ellipsoid-incidence-angle*, metadata | ✗    | ✓ 2.5       | `EllipsoidIncAngle`          | `type`, `raster:bands` (with `unit`, `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`*, *`card4l:ellipsoidal_height`* | `unit` is usually `degree`.                                  |
| *noise-power*, card4l, metadata       | ✗    | ✓ 2.6       | `NoisePower`                 | `type`, `raster:bands` (with `unit`, `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`* | **REQUIRED if noise removal was applied.** `unit` is usually NESZ or NEBZ. |
| *gamma-sigma-ratio*, metadata         | ✗    | ✓ 2.7       | `GammaToSigmaRatio`          | `type`, `raster:bands` (with `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`* |                                                              |
| (*acquisition-id* or *date-offset*), metadata | ✗ | ✓ 2.8  | `AcquisitionIDImage`         | `type`, `raster:bands` (with `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`* | **REQUIRED for multi-source products only.** Acquisition ID or acquisition date for each pixel is identified (see 2.8 for details). |
| (*elevation-model* or *surface-model* or *earth-gravitational-model*), metadata | ✗ | ✓ 2.9 | `PerPixelDEM` | `type`, `raster:bands` (with `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`* | Per-pixel DEM/DSM/EGM as used in product generation. |
| *backscatter*, data                   | ✗    | ✓ 3.1 (NRB) | `BackscatterMeasurementData` | `type`, `created`, `sar:polarizations`, `raster:bands` (with `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`*, `proj:shape`, *`card4l:border_pixels`* | **REQUIRED for *NRB*.** Points to the backscatter measurements for the polarizations specified in `sar:polarizations`. |
| (*covmat* and/or *prd*), data         | ✗    | ✓ 3.1 (POL) | `Measurements`               | `type`, `created`, `sar:polarizations` (CovMat only), `raster:bands` (with `data_type` and `bits_per_sample`), `file:byte_order`, *`file:header_size`*, `proj:shape`, *`card4l:border_pixels`* | **REQUIRED for *POL*.** Points to the Normalized Polarimetric Radar Covariance Matrix (CovMat) *or* the Polarimetric Radar Decomposition (PRD) |

#### Additional Asset Properties

The following table lists properties that may occur in the assets.
The list doesn't specify which fields apply to which asset and it also doesn't specify which fields are required.
For those details please refer to the ["Additional properties" column in the table above](#stac-item-assets).

| Field Name                | Src     | Prod    | XML Tag                                         | Data Type                                                    | Description                                                  |
| ------------------------- | ------- | ------- | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| type                      | (✓)     | ✓       | `DataFormat`                                    | string                                                       | The media type of the file format.                           |
| created                   | ✓ 1.6.6 | ✓ 1.7.1 | `ProcessingDate` (Src), `ProcessingTime` (Prod) | string                                                       | The time of the processing is specified via the `created` property of the asset as specified in the [STAC Common metadata](https://github.com/radiantearth/stac-spec/tree/v1.0.0/item-spec/common-metadata.md#date-and-time). |
| sar:polarizations         | (✓)     | ✓       | *n/a*                                           | \[string\]                                                   | The polarization(s) of the asset.                            |
| file:header_size          | ✗       | ✓ 1.7.7 | `HeaderSize`                                    | integer                                                      | File header size in bytes (**required** if applicable to the file format). |
| file:byte_order           | ✗       | ✓       | `ByteOrder`                                     | string                                                       | One of `big-endian` or `little-endian`                       |
| raster:bands              | ✗       | ✓       | see below | \[[Raster Band Object](https://github.com/stac-extensions/raster/blob/v1.1.0/README.md#raster-band-object)\] | Bands with at least the required fields for the corresponding asset role (see above and below). |
| card4l:ellipsoidal_height | ✗       | ✓       | `EllipsoidalHeight`                             | number                                                       | Indicate which ellipsoidal height was used, in meters.       |
| card4l:border_pixels      | ✗       | ✓ 1.7.7 | `NumBorderPixels`                               | integer                                                      | Number of border pixels (**required** only if applicable). |
| proj:shape                | ✗       | ✓ 1.7.7 | `NumberLines`, `NumberPixelsPerLine`            | \[integer]                                                   | The shape of the asset.                                      |

##### raster:bands

| Field Name      | Src  | Prod  | XML Tag                                      | Data Type                                                    | Description                                                  |
| --------------- | ---- | ----- | -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| data_type       | ✗    | ✓     | `DataType`                                   | string                                                       | One of the [Data Types](https://github.com/stac-extensions/raster/blob/v1.1.0/README.md#data-types). |
| bits_per_sample | ✗    | ✓     | `BitsPerSample`                              | integer                                                      | Bits per sample, e.g. 8, 16, 32, ...                         |
| unit            | ✗    | ✓     | `SampleType`                                 | string                                                       | The unit of the values in the asset, preferably compliant to [UDUNITS-2](https://ncics.org/portfolio/other-resources/udunits2/). |
| values          | ✗    | ✓ 2.2 | `ValidData` and `InvalidData` in `BitValues` | \[[Mapping Object](https://github.com/stac-extensions/file/blob/v1.0.0/README.md#mapping-object)\] | Specify value(s) for valid and invalid data separately.      |
| nodata          | ✗    | ✓ 2.2 | `NoData` in `BitValues`                      | \[any]                                                       | Value(s) for no-data.                                        |

## Notes

- 1.6.1 / 1.7.1: `SourceDataRepository` and `RepositoryURL` are covered by STAC link structures. 
  All CARD4L compliant STAC Catalog are **required** to make intensive use of STAC link relation types such as
  `root`, `parent`, `child`, `item` and `collection`.
- 1.6.9: `PeakSideLobeRatio` and `IntegratedSideLobeRatio` are *recommended* to be included in the resource linked to with the relation type
  [`performance-indicators`](#stac-item-links).
- 1.7.9: The CARD4L specification is not clear whether a string representation of a CRS is required
  or whether an EPSG code would fullfil the requirement already.
- 2.2: `raster:bands[*].values` is not standardized yet in STAC, this could change to `file:values`
  or something different with a similar structure in the future.
- 4.4: In the CARD4L metadata specification the Gridding Convenvtion details seem to be required although not required in the textual specification.
  We assume it is not required.

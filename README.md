# STAC CARD4L Extensions

This repository contains two STAC extensions that specifies how to create STAC Items (and Collections)
that comply to the [CEOS CARD4L](http://ceos.org/ard/) product family specifications for either:
- Optical - Surface Reflectance
- Optical - Surface Temperature
- SAR - Normalized Radar Backscatter
- SAR - Polarimetric Radar

There are two STAC extensions, one for Optical and one for SAR:
- [Optical](optical/README.md)
- [SAR](sar/README.md)

## Disclaimer

The extensions are not officially approved by CEOS, but there's a desire to get CEOS approval.
Nevertheless, the extensions have been discussed with CEOS.
The STAC extensions don't allow to fulfill 100% target compliance (but most of it),
but they surely allow to be 100% threshold compliant.

The extensions were written as part of the project [openEO Platform](https://openeo.cloud)
with funding from [ESA](https://www.esa.int).
Contact [Matthias Mohr](https://github.com/m-mohr) for details.

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

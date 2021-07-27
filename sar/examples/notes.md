These examples are drafts and certainly not complete yet!

Source:
- BBox and Geometry are missing as they are not given in the XML and I can't access the source files right now.
- NoiseEquivalentIntensity is a range in XML, but a single number in CARD4L - took the mean for now. Are negative values valid here?
- SatelliteName is used by Sinergise's XML file, instead of Satellite
- STAC ext. probably delivers not enough details about 1.6.9 (Performance Indicators, see also note in STAC ext.)
- view:azimuth is negative. What meaning does this have? Removed minus in the examples.

Product:
- raster:bands\[*].values is not standardized yet, could change to file:values or something different with a similar structure.
- GriddingConvention should be one of "center", "ul" or "lr", but the XML contains a URL. Randomly set to "center" for now.
- gsd is an estimate, needs proper conversion from degree.
- GeoCorrAccuracy uses completely different child fields in XML than standardized in CEOS XML spec, attempted to convert them correctly.

Available in XML and specified by CEOS, but not used in STAC:
- Target requirements for DEM
- Potentially other target requirements, which are also missing in the XML file

Available in XML, but NOT specified by CEOS and also not used in STAC:
- ProductGeometry
- NoiseReference
- GridName
- DEMOversamplingFactor
- RadiometricAccuracy
- EGMReference and related fields
- GeoCorrMethod

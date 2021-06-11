# ogc-collection
Experiment to make two 'OGC Building Blocks'. One is the 'Collection', to be able to use that JSON to link to a static file, serving as its metadata. And 
the other is the Records JSON, extracting just the core content out of OARec, that could be used statically, or could go straight into a WFS with no additional
API capabilities.

## In this repo

 * [OGC Collection Spec](ogc-collection-spec.md) - first attempt at extracting the Collection JSON from OGC API to be a standalone file that 
 can be used statically.
 * [OGC Records GeoJSON spec](ogc-record-geojson-spec.md) - first attempt at pulling out the Record content model from OGC API - Records.
 * [Examples](examples/) - Matched examples showing both Record and Collection instantiations of the same data.

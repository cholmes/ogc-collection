# OGC Collection and Record Building Blocks

This repository provides two experimental specifications. This experiment has a few related things going on:

* Trial smaller 'building block' specifications that are more accessible than the standard OGC format, surfacing key information from the core specs in smaller packages.
* Cleanly define core OGC content as JSON that is not tied to an API, so it can be used as a static resource on a cloud bucket that other API's can crawl & ingest.
* Help provide clarity on how STAC fits in with OARecords.

These points will be expanded on below. The repository contains two 'building block specifications. The first is the 'OGC Collection' which extracts the core JSON
content of a Collection from the OGC API, where it was originally specified as part of Feature, and is also in Common. STAC's 
[Collection](https://github.com/radiantearth/stac-spec/blob/master/collection-spec/collection-spec.md) is also compatible, sharing most of the core fields.

The second is the Records JSON, which just extracts the core content out of OARec (see [this link](https://github.com/opengeospatial/ogcapi-records/blob/master/core/standard/clause_7_core.adoc#response-5) and scroll down to table 8 - it doesn't seem to have a direct link), 
that could be used statically, or could go straight into a WFS with no additional API capabilities.

## In this repo

 * [OGC Collection Spec](ogc-collection-spec.md) - first attempt at extracting the Collection JSON from OGC API to be a standalone file that 
 can be used statically.
 * [OGC Records GeoJSON spec](ogc-record-geojson-spec.md) - first attempt at pulling out the Record content model from OGC API - Records.
 * [Examples](examples/) - Matched examples showing both Record and Collection instantiations of the same data.

## More experiment information

### Building Blocks

The core idea here is to provide 'small pieces, loosely coupled' that can be easily understood by any developer, not just those who are deep in OGC API's. They 
should be short, aiming for ~5 pages, no more than 10. They would not aim to provide all the 'normative' requirements for testing, but should refer to core 
standards that provide those. Initially these would target small bits of functionality that are already in OGC API standards. They could be small OpenAPI snippets
like BBOX, CRS, CQL Geometry filters. Or it could be JSON content, like TileSetMetadata or the OGC Collection proposed here. There is also a space for 'OGC 
Patterns', non-geospatial OpenAPI components that are common in OGC API's, like paging, limit, datetime, etc.

These ideally will be quite easy to read, and to edit. Right now we're using markdown, but asciidoc likely makes more sense as that's the main thing used in OGC, so 
will likely convert these to asciidoc soon.

### Static OGC ('cloud native')

One of the most interesting things to emerge from STAC is the '[static 
stac](https://medium.com/radiant-earth-insights/static-spatiotemporal-asset-catalogs-in-depth-710530934a84#2481)' idea, where STAC JSON can just sit on a cloud 
storage bucket. OGC has some really nice core JSON pieces, but they are tightly coupled to the API definitions. Little actually needs to be changed, but
imiplementors should be able to find a document that clearly describes just the content that can be placed online.

### STAC Items and Records

TODO


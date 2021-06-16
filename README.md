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

One question that has often come up is what is the relationship between STAC and OGC API Records? The two specs in this repo ideally help with an opinionated 
answer to that question. It's been hard to really talk about it without the appropriate primitives in place. 

The proposed primitives in the 'search geospatial data' space are:

* A 'minimal Collection', as defined in OGC API - Features, which is 2 required fields (id and links), and a handful of optional ones (extent, title, description, etc)
* A 'Collection', as the [OGC Collection Spec](ogc-collection-spec.md) in this repo attempts to articulate. This aims to provide enough information to enable
 'collection search'. 
* An STAC Item, which is part of a Collection, that links to some type of geospatial information. A STAC Item is a GeoJSON feature,

STAC provides both Collection and STAC Items, as well as an API to search STAC Items. In STAC there is a clear relationship, where each Item is a member of 
a Collection. To make it concrete, 'Landsat 8' is a collection, while a particular landsat scene is an 'Item'. 

The STAC API provides 'Item search', letting a user include one or more collections in a search (by area, time and/or properties), returning all STAC Items that match it.

It does not have 'Collection search', that would let a user search for collections by area, time and/or properties. This would let them find 'NAIP' in the US, or
an aerial imagery collection in the Netherlands, or a nice global dataset like Sentinel 2. The geometry of these is the area of the entire collection. THis is a 
feature that many STAC users are asking for. 

The STAC core team has been waiting for OARecords API to define that collection level search. But the needs are actually less than a fully defined new API. What
STAC actually needs is mostly the [OGC Records GeoJSON spec](ogc-record-geojson-spec.md) - how to represent a 'Collection' as GeoJSON that can go either directly
into the STAC Features API as a special layer, or is defined as a particular endpoint, but that works with the exact same Features API mechanics as STAC Item
Search (cross collection) or individual collection/items endpoints in STAC's Featuers API's. STAC implementations could choose to use the sort extension, aligning
STAC with OGC, and the Filter (CQL) extension.

For those with deep OGC background, what we're proposing is that STAC API should serve the same role as the ebRIM CSW did in the past, enabling search of millions 
of scenes/granules. And then Collection Search (Records API) should play the role as CSW iso19115 - primarily looking for 'layers', where you'd expect someone
to fill out metadata. These can be imagery collections, but also vector information like 'building footprints' or 'roads'. These would ideally link to a Features 
API. But the standalone [OGC Collection Spec](ogc-collection-spec.md) opens up the potential for 'static collections', where a Catalog crawls geopackages with
Collection JSON sidecars, ingesting them and turning them into Record GeoJSON. And potentially even ingesting their source data into an OGC API - Features 
implementation.

Theoretically we could have some root 'Record' class that both STAC and Collection inherit from, with few required fields. But the value of that is unclear, 
except to say a records API can fit anything. It seems cleaner to just let a features API have 'anything', and a records API is where you'd search iso19115 type
things.

One further question often posed is where n-dimensional data like netCDF and zarr fit in. For the most part those should be in the 'collection' level. It's made
less sense to make them individual STAC Items when it's a big dataset with lots of information. But it depends on how the files are used. One could see a series
of netCDF's that are all ocean temperatures with different time and space ranges, but that fit together into an overall 'collection', and in that case you'd want
to use STAC Items to find the individual pieces. Similarly zarr can hold huge amounts of data, so you could have STAC Items that point into various pieces of it.
It is more that with STAC you want to search for little 'parts' of the entire collection. But if you're representing the entire collection as one file then it
likely makes less sense to put that in STAC.

So in conclusion, there is a space for both STAC and OARec. STAC should focus on Item level search: scenes/granules/point cloud captures/etc. OARec should focus
on 'dataset' search. 


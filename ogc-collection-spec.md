# OGC Static Collection Specification <!-- omit in toc --> 
<!-- MarkdownTOC -->

- [Overview](#overview)
- [Collection fields](#collection-fields)
  - [Additional Field Information](#additional-field-information)
    - [id](#id)
    - [type](#type)
    - [crs](#crs)
    - [license](#license)
  - [Extent Object](#extent-object)
    - [Spatial Extent Object](#spatial-extent-object)
    - [Temporal Extent Object](#temporal-extent-object)
  - [Provider Object](#provider-object)
  - [Link Object](#link-object)
    - [Relation types](#relation-types)
- [Media Type for OGC Collections](#media-type-for-ogc-collections)

<!-- /MarkdownTOC -->

## Overview

*NOTE This is a work in progress, to flesh out how things might work*

The OGC Static Collection Specification defines how to use an OGC Collection as a standalone JSON file that provides metadata for a
single set of data. The data could be a geopackage, a geojson, a zipped shapefile, a geotiff, a netcdf, a zarr file, or any number of standard
data formats for storing geospatial information. It works especially well when combined with cloud-native geospatial formats like zarr, enabling
clients to get metadata for the file they're using directly, but it works just as well with formats that aren't as optimized for the cloud.

It is designed to be easily ingested in to an OGC API - Records service, and a companion specification will show how to translate these
core fields into GeoJSON, which is directly compatible with OGC API - Records.


## Collection fields

| Element         | Type                                             | Description                                                  |
| --------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| type            | string                                           | **REQUIRED** Denotes the resource type of the collection (e.g. feature, coverage, map, etc.).  |
| crs             | string                                           | Coordinate reference system of the data represented by this collection.
| id              | string                                           | **REQUIRED.** Identifier for the Collection that is unique across the provider. |
| title           | string                                           | **REQUIRED.** A short descriptive human-readable one-line title for the Collection.       |
| description     | string                                           | **REQUIRED.** Detailed multi-line description to fully explain the Collection. [CommonMark 0.29](http://commonmark.org/) syntax MAY be used for rich text representation. |
| keywords        | \[string]                                        | List of keywords describing the Collection.                  |
| keywordsCodespace |  |A reference to a controlled vocabulary used for the keywords property. |
| providers       | \[[Provider Object](#provider-object)]           | A list of providers, which may include all organizations capturing or processing the data or the hosting provider. Providers should be listed in chronological order with the most recent provider being the last element of the list. |
| extent          | [Extent Object](#extent-object)                  | **REQUIRED.** Spatial and temporal extents.                  |
| links           | \[[Link Object](#link-object)]                   | **REQUIRED.** A list of references to other documents.       |
| created         |  string                                          | The date-time this collection was created, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).|
| updated         |  string                                          | The date-time this collection was updated, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).|
| language | string | The natural language used for textual values (i.e. titles, descriptions, etc) that the collection information is given in. |
| themes | string | A knowledge organization system used to classify the resource. |
| formats | \[string]|A list of available distributions for the resource. |
| rights | string |A statement that concerns all rights not addressed by the license such as a copyright statement. |
| associations | \[[Link Object] | A list of links for accessing the resource, links to other resources associated with this resource, etc. |

### Additional Field Information

#### id

It is important that Collection identifiers are unique across the provider. And providers should strive as much as possible to make
their Collection ids 'globally' unique, prefixing any common information with a unique string. This could be the provider's name if
it is a fairly unique name, or their name combined with the domain they operate in.

#### type

The type of the data. Current options are `feature`, `coverage` and `map`. 

TODO: Explain what these are / when to use them. 

#### crs

 Coordinate reference system of the data represented by this collection. 

#### license

Collection's license(s) as a SPDX [License identifier](https://spdx.org/licenses/).
Alternatively, use `proprietary` (see below) if the license is not on the SPDX license list or `various` if multiple licenses apply.
In all cases links to the license texts SHOULD be added, see the `license` link relation type.
If no link to a license is included and the `license` field is set to `proprietary`, the Collection is private,
and consumers have not been granted any explicit right to use the data.

### Extent Object

The object describes the spatio-temporal extents of the Collection. Both spatial and temporal extents are required to be specified.

| Element  | Type                                              | Description                                                           |
| -------- | ------------------------------------------------- | --------------------------------------------------------------------- |
| spatial  | [Spatial Extent Object](#spatial-extent-object)   | **REQUIRED.** Potential *spatial extents* covered by the Collection.  |
| temporal | [Temporal Extent Object](#temporal-extent-object) | **REQUIRED.** Potential *temporal extents* covered by the Collection. |

#### Spatial Extent Object

The object describes the spatial extents of the Collection.

| Element | Type         | Description                                                          |
| ------- | ------------ | -------------------------------------------------------------------- |
| bbox    | \[\[number]] | **REQUIRED.** Potential *spatial extents* covered by the Collection. |

**bbox**: Each outer array element can be a separate spatial extent describing the bounding boxes
of the assets represented by this Collection using either 2D or 3D geometries.

The first bounding box always describes the overall spatial extent of the data. All subsequent bounding boxes can be
used to provide a more precise description of the extent and identify clusters of data.
Clients only interested in the overall spatial extent will only need to access the first item in each array.
It is recommended to only use multiple bounding boxes if a union of them would then include
a large uncovered area (e.g. the union of Germany and Chile).

The length of the inner array must be 2*n where n is the number of dimensions.
The array contains all axes of the southwesterly most extent followed by all axes of the northeasterly most extent specified in
Longitude/Latitude or Longitude/Latitude/Elevation based on [WGS 84](http://www.opengis.net/def/crs/OGC/1.3/CRS84).
When using 3D geometries, the elevation of the southwesterly most extent is the minimum depth/height in meters
and the elevation of the northeasterly most extent is the maximum.

The coordinate reference system of the values is WGS 84 longitude/latitude.
Example that covers the whole Earth: `[[-180.0, -90.0, 180.0, 90.0]]`.
Example that covers the whole earth with a depth of 100 meters to a height of 150 meters: `[[-180.0, -90.0, -100.0, 180.0, 90.0, 150.0]]`.

#### Temporal Extent Object

The object describes the temporal extents of the Collection.

| Element  | Type               | Description                                                           |
| -------- | ------------------ | --------------------------------------------------------------------- |
| interval | \[\[string\|null]] | **REQUIRED.** Potential *temporal extents* covered by the Collection. |

**interval**: Each outer array element can be a separate temporal extent.
The first time interval always describes the overall temporal extent of the data. All subsequent time intervals
can be used to provide a more precise description of the extent and identify clusters of data.
Clients only interested in the overall extent will only need to access the first item in each array.
It is recommended to only use multiple temporal extents if a union of them would then include a large
uncovered time span (e.g. only having data for the years 2000, 2010 and 2020).

Each inner array consists of exactly two elements, either a timestamp or `null`.

Timestamps consist of a date and time in UTC and MUST be formatted according to
[RFC 3339, section 5.6](https://tools.ietf.org/html/rfc3339#section-5.6).
The temporal reference system is the Gregorian calendar.

Open date ranges are supported by setting the start and/or the end time to `null`.
Example for data from the beginning of 2019 until now: `[["2019-01-01T00:00:00Z", null]]`.
It is recommended to provide at least a rough guideline on the temporal extent and thus
it's not recommended to set both start and end time to `null`. Nevertheless, this is possible
if there's a strong use case for an open date range to both sides.

### Provider Object

The object provides information about a provider.
A provider is any of the organizations that captures or processes the content of the Collection
and therefore influences the data offered by this Collection.
May also include information about the final storage provider hosting the data.

| Field Name  | Type      | Description                                                  |
| ----------- | --------- | ------------------------------------------------------------ |
| name        | string    | **REQUIRED.** The name of the organization or the individual. |
| description | string    | Multi-line description to add further provider information such as processing details for processors and producers, hosting details for hosts or basic contact information. [CommonMark 0.29](http://commonmark.org/) syntax MAY be used for rich text representation. |
| roles       | \[string] | Roles of the provider. Any of `licensor`, `producer`, `processor` or `host`. |
| url         | string    | Homepage on which the provider describes the dataset and publishes contact information. |

**roles**: The provider's role(s) can be one or more of the following elements:

- *licensor*: The organization that is licensing the dataset under the license specified in the Collection's `license` field.
- *producer*: The producer of the data is the provider that initially captured and processed the source data, e.g. ESA for Sentinel-2 data.
- *processor*: A processor is any provider who processed data to a derived product.
- *host*: The host is the actual provider offering the data on their storage.
  There should be no more than one host, specified as last element of the list.

### Link Object

This object describes a relationship with another entity. Data providers are advised to be liberal with links.

| Field Name | Type   | Description                                                  |
| ---------- | ------ | ------------------------------------------------------------ |
| href       | string | **REQUIRED.** The actual link in the format of an URL. Relative and absolute links are both allowed. |
| rel        | string | **REQUIRED.** Relationship between the current document and the linked document. See chapter "[Relation types](#relation-types)" for more information. |
| type       | string | [Media type](../catalog-spec/catalog-spec.md#media-types) of the referenced entity. |
| title      | string | A human readable title to be used in rendered displays of the link. |

At least one link object is required, to link to the actual data that is represented by this collection. 

TODO: Link to or explain common types that one would expect. Including linking to fuller metadata in standard formats. 

#### Relation types

OGC Collections use a variety of `rel` types in the link object,
to describe the exact nature of the link between this Collection and the entity it is linking to.
It is recommended to use the official
[IANA Link Relation Types](https://www.iana.org/assignments/link-relations/link-relations.xhtml) where possible.
The following table explains common `rel` types that are used for Collections.


| Type    | Description                                                  |
| ------- | ------------------------------------------------------------ |
| self    | STRONGLY RECOMMENDED. *Absolute* URL to the location that the Collection file can be found online, if available. This is particularly useful when in a download package that includes metadata, so that the downstream user can know where the data has come from. |
| data    | URL to the actual data, generally used in conjunction with the `type` field for clients to know the data format.
| license | The license URL(s) for the Collection SHOULD be specified if the `license` field is set to `proprietary` or `various`. If there is no public license URL available, it is RECOMMENDED to put the license text in a separate file and link to this file. |

TODO: Add a more complete list of potentail rel types.

## Media Type for OGC Collections

An OGC Collection is a JSON file ([RFC 8259](https://tools.ietf.org/html/rfc8259)), and thus should use the 
[`application/json`](https://tools.ietf.org/html/rfc8259#section-11) as the [Media Type](https://en.wikipedia.org/wiki/Media_type) 
(previously known as the MIME Type). 


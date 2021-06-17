# OGC Record GeoJSON Specification 

<!-- MarkdownTOC -->

- [Overview](#overview)
- [Item fields](#item-fields)
  - [Properties Object](#properties-object)
  - [Link Object](#link-object)
    - [Relation types](#relation-types)
- [Media Type for STAC Item](#media-type-for-stac-item)

<!-- /MarkdownTOC -->


## Overview

This document explains the structure and content of an OGC Record as GeoJSON, the core atomic unit in a OGC API - Records
implementation. A **Record**. It is specified in that core specification, this is meant to succinctly 
describe the core 'building block' that can be used a standalone file. This **Record** is a
[GeoJSON](http://geojson.org/) [Feature](https://tools.ietf.org/html/rfc7946#section-3.2) augmented with
[foreign members](https://tools.ietf.org/html/rfc7946#section-6) relevant to an OGC geospatial entity.
These include fields that identify the spatial bounds, time range and associations of the Record.

A Record is the GeoJSON equivalent of an [OGC Collection](ogc-collection-spec.md), and shares most all the same fields.

Records are represented in JSON format and are very flexible. Any JSON object that contains all the
required fields is a valid Record.

- Examples:
  - See the [minimal example](./examples/record-building-example.json),
    as well as a [more fleshed example](./examples/record-meetlocaties-example.json) that contains more fields and links.
- JSON Schema: TODO

## Item fields

This object describes a Record. The fields `id`, `type`, `bbox`, `geometry` and `properties` are
inherited from GeoJSON.

| Field Name | Type                                                                       | Description |
| ---------- | -------------------------------------------------------------------------- | ----------- |
| type       | string                                                                     | **REQUIRED.** Type of the GeoJSON Object. MUST be set to `Feature`. |
| id         | string                                                                     | **REQUIRED.** A unique record identifier. |
| geometry   | [GeoJSON Geometry Object](https://tools.ietf.org/html/rfc7946#section-3.1) \| [null](https://tools.ietf.org/html/rfc7946#section-3.2) | **REQUIRED.** Defines the full footprint of the asset represented by this item, formatted according to [RFC 7946, section 3.1](https://tools.ietf.org/html/rfc7946#section-3.1). The footprint should be the default GeoJSON geometry, though additional geometries can be included. Coordinates are specified in Longitude/Latitude or Longitude/Latitude/Elevation based on [WGS 84](http://www.opengis.net/def/crs/OGC/1.3/CRS84). |
| properties | [Properties Object](#properties-object)                                    | **REQUIRED.** A dictionary of additional metadata for the Item. |
| links      | \[[Link Object](#link-object)]                                             | **REQUIRED.** List of link objects to resources and related URLs. A link with the `rel` set to `self` is strongly recommended. |
| created         |  string                                          | The date-time this record was created, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).|
| updated         |  string                                          | The date-time this record was updated, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).|

### Properties Object

Additional metadata fields can be added to the GeoJSON Object Properties. The only required field
is `datetime` but it is recommended to add more fields, see [Additional Fields](#additional-fields)
resources below.

| Field Name | Type         | Description                                                  |
| ---------- | ------------ | ------------------------------------------------------------ |
| type            | string                                           | **REQUIRED** Denotes the resource type of the collection (e.g. feature, coverage, map, etc.).  |
| title           | string                                           | **REQUIRED** A short descriptive human-readable one-line title for the Collection.       |
| description     | string                                           | Detailed multi-line description to fully explain the Collection. [CommonMark 0.29](http://commonmark.org/) syntax MAY be used for rich text representation. |
| keywords        | \[string]                                        | List of keywords describing the Collection.                  |
| keywordsCodespace |  |A reference to a controlled vocabulary used for the keywords property. |
| language | string | The natural language used for textual values (i.e. titles, descriptions, etc) that the collection information is given in. |
| externalId              | string                                           | Identifier for the Collection that is unique across the provider. |
| publisher       | string          | The entity making the resource available. |
| created         |  string                                          | The date-time the collection represented by this record was created, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).|
| updated         |  string                                          | The date-time this collection represented by this record was updated, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).|
| themes | string | A knowledge organization system used to classify the resource. |
| formats | \[string]|A list of available distributions for the resource. |
| contactPoint | string |A n entity to contact about the resource. |
| license | string | A legal document under which the resource is made available. |
| rights | string |A statement that concerns all rights not addressed by the license such as a copyright statement. |
| extent          | [Extent Object](#extent-object)                  | Spatial and temporal extents.                  |
| associations | \[[Link Object](#link-object)] | A list of links for accessing the resource, links to other resources associated with this resource, etc. |
| crs             | string                                           | Coordinate reference system of the data represented by this collection.

TODO: Explain these in more depth. I think some aren't strings, as per the examples.

### Link Object

This object describes a relationship with another entity.

| Field Name | Type   | Description |
| ---------- | ------ | ----------- |
| href       | string | **REQUIRED.** The actual link in the format of an URL. Relative and absolute links are both allowed. |
| rel        | string | **REQUIRED.** Relationship between the current document and the linked document. See chapter "Relation types" for more information. |
| type       | string | [Media type](../catalog-spec/catalog-spec.md#media-types) of the referenced entity. |
| title      | string | A human readable title to be used in rendered displays of the link. |

#### Relation types

Records use a variety of `rel` types in the link object,
to describe the exact nature of the link between this Item and the entity it is linking to.
It is recommended to use the official
[IANA Link Relation Types](https://www.iana.org/assignments/link-relations/link-relations.xhtml) where possible.

TODO: List common rel types


## Media Type for STAC Item

A Record is a GeoJSON file ([RFC 7946](https://tools.ietf.org/html/rfc7946)), and thus should use the 
[`application/geo+json`](https://tools.ietf.org/html/rfc7946#section-12) as the [Media Type](https://en.wikipedia.org/wiki/Media_type) 
(previously known as the MIME Type). 

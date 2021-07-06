## Overview

This document describes the fields needed for an OGC Record to describe a 'dataset'. (TODO: Describe a dataset
better or link to some good definition)

This is the metadata needed for users to actually find the data they need. The data itself may be available as
an OGC API Service, an older OGC W\*S Service, or an actual data file.

A dataset record is an [OGC Record](ogc-record-geojson-spec.md), and uses all the exact same fields, but makes
more of the fields required, to enable users to actually find data. 

A Record is the GeoJSON equivalent of an [OGC Collection](ogc-collection-spec.md) that includes 'Dataset Fields' 
(TODO: Flesh this out - just document the required extras), and shares most all the same fields.

Dataset Records are represented in JSON format and are very flexible. Any JSON object that contains all the
required fields is a valid Record.

- Examples:
  - See the [minimal example](./examples/record-building-example.json),
    as well as a [more fleshed example](./examples/record-meetlocaties-example.json) that contains more fields and links.
- JSON Schema: TODO

(TODO: somewhere should have a set of Record 'definitions')

## Dataset Record Fields

The core Record fields for a 'Dataset Record' remain the same as in the core [OGC Record](ogc-record-geojson-spec.md), with the
exact same Item fields as [specified there](ogc-record-geojson-spec.md#item-fields).

### Datset Record Property Fields

The property fields are where the Dataset Record has more requirements. It uses all the same core Record definitions, but adds in 
more requirements and a couple defaults. 

| Field Name        | Required in Core Record | Required in Dataset Record | Description | [DCAT](https://www.w3.org/TR/vocab-dcat-2/) alignment |
|-------------------|-------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|--|
| type              | M                       | M                          | Denotes the resource type of the record. For the dataset record this is **required** to be `dataset`.                                                         | `rdf:type dcat:Dataset` |
| title             | M                       | M                          | A short descriptive human-readable one-line title for the Collection.                                                                                       | `dcterms:title` |
| description       | O                       | M                          | Detailed multi-line description to fully explain the Collection. | `dcterms:description` |
| keywords          | O                       | M                          | List of keywords describing the Collection.                                                                                                                 | `dcat:keyword` (free-text - repeat if necessary) <br/> `dcat:theme` (URI - typically denoting a `skos:Concept`) |
| keywordsCodespace | O                       | O (defaults to XXX)        | A reference to a controlled vocabulary used for the keywords property.                                                                                      | `dcat:themeTaxonomy` (pointer to `skos:ConceptScheme`) |
| language          | O                       | O (defaults to english)    | The natural language used for textual values (i.e. titles, descriptions, etc) that the collection information is given in.                                  | `dcterms:language` |
| externalId        | O                       | O                          | Identifier for the Collection that is unique across the provider.                                                                                           | `dcterms:identifier` |
| publisher         | O                       | M                          | The entity making the resource available.                                                                                                                   | `dcterms:publisher` |
| created           | O                       | M                          | The date-time the collection represented by this record was created, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).              | `dcterms:created` or `dcterms:issued` |
| updated           | O                       | O                          | The date-time this collection represented by this record was updated, formatted to [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6).             | `dcterms:modified` |
| themes            | O                       | O                          | A knowledge organization system used to classify the resource.                                                                                              | `dcat:themeTaxonomy` (pointer to `skos:ConceptScheme`) |
| formats           | O                       | O                          | A list of available distributions for the resource.                                                                                                         | `dcat:distribution` (pointer to description of serialized form) (repeat as required) |
| contactPoint      | O                       | M                          | An entity to contact about the resource.                                                                                                                   | `dcat:contactPoint` |
| license           | O                       | M                          | A legal document under which the resource is made available.                                                                                                | `dcterms:license` |
| rights            | O                       | O                          | A statement that concerns all rights not addressed by the license such as a copyright statement.                                                            | `dcterms:rights` or `dcterms:accessRights` |
| extent            | O                       | M                          | Spatial and temporal extents.                                                                                                                               | `dcterms:spatial` |
| associations      | O                       | M                          | A list of links for accessing the resource, links to other resources associated with this resource, etc.                                                    | `dcat:qualifiedRelation` `dcterms:relation` `dcat:accessURL` and a few other specific properties and property-paths |
| crs               | O                       | O (default to latlong)     | Coordinate reference system of the data represented by this collection.                                                                                     | - |

### Associations

At least one association is required. This should link to the actual dataset. It could be to OGC API (or OGC W\*S interfaces to the data, it 
could link directly to the source format file for the dataset, or it ideally is a combination: several OGC services and a link to the source data.

TODO: Flesh out common rel types for ogc api links, source data file links, etc.

### Dataset definition ideas (Work in Progress)

From STAC: a set of assets that are defined with the same properties and share higher level metadata. In the satellite world these would typically all come from the same sensor or constellation. It corresponds directly to what others call a "dataset series" (ESA, ISO 19115), "collection" (CNES, NASA), and "dataset" (JAXA, DCAT). So if all your Items have the same properties, they probably belong in the same Collection.

We should also reference vector dataset ideas, how it maps to a 'layer', can be a coverage, etc.

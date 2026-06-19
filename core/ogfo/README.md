# Open Geospatial Feature Ontology (OGFO)

OGFO is the second layer of the O'stack. It extends OESO with geospatial machinery by composing `oeso:State` with GeoSPARQL. Domain ontologies that require spatial description import OGFO rather than OESO directly. Mirrors without a spatial component import OESO directly and never touch OGFO.

## Core insight

In most GIS systems geometry is a mutable property of a feature. OGFO makes geometry a State. When a facility's coordinates are corrected, a new `ogfo:GeospatialState` is created and the prior one is retained permanently. Historical spatial queries automatically use the correct geometry for the time period in question.

Geometry is not special — it is simply mandatory. A `GeospatialState` carries geometry alongside all other attributes from the same source distribution in a single State, sourced and timestamped like any other `oeso:State`.

## Provenance chain

OGFO inherits the full OESO provenance chain. A `GeospatialState` is assigned to an Entity via `oeso:EntityAssignment` — no special spatial assignment mechanism is needed.

```
oeso:Organization
        │
        │ owns / maintains
        ▼
dcat:Dataset
        ▲
        │ dcat:isDistributionOf
        │
dcat:Distribution
        ▲
        │ oeso:hasSource
        │
ogfo:GeospatialState ──── geo:hasGeometry ───► geo:Geometry
        │                                           │
        │ (+ all other attributes                   │ ogfo:geometryType
        │   from the same source)                   ▼
        ▲                                    ogfo:GeometryType
        │ oeso:assignsState
        │
oeso:EntityAssignment
        │
        │ oeso:assignsToEntity
        ▼
oeso:Entity
```

## Key concepts

**GeospatialState** — a subclass of `oeso:State` with two mandatory constraints: a `geo:hasGeometry` link to a `geo:Geometry` instance, and a declared `ogfo:coordinateReferenceSystem`. All other attributes from the source distribution are carried in the same State alongside the geometry.

**Coordinate reference system** — mandatory on every `GeospatialState`. A geometry without a declared CRS is analytically dangerous. The standard default is EPSG:4326 (WGS84).

**GeometryType** — named individuals classifying the geometry representation: `ogfo:Point`, `ogfo:LineString`, `ogfo:Polygon`, `ogfo:MultiPoint`, `ogfo:MultiLineString`, `ogfo:MultiPolygon`.

**GeoSPARQL composition** — OGFO imports GeoSPARQL (`http://www.opengis.net/ont/geosparql`) and uses `geo:Geometry`, `geo:hasGeometry`, and `geo:asWKT` directly. OGFO adds only what GeoSPARQL does not provide: the bitemporal State wrapper, mandatory CRS enforcement, geometry type classification, precision, and acquisition method.

## What OGFO adds over GeoSPARQL

| Concern | GeoSPARQL | OGFO |
|---|---|---|
| Geometry class | `geo:Geometry` ✓ | inherited |
| WKT serialization | `geo:asWKT` ✓ | inherited |
| Feature-geometry link | `geo:hasGeometry` ✓ | inherited |
| Bitemporal State wrapper | ✗ | `ogfo:GeospatialState` |
| Mandatory CRS | ✗ | `ogfo:coordinateReferenceSystem` |
| Geometry type classification | ✗ | `ogfo:GeometryType` individuals |
| Acquisition precision | ✗ | `ogfo:precision` |
| Acquisition method | ✗ | `ogfo:geometrySource` |

## Extending OGFO

To extend OGFO for a domain:

1. Import this ontology
2. Subclass `oeso:Entity` to define your domain entity type
3. Subclass `ogfo:GeospatialState` to define typed geospatial state snapshots

The geometry machinery, CRS enforcement, and full OESO provenance chain are inherited automatically.

## Namespace

```
https://mirrordrop.org/ontologies/core/ogfo#
```

Prefix: `ogfo:`

## Dependencies

- [OESO](../oeso/) — `https://mirrordrop.org/ontologies/core/oeso`
- GeoSPARQL — `http://www.opengis.net/ont/geosparql`

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — Joseph A. Stoffa
# O'stack Ontologies

A collection of interoperable ontologies for provenance-first, entity-centric modeling. Nicknamed the O'stack. Ontologies follow the O--O naming convention.

## Design philosophy

Every class and property in the O'stack earns its place by being load-bearing in the operational model. If removing it would break something — a query, a provenance chain, a resolution, an activity distinction — it belongs. If it is only conceptually tidy, it waits. This is the earned abstraction principle.

## Import chain

```
OESO  (entity state foundation)
  └── OGFO  (geospatial feature layer)
        ├── OAGO  (administrative geography)
        └── OICO  (infrastructure)
              ├── OEPO  (electrical power)
              ├── OWIO  (water infrastructure)
              └── OMIO  (maritime infrastructure)

MDPO  (acquisition pipeline — extends OESO only, no spatial)
```

Domain ontologies import all the layers the need from the lowest on the way up. A mirror that tracks social media posts imports OESO directly. A mirror that tracks power plants imports OEPO, OICO, OGFO, and OESO.

_Note: Transitive import resolution via `owl:imports` requires ontologies 
to be served at their canonical URIs. This will be supported once the 
O'stack is formally published to `https://mirrordrop.org/ontologies` 
(reserved but not yet implemented). Until then, all ontologies in the 
import chain must be loaded explicitly._

## Core

**OESO — Open Entity State Ontology**
The foundational layer. Provides the bitemporal State model, the provenance chain (Organization → Dataset → Distribution → State → Entity), Assessment-based source quality tracking, and the full resolution activity hierarchy (EntityAssignment, EntityEquivalence, AttributeMapping). Domain-agnostic — no assumptions about what kind of entity is being described. [`core/oeso/`](oeso/)

**OGFO — Open Geospatial Feature Ontology**
Extends OESO with geospatial machinery. Introduces `GeospatialState` — a State subclass that requires a geometry and a coordinate reference system. Geometry is a State, not a property: when coordinates change, a new GeospatialState is created and the old one is retained permanently. All domain ontologies that require spatial description import OGFO rather than OESO directly. [`core/ogfo/`](ogfo/)

## Domain extensions

Coming soon.

### GIS

**OAGO — Open Administrative Geography Ontology**
Extends OGFO. Models administrative boundaries as first-class entities rather than static geometric containers. A boundary is an entity described by States that change over time — its geometry is one State, its population is another, its energy consumption is another. Solves the cross-agency join problem via `intersectionWeight` derived States.

### Infrastructure

**OICO — Open Infrastructure Core Ontology** — planned
**OEPO — Open Electrical Power Ontology** — planned
**OWIO — Open Water Infrastructure Ontology** — planned
**OMIO — Open Maritime Infrastructure Ontology** — planned

## Platform

**MDPO — MirrorDrop Provenance Ontology**
Extends OESO with platform-specific acquisition properties for the lil'drop / MirrorDrop data acquisition pipeline. Covers `MirrorAcquisitionActivity`, `AcquiredArtifact`, artifact hashing, and ingestor versioning. Not part of the spatial spine — imports OESO only.

## License

All ontologies in this repository are released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

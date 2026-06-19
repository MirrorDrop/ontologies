# Changelog

All notable changes to the O'stack ontologies are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

## [ogfo-0.1.0] — 2026-06-18

### Added
- `ogfo:GeospatialState` — `oeso:State` subclass with mandatory `geo:hasGeometry` and `ogfo:coordinateReferenceSystem`
- `ogfo:GeometryType` — named individual class classifying geometry representations
- Named individuals: `ogfo:Point`, `ogfo:LineString`, `ogfo:Polygon`, `ogfo:MultiPoint`, `ogfo:MultiLineString`, `ogfo:MultiPolygon`
- `ogfo:geometryType` — object property linking `geo:Geometry` to `ogfo:GeometryType`
- `ogfo:coordinateReferenceSystem` — mandatory datatype property, EPSG code or OGC CRS URI
- `ogfo:precision` — optional geometry precision in metres
- `ogfo:geometrySource` — optional acquisition method (survey, GPS, geocoding, digitized, modeled)
- Imports GeoSPARQL (`http://www.opengis.net/ont/geosparql`) — `geo:Geometry`, `geo:hasGeometry`, `geo:asWKT` used directly
- Disjointness axioms across all geometry type individuals

## [oeso-0.1.0] — 2026-06-18

### Added
- `oeso:Entity` — a real-world thing described by States
- `oeso:State` — immutable, bitemporal, single-source description of an Entity
- `oeso:Source` — abstract provenance entity; `dcat:Dataset` and `dcat:Distribution` as subclasses
- `oeso:Organization` — formal group responsible for publishing Sources
- `oeso:Assessment` — immutable quality evaluation of a Source
- `oeso:EntityAssignment` — reified assertion that a State describes a particular Entity
- `oeso:EntityEquivalence` — reified assertion that 2+ Entity UUIDs refer to the same real-world thing
- `oeso:AttributeMapping` — reified assertion that 2+ attributes from different Sources represent the same concept
- `oeso:StateActivity` → `oeso:AcquisitionActivity` / `oeso:DerivationActivity`
- `oeso:ResolutionActivity` → `oeso:EntityAssignmentActivity` / `oeso:EntityEquivalenceActivity` / `oeso:AttributeMappingActivity`
- `oeso:AssessmentActivity` — produces Assessments for Sources
- Bitemporal properties: `oeso:hasStateDate`, `oeso:hasStateValidTo`, `oeso:hasStateCreationDate`
- Provenance properties: `oeso:hasSource`, `oeso:hasAssessment`, `oeso:assessedBy`
- Resolution properties: `oeso:assignsState`, `oeso:assignsToEntity`, `oeso:hasEntity`, `oeso:mapsAttribute`, `oeso:sameRealWorldEntity`, `oeso:attributeMappingScope`
- `oeso:canonicalIdentifier` — source's own identifier for a record, input to entity resolution
- `oeso:artifactHash` — content hash of source artifact, primary reproducibility anchor
- `oeso:equivalenceConfidence` — confidence score (0.0–1.0) for EntityEquivalence assertions
- `oeso:assessmentDate` — timestamp on Assessment instances
- DCAT mappings: `dcat:Dataset` and `dcat:Distribution` declared as subclasses of `oeso:Source`
- Disjointness axioms across StateActivity and ResolutionActivity branches
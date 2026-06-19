# Open Entity State Ontology (OESO)

OESO is a domain-agnostic ontology for describing real-world entities through temporally bounded, immutable, sourced descriptions called States. It is the foundational layer of the O'stack.

## Core insight

Most data systems treat descriptions of things as properties of those things. OESO separates the two. A bridge is not its inspection report. A vessel is not its AIS transponder signal. A census tract is not its 2020 boundary. Each of these is an entity described by one or more States — each State sourced, timestamped, and immutable.

This separation makes provenance, temporal correctness, and multi-source reconciliation structural rather than bolted on.

## Provenance chain

```
oeso:Organization
        │
        │ owns / maintains
        ▼                              
dcat:Dataset ───────────────────────────────────────────────────────────────────┐
        ▲                                                                       │ oeso:hasAssessment
        │ dcat:isDistributionOf                                                 │
        │                                                                       ▼
dcat:Distribution ──────────────────────────────────────────────────► oeso:Assessment
        ▲         oeso:hasAssessment                                            ▲
        │                                                                       │
        │ oeso:hasSource                                             oeso:AssessmentActivity
        │
oeso:State ─────────────────────────────────────────────────────────────────────┐
        ▲                                                                       │
        │ oeso:assignsState                                                     │ prov:wasGeneratedBy
        │                                                                       ▼
oeso:EntityAssignment                                               oeso:AcquisitionActivity
        │                                                           oeso:DerivationActivity
        │ oeso:assignsToEntity
        ▼
oeso:Entity ◄──────────────────────────────────────────────────────────────────────────┐
                                                                                       │
oeso:EntityEquivalence ─── oeso:hasEntity (min 2) ──── oeso:sameRealWorldEntity ───────┘
        ▲
        │ prov:generated
oeso:EntityEquivalenceActivity

oeso:AttributeMapping ─── oeso:mapsAttribute (min 2)
        ▲
        │ prov:generated
oeso:AttributeMappingActivity
```

## Key concepts

**State** — an immutable, single-source description of an entity at a point in time. Corrections never modify existing States — they create new ones. The prior State is retained permanently.

**Bitemporal model** — every State carries two timestamps: `hasStateDate` (valid-from, when the described fact was true in the real world) and `hasStateCreationDate` (transaction time, when the State entered the system). This supports both current-state queries and historical reconstruction.

**Source** — the provenance anchor for a State. `dcat:Dataset` and `dcat:Distribution` are subclasses of `oeso:Source`, making any DCAT catalog entry immediately usable as a State source.

**Assessment** — an immutable quality evaluation of a Source's distributions. Produced by an `AssessmentActivity`. Assessments drive BEES resolution — higher-assessed sources win attribute-level tiebreaks.

**EntityAssignment** — a reified assertion that a State describes a particular Entity, produced by an `EntityAssignmentActivity`. Carries its own provenance — when the assignment was made and by what logic.

**EntityEquivalence** — a reified assertion that two or more Entity UUIDs refer to the same real-world thing, produced by an `EntityEquivalenceActivity`. `sameRealWorldEntity` designates the canonical UUID the cluster resolves to. `equivalenceConfidence` (0.0–1.0) flags uncertain matches for human review.

**AttributeMapping** — a reified assertion that two or more attributes from different Sources represent the same real-world concept, produced by an `AttributeMappingActivity`.

## Activity hierarchy

```
prov:Activity
    └── oeso:StateActivity
            ├── oeso:AcquisitionActivity   # raw external data → State
            └── oeso:DerivationActivity    # existing States → new State
    └── oeso:ResolutionActivity
            ├── oeso:EntityAssignmentActivity
            ├── oeso:EntityEquivalenceActivity
            └── oeso:AttributeMappingActivity
    └── oeso:AssessmentActivity
```

`StateActivity` and `ResolutionActivity` are disjoint — resolution activities do not produce States, they produce assignments and equivalences.

## BEES — Best Estimate of Entity State

BEES is a query concept built on top of OESO. For a given entity and point in time, BEES resolves the best known value per attribute across all States from all sources using some predefined logic.

Default resolution: most recent `hasStateDate` wins per attribute. Tiebreak: most recent `hasStateCreationDate`. When Assessments are present, assessment score becomes the primary sort.

BEES is not part of the ontology itself — it is an application layer that queries the graph.

## Design principles

**States are immutable.** Corrections create new States. Old ones are never modified or retracted.

**Validation is a property of distributions, not datasets or organizations.** A dataset published by an authoritative agency does not automatically make its distributions authoritative. Each distribution is assessed independently.

**Earned abstraction.** Every class and property in OESO earns its place by being load-bearing in the operational model. If removing it would break a query, a provenance chain, a BEES resolution, or an activity distinction — it belongs. If it is only conceptually tidy, it waits.

**Domain agnostic.** OESO makes no assumptions about the type of entity being described. Infrastructure, vessels, census tracts, social media posts, and scientific observations are all entities that can be described by OESO States. Domain ontologies import OESO and subclass `oeso:Entity` and `oeso:State`.

## Extending OESO

To extend OESO for a domain:

1. Import this ontology
2. Subclass `oeso:Entity` to define your domain entity type
3. Subclass `oeso:State` to define typed state snapshots for that entity
4. Subclass `oeso:AcquisitionActivity` or `oeso:DerivationActivity` to type your acquisition methods

The bitemporal properties, source linkage, and provenance machinery are inherited automatically.

## Namespace

```
https://mirrordrop.org/ontologies/oeso/core#
```

Prefix: `oeso:`

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — Joseph A. Stoffa
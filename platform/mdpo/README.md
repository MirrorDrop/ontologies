# MirrorDrop Provenance Ontology (MDPO)

MDPO is the platform layer of the O'stack. It extends OESO with acquisition provenance properties specific to the lil'drop / MirrorDrop data pipeline. Domain ontologies do not import MDPO — it is a platform concern, not a modeling concern.

## Core insight

OESO deliberately omits concrete acquisition details. It knows that data comes from Sources via Activities, but it has no opinion about what kind of ingestor ran, what URL was fetched, or where the artifact landed on disk. Those are platform concerns. MDPO defines them.

## What MDPO adds over OESO

| Concern | OESO | MDPO |
|---|---|---|
| Acquisition activity | `oeso:AcquisitionActivity` | `mdpo:MirrorAcquisitionActivity` |
| Raw artifact as Source | `oeso:Source` (abstract) | `mdpo:AcquiredArtifact` |
| Source URL | ✗ | `mdpo:sourceURL` |
| Ingestor identity | ✗ | `mdpo:ingestorName`, `mdpo:ingestorVersion` |
| Acquisition method | ✗ | `mdpo:activityType` |
| Artifact hash | `oeso:artifactHash` (on Source) | `mdpo:artifactHash` (on AcquiredArtifact) |
| Artifact path | ✗ | `mdpo:artifactPath` |
| Artifact extension | ✗ | `mdpo:artifactExtension` |
| Activity → Artifact link | ✗ | `mdpo:producedArtifact` |

## Provenance chain

Every artifact acquired by a lil'drop ingestor produces the following chain in Fuseki:

```
mdpo:MirrorAcquisitionActivity
        │
        │ mdpo:producedArtifact
        ▼
mdpo:AcquiredArtifact ◄─────────────────────────────── oeso:State
        │                        oeso:hasSource                ▲
        │ mdpo:artifactHash                                    │
        │ mdpo:artifactPath                        oeso:EntityAssignment
        │ mdpo:artifactExtension                               │
        │                                          oeso:assignsToEntity
        └── mdpo:sourceURL (on activity)                       │
            mdpo:ingestorName                            oeso:Entity
            mdpo:ingestorVersion
            mdpo:activityType
            prov:startedAtTime
```

The left side (activity + artifact) is written by the platform `ProvenanceProcessor` automatically on every `artifact.created.*` event. The right side (State + EntityAssignment + Entity) is written by mirror-specific entity processors in MS2.

## Key classes

**`mdpo:MirrorAcquisitionActivity`** — subclass of `oeso:AcquisitionActivity`. Represents a single ingestor run. One activity per run, identified by a UUID. Carries source URL, ingestor name and version, activity type, and start time.

**`mdpo:AcquiredArtifact`** — subclass of `oeso:Source`. Represents the raw artifact produced by an acquisition. Identified by its SHA-256 hash URI. Idempotent — if the same artifact hash is acquired twice, only one `AcquiredArtifact` exists in the graph; multiple activities may link to it via `mdpo:producedArtifact`.

## Activity types

`mdpo:activityType` carries one of three values corresponding to the lil'drop ingestor contract:

- `file_download` — artifact acquired via HTTP/HTTPS file retrieval
- `api_fetch` — artifact acquired via structured API call
- `scraper_run` — artifact produced by a scraper extracting from unstructured source

## URI conventions

Activity URIs use a per-run UUID:
```
https://protodrop.io/activity/<uuid-hex>
```

Artifact URIs use the artifact hash (stable — same content = same URI):
```
https://protodrop.io/artifact/<sha256-hash>
```

## Relationship to OESO

MDPO imports OESO and subclasses two of its abstract classes:

- `mdpo:MirrorAcquisitionActivity` → `oeso:AcquisitionActivity` → `oeso:StateActivity` → `prov:Activity`
- `mdpo:AcquiredArtifact` → `oeso:Source` → `prov:Entity`

MDPO does not define Assessment logic. Source quality assessment belongs to mirror-specific vocabularies that extend `oeso:Assessment` for their domain.

## Namespace

```
https://mirrordrop.org/ontologies/platform/mdpo#
```

Prefix: `mdpo:`

## Dependencies

- [OESO](../../core/oeso/) — `https://mirrordrop.org/ontologies/core/oeso`

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — Joseph A. Stoffa
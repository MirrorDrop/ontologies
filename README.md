# ontologies
## O--O
A collection of interoperable ontologies for provenance-first entity-centric modeling.  Nicknamed the O'stack core ontologies take the form O--O for uniformity.

Open Entity State Ontology (OESO) is the core ontology that provides States, Assessments, and State related activities.  OESO is designed to be implemented by entity-centric systems that want to model  multiple descriptions of an entity with provenance as a first class feature.



Open 


oeso:Organization
        │
        │ owns / maintains
        ▼
dcat:Dataset ──────────────────────────────────────┐
        ▲                                          │
        │ dcat:isDistributionOf                    │
        |                                          │
dcat:Distribution -──────────────────────────────► oeso:Assessment
        ▲         oeso:hasAssessment                     ▲
        │                                                │
        │ oeso:hasSource                      oeso:AssessmentActivity
        |
oeso:State ──────────────────────────────────────────────────────────┐
        ▲                                                            │
        │ oeso:assignsState                                          │ prov:wasGeneratedBy
        |                                                            ▼
oeso:EntityAssignment                                    oeso:AcquisitionActivity
        │                                                oeso:DerivationActivity
        │ oeso:assignsToEntity
        ▼
oeso:Entity ◄────────────────────────────────────────────────────────────────────┐
                                                                                 │
oeso:EntityEquivalence ─── oeso:hasEntity (min 2) ──── oeso:sameRealWorldEntity ─┘
        ▲
        │ prov:generated
oeso:EntityEquivalenceActivity

oeso:AttributeMapping ─── oeso:mapsAttribute (min 2)
        ▲
        │ prov:generated
oeso:AttributeMappingActivity

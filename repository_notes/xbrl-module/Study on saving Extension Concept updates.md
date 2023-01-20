---
title: Study on saving Extension Concept updates
date:  2023-01-05
---

Relevant files: 
- create_extension_concept_component.dart
- label_middleware.dart
- concept_store.dart
- label_store.dart


Prior to multi language, the creation of extension concept component works in these steps: 
- pas
 
 ### Process of normalization of extension concept labels => Label
```mermaid
graph 
subgraph concept_store
    A(_fetchExtensionConcepts) --> BB(fetched extConcepts)

    BB--> B(_replaceExtensionConcept)
    BB --> C(actions.updateExtensionConceptLabel)

    B --> I(_extensionConceptMap & _extensionConceptMapByName)

    I --> H(actions.conceptsCached)
end
 
subgraph label_middleware
C --> Y(onUpdateExtConcept)
    Y --> |_fetchOrCreateLabelValue| D(docLabel) 
    Y --> |_fetchOrCreateLabelValue| E(standardLabel) 
    D --> F(Label creation)
    E --> F
    F --> G(actions.cacheConceptLabels)
end
subgraph label_store
    G --> |setState| Z(state.labelByConcept & state.labelById & state.labelValueById)
end
   
```

### Process of retrieval of normalized label to the UI

```mermaid
graph 

subgraph create_extension_concept
AA(props.extensionConcept) --getLabelValueByConcept-->B(LabelValues)
B--setState-->C(state.documentation)
end
```

### Process of creation of Extension Concept and its corresponding labels

```mermaid
graph 
subgraph create_extension_concept
A(state)-->B(_createExtConcept)
B-->|createExtConceptPayload| C(actions.createExtConcept)
end

subgraph concept_store 
C --> CC(onCreateExtConcept)
CC --> D(createExtConceptDefault)
CC --extract non default labels --> E(actions.updateLabelValues)
end

subgraph label_middleware
E-->F(onUpdateLabelValue)
end
```

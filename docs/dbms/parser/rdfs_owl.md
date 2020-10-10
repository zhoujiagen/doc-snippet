# Modleing in RDFS and OWL

## 资源

- Dean Allemang, James Hendler. Semantic Web for the Working Ontologist, Second Edition. Elsevier: 2011.



## RDF


## RDFS


## OWL

### owl:someValuesFrom

All individuals for which at least one value of the property P comes from class C.

例:

```
AllStarPlayer owl:equivalentClass
    [a owl:Restriction;
      owl:onProperty :playFor;
      owl:someValuesFrom :AllStarTeam]
```

### owl:allValuesFrom

the individuals for which all values of the property P come from class C

例:

```
q:SelectedAnswer rdfs:subClassOf
    [a owl:Restriction;
      owl:onProperty q:enablesCandidate;
      owl:allValuesFrom q:EnabledQuestion]
```

### owl:hasValue

the class C happens to be a singleton set {A}

例:

```
q:HighPriorityItem owl:equivalentClass
    [a owl:Restriction;
      owl:onProperty q:hasPriority;
      owl:hasValue q:High]
```

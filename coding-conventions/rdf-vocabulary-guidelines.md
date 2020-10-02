# RDF Vocabulary Guidelines

## Overview
The ultimate goal of this documentation is to provide a professional, cohesive
set of general guidelines for anyone wishing to create, extend or maintain RDF
vocabularies. It borrows extensively from existing (and continually evolving)
research in this space, but aims to provide a one-stop-shop for anyone looking
for current industry best practices in relation to RDF vocabulary design.


## The Inrupt Artifact Generator

Although multiple attempts have been made at producing RDF vocabulary
guidelines (and we link to a number of examples below), a fundamental problem is
that these guidelines are generally written in academic papers, or described in
prose, and therefore not machine-readable or enforceable.
 
One nice consequence of Inrupt having an [Artifact Generator]() is that if a
vocabulary is explicitly declared as being of a specific type, then our
generator can enforce a number of these guidelines automatically (e.g. all
Classes and Properties must provide `rdfs:label` and `rdfs:comment` values, in
at least English) before generating source-code artifacts that contain
programming-language constants for the terms contained in that vocabulary.

## Existing research / guidelines

- Richard Cyganiak: [RulesOfThumb](https://www.w3.org/wiki/User:Rcygania2/RulesOfThumb)
- Axel Polleres, Haller (2019): [Are We Better Off With Just One Ontology on the Web?](http://www.semantic-web-journal.net/system/files/swj2307.pdf)

- Widoco: [Vocab Best Practices](http://dgarijo.github.io/Widoco/doc/bestPractices/index-en.html)
- W3C (from 2008): [Best Practice Recipes for Publishing RDF Vocabularies](https://www.w3.org/TR/swbp-vocab-pub/)
  - Old now, talks mostly about configuring Apache to serve vocabs (with
   optional content negotiation), but Appendix B on namespace URI is still
   helpful.

- Axel Polleres et el (2018): [A More Decentralized Vision for Linked Data](https://epub.wu.ac.at/6371/1/IPM_workingpaper_02_2018.pdf)
  - Although more focused on instance data, it does describe the lack of
  vocabulary re-use in the real-world. 
  - Recommends strict adherence to either hash or slash namespaces.
  - It links to [43](https://www.researchgate.net/publication/298213412_A_Systematic_Analysis_of_Term_Reuse_and_Term_Overlap_across_Biomedical_Ontologies)
   (I didn't read it all (as it seems very 'dense'!), but the final short 
   sections 5.5 and 6 seem interesting in that they allude to solutions for term
   discovery and reuse.
  - "Hogan in his thesis [33, Section 5]"

- Vocabulary of Interlinked Datasets: [VoID](https://www.w3.org/TR/void/)
  - Mostly relevant for describing instance data, as opposed to vocabularies.
- Live OWL Documentation Environment: [LODE](https://essepuntato.it/lode/)

## Guidelines

- Provide your vocabulary as [Turtle](https://www.w3.org/TR/turtle/).


### Prefixes

- Use the RDF 1.1 Turtle syntax for the `base` and `prefix` directives (as it
allows easier cut-and-paste into SPARQL queries), and just for consistency we've
chosen to use lower-case for these directives (no strong justification here,
just easier on the eye!).

- Decide on a short prefix for your vocabulary, but don't get caught up in
trying to make the prefix **_too_** short, e.g.
```
prefix myVocab: <http://myCompany.com/ns/example/myVocab#>

# Long perfixes are fine (if justified!) too:
prefix fibo-loan-loan-mod: <https://spec.edmcouncil.org/fibo/ontology/LOAN/Loans/MetadataLOANLoans/>
```

- Try to avoid existing commonly used prefixes if possible. Use [prefix.cc](http://prefix.cc/)
to check if your preferred prefix is already in common use. But just be aware
that prefix.cc is not actively curated, and that anyone is free to add prefixes
as they see fit. So if your preferred prefix is already registered, but on
investigation you determine that the existing vocbaulary is no longer active or
is not in wide use, and that existing vocabulary wouldn't conflict conceptually
with your vocabulary, then feel free to use that prefix (prefixes are only a
convenient syntactic sugar available in some RDF serializations, and anyone
using your vocabulary is completely free to apply whatever prefix they wish
themselves, allowing them to avoid prefix name clashes).

- Consider registering your prefix at [prefix.cc](http://prefix.cc/) once you
think your vocabulary's chosen namespace URI will remain stable.

### Recommended prefixes

- Because anyone can define whatever prefix they want when re-using vocabularies
(e.g. the Dublin Core Terms vocabulary is often used with the prefix `dc:`, or 
`dct:`, or `dcterms:` (the Turtle serialization of the vocabulary itself uses
`dcterms:`, so we do too!)), we provide here the prefixes that we at Inrupt
use for common vocabularies, just to try and keep our prefix usage consistent
across all our RDF:

```
prefix dcterms:  <http://purl.org/dc/terms/>
prefix schema:   <http://schema.org/>
prefix xsd:      <http://www.w3.org/2001/XMLSchema#>
```

### Namespace URI

- Use the HTTPS schema for your vocabulary's namespace (as opposed to HTTP).

- Choose between a hash '#' namespace, or a slash '/' namespace. For more
information on the distinction, see the W3C guidelines in
 [Appendix B: URI namespaces](https://www.w3.org/TR/swbp-vocab-pub/#naming).
  - Simple guidance:
     - use a hash namespace if you consider your vocabulary 'small' (perhaps
      just a few dozen terms), and relatively self-contained.
     - use a slash namespace if think your vocabulary has potential to grow
      substantially, or might be broken up into sub-vocabularies.
  - Important to note: a hash namespace means the entire vocab will be retrieved
  in one GET request, whereas a slash namespace allows each individual term to
  be dereferencable individually (e.g. https://schema.org/Person)
  - Generally speaking, a slash namespace is more future-proof, as new levels of
  hierarchy can be added later, whereas a hash namespace can only have a single
  (root) level of hierarchy.  
  
- Consider using a persistent URI (to allow the links and references defined in
your vocabulary to outlive your current organisation, or personally owned
domain):
  - PURL (but it seems the Internet Archive are no longer supported it (i.e.
   they were not responding to update requests in 2019)).
  - The [w3id.org](https://w3id.org/) project (but as a GitHub repo, can it
   continue to scale indefinitely...?!)

- Do not include a version number in the namespace URI:
```
prefix foaf: <http://xmlns.com/foaf/0.1/>
```
 
- Open question on whether to include year (and possibly month) (as a simple
form of versioning) in the namespace URI.
  - It's fairly common, especially in W3C vocabularys, e.g.
```
prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> 
```
  - ...but my personal preference is to **_not_** include any attempt at 
  explicit versioning in the URI (instead use explicit versioning meta-data
  within the vocabulary itself).
   
### Providing meta-data for your vocabulary

It's very important that you describe your vocabulary, explaining its intent
and providing guidance for potential users of your vocabulary.

#### Explicitly say your vocabulary is a vocabulary!

- Firstly, explicitly define your vocabulary **_as_** a vocabulary. Simply use
the `Ontology` property from the [OWL](http://www.w3.org/2002/07/owl#)
vocabulary, e.g.
```
myvocab: a owl:Ontology .
```

- Optionally, also type your vocabulary as an Artifact Generator-aware
vocabulary to allow automatic machine enforcement of many of these guidelines, 
e.g.
```
# NOTE: this Artifact Generator-aware vocabulary is subject to change, so we
# don't provide the full namespace URI at the moment!
myvocab a artifact-generator:Ontology .
``` 

#### Describe the vocabulary

- Provide basic descriptions of your vocabulary using `rdfs:label` and
`rdfs:comment`, and use explicit language tags to denote the language, e.g.
```
myvocab: a owl:Ontology ;
  rdfs:label "My guidelines vocab"@en ;
  rdfs:label "Mon vocabulaire de lignes directrices"@fr ;
  rdfs:comment "A longer description of my guidelines vocab"@en ;
  rdfs:comment "Une description plus longue de mon vocabulaire de lignes directrices"@fr .
```

#### Provide preferred prefix and namespace hints

- Use the [VANN](http://purl.org/vocab/vann/) properties 
`preferredNamespacePrefix` and `preferredNamespaceUri` to describe your
recommended prefix (remember this is just a suggestion - users of your
vocabulary can choose whatever prefix they like, including none at all!), and
your namespace URI. 

#### Explicitly state the vocabulary license

- What license predicate to use?
  - Dublin Core Terms: `http://purl.org/dc/terms/license`
  - Creative Commons: `http://creativecommons.org/ns#license`
  - Both?

- What license value to use?
  - `http://purl.org/NET/rdflicense/MIT1.0.ttl`
  - `http://www.opendatacommons.org/licenses/pddl/`
  - `https://opensource.org/licenses/MIT`
  - `https://creativecommons.org/publicdomain/zero/1.0/`
  
- What other licensing properties should we use?  
  - `http://creativecommons.org/ns#attributionURL` ?

#### Use `rdfs:isDefinedBy`

- Each term in your vocabulary should link back to the vocabulary itself using
the `rdfs:isDefinedBy` property. This just makes it easy for anyone
dereferencing a term in isolation to link back to the overall vocabulary
defining that term.

#### Use `dcterms:issued` and `dcterms:modified`

- Use `dcterms:issued` (with the value typed as `xsd:date`) for your vocabulary
to represent its official issue date (i.e. original date of release).

- Update `dcterms:modified` (with the value typed as `xsd:date`) for your
vocabulary each time the vocabulary is modified.

- Use `dcterms:issued` for individual terms in your vocabulary if they were
issued after the official issue date of the vocabulary itself.

- Update `dcterms:modified` (with the value typed as `xsd:date`) for each
vocabulary term each time you update any meta-data for that term.

```
myvocab: a owl:Ontology ;
  :
  dcterms:issued "2010-01-01"^^xsd:date ; 
  dcterms:modified "2015-01-01"^^xsd:date . 

myvocab:prop1 a rdf:Property ;
  :
  rdfs:comment "My first property - issued in original vocabulary."@en ;
  dcterms:modified "2015-01-01"^^xsd:date . 

myvocab:prop2 a rdf:Property ;
  :
  rdfs:comment "My new property - issued after the original vocabulary."@en ;
  dcterms:issued "2020-10-02"^^xsd:date . 
``` 

#### Versioning

- Use the `owl:versionInfo` property to provide basic vocabulary version
information.
  - This is **_not_** intended to follow the [SemVer](https://semver.org/)
  convention common elsewhere.
  - Currently, we just use a simple `0.x` format until we think a vocabulary is
  ready to be considered somewhat 'finished', at which point we would promote
  the version to `1.0`, and continue to evolve from there.
  
#### Describe who created this vocabulary

- Provide a simple description of who created this vocabulary using the 
`dc:creator` property, e.g.

```
myVocab dcterms:creator "Inrupt, Inc." .
```  

## Domain and range

- Where appropriate use `schema:domainIncludes` and `schema:rangeIncludes` (in
favour of the more semantically rigid `rdfs:domain` and `rdfs:range` (see [Schema.org justification])(https://www.w3.org/wiki/WebSchemas/SchemaDotOrgMetaSchema)
for more details on the rationale here). These properties can be very helpful in
communicating the intent of vocabulary properties. 

## Publishing your vocabulary

- W3C (from 2008): [Best Practice Recipes for Publishing RDF Vocabularies](https://www.w3.org/TR/swbp-vocab-pub/)
  - Definitely support Content negotiation if at all possible, and support as
  many RDF seralizations as possible (i.e. just use any common RDF library to
  perform the appropriate serializations dynamically at runtime).

## General style guidelines

- Avoid using the `base` directive, as it prevents the use of any relative IRIs
in the remainder of the vocabulary.

This is just a general guideline when working with RDF in general, as relative
IRIs should generally be avoided **_in vocabularies_**, since they make it harder
to process the vocabulary in isolation (i.e. any processor will need to provide
a base IRI to convert any relative IRIs into absolute IRIs, as mandated by RDF
itself). 

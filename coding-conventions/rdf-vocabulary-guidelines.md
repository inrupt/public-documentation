# RDF Vocabulary Guidelines

## Overview

The goal of this documentation is to suggest a cohesive set of guidelines for
anyone wishing to create, extend and/or maintain RDF vocabularies. It borrows
extensively from existing (and continually evolving) research in this space, but
aims to provide a one-stop-shop for anyone looking for current industry best
practices in relation to RDF vocabulary design.

## Background

So although multiple attempts have been made at providing RDF vocabulary
guidelines (see the links in the
[Existing research / guidelines section](#existing-research--guidelines)
below), a fundamental problem is that all these guidelines are generally written
in academic papers, or described in prose, and are therefore not
machine-readable nor automatically enforceable.

This document attempts to fill that gap by defining very specific,
very prescriptive guidelines for creating new RDF vocabularies, as a fist step
to being able to automate the validation, reporting, and enforcement of these
guidelines by tools - with the ultimate goal for making vocabularies easier to
create and subsequently to read and understand by everyone.

So in many ways, the motivation behind this document is very much the same as
the motivations for the existence of extremely popular and common development
tools such as ESLint, or Prettier, or PMD, or SpotBugs, etc. In other words, by
making our RDF 'look' more consistent generally (i.e., by simply following
widely agreed-upon standards), it simply makes that RDF easier to work with for
everyone involved.

## Inrupt's Artifact Generator

One nice consequence of Inrupt providing the Artifact Generator tool is that if
any vocabulary explicitly declares itself as being of a specific type (e.g., of
type `an Inrupt (or Solid?) guideline following vocabulary`), then our generator
can enforce a number of these prescriptive guidelines automatically before
generating source-code that contains consistent programming artifacts for the
terms contained in that vocabulary.

For example, if our guidelines stipulate that all RDF Classes and Properties
should provide `rdfs:label` and `rdfs:comment` values (and that those values
must include at least one English value), then the Artifact Generator can throw
an exception if either of these predicates are missing for a term defined in
such a vocabulary (i.e., one marked explicitly as claiming to be an `Inrupt (or
Solid?) guideline following vocabulary`).

## Existing research / guidelines

- W3C [Best Practices for Publishing Linked Data](https://www.w3.org/TR/ld-bp/)
  from 2014. Offers pretty generic guidance (from a governmental data
  perspective).
- W3C [Data on the Web Best Practices](Data on the Web Best Practices) from
  2017. Contains some nice references to 
  [challenges](https://www.w3.org/TR/dwbp/#challenges) and
  [benefits](https://www.w3.org/TR/dwbp/#BP_Benefits) of following best
  practices, but is a very long document.
- FAIR principles: [Coming to Terms with FAIR Ontologies](https://dgarijo.com/papers/EKAW2020_Coming_to_Terms_with_FAIR_Ontologies.pdf)
- Richard Cyganiak: [RulesOfThumb](https://www.w3.org/wiki/User:Rcygania2/RulesOfThumb)
- Axel Polleres, Haller (2019): [Are We Better Off With Just One Ontology on the Web?](http://www.semantic-web-journal.net/system/files/swj2307.pdf)
- Widoco: [Vocab Best Practices](http://dgarijo.github.io/Widoco/doc/bestPractices/index-en.html)
- W3C (from 2008): [Best Practice Recipes for Publishing RDF Vocabularies](https://www.w3.org/TR/swbp-vocab-pub/)
  - Old now, talks mostly about configuring Apache to serve vocabs (with
   optional content negotiation), but Appendix B on Namespace IRI is still
   helpful.
- Axel Polleres et el (2018): [A More Decentralized Vision for Linked Data](https://epub.wu.ac.at/6371/1/IPM_workingpaper_02_2018.pdf)
  - Although more focused on instance data, it does describe the lack of
  vocabulary re-use in the real-world. 
  - Recommends strict adherence to either hash or slash namespaces.
  - It links to [A Systematic Analysis of Term Reuse and Term Overlap across Biomedical_Ontologies](https://www.researchgate.net/publication/298213412_A_Systematic_Analysis_of_Term_Reuse_and_Term_Overlap_across_Biomedical_Ontologies)
   (I didn't read it all (as it seems very 'dense'!), but the final short 
   sections 5.5 and 6 seem interesting in that they allude to solutions for term
   discovery and reuse).
  - "Hogan in his thesis [33, Section 5]"
- Vocabulary of Interlinked Datasets: [VoID](https://www.w3.org/TR/void/)
  - Mostly relevant for describing instance data, as opposed to vocabularies.
- Live OWL Documentation Environment: [LODE](https://essepuntato.it/lode/)

## High-Level Guidelines

### Serialization

- Write your vocabulary as [Turtle](https://www.w3.org/TR/turtle/).
  - Justification:
    - Turtle is widely accepted as being the most human-readable standardized 
      RDF serialization.

### Conventions for descriptions of terms

- A very generally accepted convention is that all the individual terms defined
  in any vocabulary will always provide both a short-form and a long-form
  description of that term.
  
  Inrupt prescribes a very simple heuristic to help when deciding what values to
  provide for these short- and long-form descriptions, namely that:

  - Short-form descriptions can be thought of as the User Interface (UI) label
    that might be displayed to the left of a Web form's text entry field on a
    webpage asking the user to enter a value for that property.
    
    For example, if a form asks for the user's telephone number, then the short-
    form description might be "Telephone number", and not "telephone number", or
    "telephone_number", or "TelephoneNumber", etc.
    
  - Long-form descriptions can be thought of as the UI tooltip value that might
    be displayed when a user hovers over that UIs text entry field on a website
    form asking the user to provide a value for that property.

    For example, if a form asks for the user's telephone number, then the long-
    form description might be something like, "The telephone number at which you
    can be contacted (can be your mobile number, or a landline number, or a work
    phone number, etc.).".

  Note: another, different, convention is that short-form descriptions can be
  thought of as placeholder replacements for that property in normal prose
  sentences. For example, the value from the above example might be "telephone
  number", so that it 'fits' more naturally within prose sentences like,
  "This property is your telephone number for use when...".
  But currently, Inrupt prescribes the UI-Form heuristic for vocabulary term
  descriptions.

- Full stops.
  - As a general rule, we recommend that short-form descriptive strings (e.g., 
    `rdfs:label`) don't end in full stops.
  - But longer descriptive strings (e.g., `rdfs:comment`, `dcterms:description`,
    `shacl:message`, etc.) do end with full stops (since they can often be
    made up of multiple sentences).
  
    The following example shows a single vocabulary term (i.e., the 'Shininess'
    property of Pet Rocks) that defines a short-, and a long-form description
    for this property (both in English):

    ```turtle
    pet:shininess a rdf:Property ;
      rdfs:label "Shininess (no full stop)"@en ;
      rdfs:comment """The shininess of a Pet Rock. Could be a long description of why 
                   shininess is so important to Pet Rock aficionados. So end with a full 
                   stop."""@en .
    ```

### Internationalization

- As a general rule, always provide explicit language tags for any textual
  information you provide in your vocabulary that is intended for humans to
  read, and which makes sense to be 'translatable'. 
  For example, nicknames are generally not expected to be translatable into
  other languages (so providing a language tag would not be expected), but
  someone's first name might be (e.g., "Patrick" in English will be written in
  Irish as "Pádraig" (or "Pádraic", or "Páraic"!)).
  
  - For instance, all Classes and Predicates in a vocabulary should provide
    `rdfs:label` and `rdfs:comment` values to provide short human-readable
    labels and longer human-readable descriptions of those terms. Adding
    explicit language tags makes it explicitly clear what human language those
    descriptions are in, and doing so also provides simple guidance and slight
    encouragement to also provide those same descriptions in other languages
    too. 
  
    ```turtle
    pet:PetRock a rdfs:Class ;
      rdfs:label "Pet Rock"@en ;
      rdfs:label "Peataí Rock"@ga ;
      rdfs:label "Pierre de compagnie (Pet Rock)"@fr ;
      rdfs:comment "Pet Rock is a collectible toy made in 1975 by advertising executive Gary Dahl."@en ;
      rdfs:comment "Is bréagán inbhailithe é Pet Rock a rinne an feidhmeannach fógraíochta Gary Dahl i 1975."@ga ;
      rdfs:comment "Pet Rock est un jouet de collection fabriqué en 1975 par le directeur de la publicité Gary Dahl."@fr .
    ```

- Explicitly provided language tags on textual information can be used by
  libraries to automatically provide internationalized UI elements, or error
  messages, or any human-readable information. For example, the Inrupt
  Artifact Generator can generate source-code from RDF vocabs that provides
  easy and direct programmatic access to these translations, such as this
  example that displays the Spanish comment associated with the 'shininess'
  property defined in Inrupt's example Pet Rock vocabulary:
  
    ```javascript
    import { PET_ROCK } from "@inrupt/generated-vocab-pet-rock";
    
    console.log(PET_ROCK.shininess.asLanguage("es").comment);
    ```

### Prefixes

- Use the lowercase SPARQL syntax for `prefix` and `base` (if you use `base` at
  all, as our guidance is to avoid it **_for vocabularies_**). See the NOTE in
  [Turtle 1.1 Spec IRIs Note](https://www.w3.org/TR/turtle/#sec-iri) for more
  information).
  
  - Justification:
    - We consider RDF 1.1 Turtle parsers to be widely deployed now, so choosing
    lowercase and the non-@-prefixed syntax shouldn't cause any problems.
    - It allows easier cut-and-paste into SPARQL queries. 
    - Readability (i.e., lowercase tends to 'scream' less!).
    - Consistency (i.e., just pick one, and stick with it - Inrupt has chosen
      lowercase).

- Use a short prefix for your vocabulary, but don't get caught up in
  trying to make the prefix **_too_** short. For example:

    ```turtle
    prefix my_vocab: <http://myCompany.com/ns/example/myVocab/>
    
    # Long prefixes are fine too, if justified...
    prefix fibo-loan-loan-mod: <https://spec.edmcouncil.org/fibo/ontology/LOAN/Loans/MetadataLOANLoans/>
    
    # ...but we recommend using underscores instead of hyphens (see below).
    prefix inrupt_ess_service_audit: <https://inrupt.com/vocab/ess/service/audit/>
    ```

- If you feel your prefix needs multiple words or acronyms (such as the example
  `inrupt_ess_service_audit` above), use underscores and not hyphens to separate
  the words.
  There's no particularly strong justification for this preference (i.e., either
  would be fine), it's just more important to pick one to maintain consistency
  across vocabs.
  
  But by choosing underscores, it generally means that programming-language
  constructs (like classes, variables, or method names) generated
  automatically from RDF vocabularies won't need to transform the hyphens,
  since hyphens are invalid characters in many programming languages (such as
  Java, C#, JavaScript, Python, etc.). So by choosing underscores in your
  vocabularies instead, you can maintain more naming consistency between your
  RDF and your source-code.

- Try to avoid existing commonly used prefixes if possible. Use
  [prefix.cc](http://prefix.cc/) to check if your preferred prefix is already in
  common use.
  But just be aware that `prefix.cc` is not actively curated, and that anyone is
  free to add prefixes as they see fit. So if your preferred prefix is already
  registered on prefix.cc, but on further investigation you determine that the
  existing vocabulary is no longer active, or is not in wide use, and that that
  existing vocabulary wouldn't conflict too much conceptually with your
  vocabulary, then feel free to use that prefix.
 
  Prefixes are only a convenient syntactic sugar available in some RDF
  serializations, and anyone using your vocabulary is completely free to apply
  whatever prefix they wish themselves (allowing them to avoid prefix name
  clashes if they do arise), but such instances should be extremely rare, and so
  our guidance is definitely to try and keep vocabulary prefixes as distinctly
  unique as possible.

- Consider registering your prefix at [prefix.cc](http://prefix.cc/) once you
  think your vocabulary's chosen namespace prefix and IRI will remain stable.

#### Recommended prefixes for common RDF vocabularies

- Use preferred namespace prefixes.
    - Justification: 
      - Consistency; Readability.

Note: Anyone can define whatever prefix they want when using vocabularies, so
for instance the Dublin Core Terms vocabulary is often used with different
prefixes, such as `dc:`, or `dct:`, or `dcterms:`, which can be confusing. Since
the [Turtle serialization of the vocabulary](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/dublin_core_terms.ttl)
itself uses the prefix `dcterms:`, so do we at Inrupt!

We also recommend using the common prefixes listed by the
[W3C's RDFa Core Initial Context](https://www.w3.org/2011/rdfa-context/rdfa-1.1).

Below we list prefixes that we use at Inrupt across all our RDF for
vocabularies, but whose prefix might not be immediately obvious, or is not
listed in the RDFa Core Initial Context above. (If you're unsure of a prefix
to use, try and look for existing usage of that vocabulary in our RDF, or just
ask at info@inrupt.com):

```turtle
# Many RDF examples in the wild use `dc:` or `dct:` instead of `dcterms:` (and
# indeed the RDFa Core Initial Context lists both `dc:` and `dcterms:`), but
# we have chosen to consistently use `dcterms:`, as this is the prefix the
# Dublin Core themselves use in their Turtle serialization.
prefix dcterms:  <http://purl.org/dc/terms/>

prefix xsd:      <http://www.w3.org/2001/XMLSchema#>
prefix ws:       <http://www.w3.org/ns/pim/space#>
prefix shacl:    <http://www.w3.org/ns/shacl#>
prefix solid:    <http://www.w3.org/ns/solid/terms#>
```

### Namespace IRI

- Use the HTTPS scheme for your vocabulary's namespace IRI (as opposed to HTTP).

- Use a trailing slash '/' on your vocabulary's namespace IRI (as opposed to a
  trialing hash '#').

  For more information on the distinction, see the W3C guidelines in
 [Appendix B: URI namespaces](https://www.w3.org/TR/swbp-vocab-pub/#naming).
  - Note: a hash namespace means the entire vocab will be retrieved in one GET
    request, whereas a slash namespace allows each individual term to
    be dereferencable individually (e.g., `https://schema.org/Person`).
  - Generally speaking, a slash namespace is more future-proof, as new levels of
    hierarchy can be added later, whereas a hash namespace can only ever have a
    single (root) level of hierarchy.  
  
- We strongly adovcate using a persistent URL (i.e., a 
  [PURL](https://en.wikipedia.org/wiki/Persistent_uniform_resource_locator)) to
  allow the links and references defined in your vocabulary to outlive your
  current organisation or personally owned domain. For example:
  - [PURL](https://purl.org/), but it seems the Internet Archive are no longer
   supporting it (i.e., they were not responding to update requests in 2019).
  - The [w3id.org project](https://w3id.org/).
  - Inrupt has choosen w3id.org to register its official PURL (which we can use
    to reference vocabularies, as well as sample datasets, and training
    materials, and tutorials, and employee WebID's, etc.). The registered PURL
    is: [https://w3id.org/inrupt/](https://w3id.org/inrupt/).

- Do **_not_** include version numbers in the Namespace IRI. The justification
  is simply the impracticalities of migrating namespaces in a global,
  distributed, and decentralised environment (see the "Term-centric Semantic Web
  Vocabulary Annotations" W3C editor draft for
  [an historical note on the lessons learned on this subject](https://www.w3.org/2003/06/sw-vocab-status/note#history)):

    ```turtle
    # Even though the terms defined by FOAF did change over time, the version
    # number in the namespace never did! 
    prefix foaf: <http://xmlns.com/foaf/0.1/>
    ```
 
  - Although it has been a fairly common practice (especially in W3C
    vocabularies) to include the year (and possibly month) as a simple form of
    versioning in Namespace IRI's... (for example:
  
    ```turtle
    prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> 
    ```
    
    ...our guidance is to **_not_** include any attempt at explicit versioning
    (including dates or years) in the Namespace IRI.
    
    An example of where this lesson has already been learned is described in 
    [FOAF extension and evolution](https://www.w3.org/2003/06/sw-vocab-status/note#history):

    *"It long ago became impractical to update the Namespace \[IRI\] without
    causing huge disruption to both producers and consumers of FOAF data. We are
    left with the digits "0.1" in our URI. This stands as a warning to all those
    who might embed metadata in their vocabulary identifiers."*
    
- Instead of attempting to version the Namespace IRI, we propose using explicit
  versioning meta-data within the vocabulary itself, including status,
  deprecation or versioning meta-data on individual terms, if appropriate. For
  more details, see the section [Versioning](#versioning) below.
   
## Providing meta-data for your vocabulary

It's very important that you describe your vocabulary, explaining its intent
and providing guidance for potential users of your vocabulary.

### Explicitly say your vocabulary is a vocabulary!

- Firstly, explicitly define your vocabulary **_as_** a vocabulary. Simply type
your vocabulary using the `Ontology` Class from the
[OWL](http://www.w3.org/2002/07/owl#) vocabulary, for example:

    ```turtle
    my_vocab: a owl:Ontology .
    ```

- Optionally, also type your vocabulary as an Inrupt Artifact Generator-aware
vocabulary to allow automatic machine enforcement of many of these guidelines, 
for example:

    ```turtle
    # NOTE: this Artifact Generator-aware vocabulary is subject to change, so we
    # don't provide the full Namespace IRI at the moment!
    my_vocab a owl:Ontology, inrupt-artifact-generator:Ontology .
    ``` 

### Describe the vocabulary

- Provide basic descriptions of your vocabulary, using `dcterms:title` for a
  short-form description, and `dcterms:description` for a longer, more detailed
  description. Use explicit language tags to denote the language, for example:

    ```turtle
    my_vocab: a owl:Ontology ;
      dcterms:title "My guidelines vocab"@en ;
      dcterms:title "Mon vocabulaire des recommandations"@fr ;
      dcterms:description "A longer description of my guidelines vocab."@en ;
      dcterms:description "Une description plus longue de mon vocabulaire des recommandations."@fr .
    ```

- Also provide `rdfs:seeAlso` references to any relevant documentation or
specifications that might relate to your vocabulary.

    ```turtle
    my_vocab: a owl:Ontology ;
      dcterms:title "My Solid-related vocab"@en ;
      rdfs:seeAlso <https://solid.github.io/specification/> .
    ```

### Provide preferred prefix and namespace hints

- Use the SHACL terms to explicitly describe your preferred Namespace IRI and
  prefix for your vocabulary.

    ```turtle
    my_vocab: a owl:Ontology ;
      sh:declare my_vocab:_PrefixDeclaration .
  
    my_vocab:_PrefixDeclaration a sh:PrefixDeclaration ;
      sh:prefix "my_common" ;
      sh:namespace "https://w3id.org/inrupt/namespace/vocab/common/"^^xsd:anyURI .
    ```

- Many existing vocabularies use the [VANN](http://purl.org/vocab/vann/)
  properties `preferredNamespacePrefix` and `preferredNamespaceUri` to describe
  these, but that vocabulary is not actively maintained, and it never provided
  any dereferenceable RDF (e.g., no Turtle).

- Remember that the preferred prefix value is only a suggestion to users of
  your vocabulary to encourage consistent usage across all users - but they
  are always free to choose whatever prefix they like, including none at all! 
- The values of `preferredNamespaceUri` should always be strings (and not
  IRI's), but explicitly typed as `xsd:URI` (see example above).

### Explicitly state the vocabulary license

- There are a number of options when it comes to describing the license under
  which you want your vocabulary to fall (see
  [Describing Copyright in RDF](https://creativecommons.org/ns#) for example),
  but Inrupt has chosen to use [DALICC](https://www.dalicc.net/).
  
- What license predicate to use?
  - Dublin Core Terms: `http://purl.org/dc/terms/license`
  - Creative Commons: `http://creativecommons.org/ns#license`
  - Both?
  - Inrupt has chosen to just use `dcterms:license`.

- What actual license value to use? All the following RDF representations of
  common licenses:
  - `http://purl.org/NET/rdflicense/MIT1.0.ttl`
  - `http://www.opendatacommons.org/licenses/pddl/`
  - `https://opensource.org/licenses/MIT`
  - `https://creativecommons.org/publicdomain/zero/1.0/`
  - Inrupt has chosen to just use MIT (specifically
    [dalicc:MIT](https://api.dalicc.net/web/license/MIT)).
  
- What other licensing properties should we use?  
  - `http://creativecommons.org/ns#attributionURL` ?

### Versioning

- Use the `owl:versionInfo` property to provide basic vocabulary version
  information.
  
  - Note: this version string can be appended to the filenames and classnames
    of generated programming-language source-code artifacts, such as those
    generated by the Inrupt Artifact Generator, that can be used to ease
    working with RDF vocabularies generally.

  - We follow the [SemVer](https://semver.org/) convention very common
    elsewhere in software engineering (and as recommended by the FAIR
    principles for vocabularies).

  - A very interesting idea (not yet adopted, or prescribed) by Inrupt) is to
    instead use the `owl:versionInfo` predicate to reference richer meta-data
    for each vocabulary change, e.g:
    
    ```turtle
    my_vocab:
        a owl:Ontology ;
        rdfs:isDefinedBy my_vocab: ;
        owl:versionInfo [
                dcterms:date "2017-06-06"^^xsd:date ;
                rdfs:seeAlso <https://github.com/me/my_vocab/pull/1> ;
                rdfs:isDefinedBy <https://raw.githubusercontent.com/me/my_vocab/randomblob372836/myvocab.ttl> ;
            ] ;
        owl:versionInfo [
                dcterms:date "2019-10-26"^^xsd:date ;
                rdfs:seeAlso <https://github.com/me/my_vocab/pull/2> ;
                rdfs:isDefinedBy <https://raw.githubusercontent.com/me/my_vocab/randomblob232829/myvocab.ttl> ;
            ] ;
        owl:versionInfo [
                dcterms:date "2020-01-02"^^xsd:date ;
                rdfs:seeAlso <https://github.com/me/my_vocab/pull/2> ;
                rdfs:isDefinedBy <https://raw.githubusercontent.com/me/my_vocab/randomblob989929/myvocab.ttl> ;
            ] .  
    ```
    
    Note: This example uses blank nodes, meaning we can't easily reference each
    individual version change, so using actual (relative) IRI's instead would be
    preferable.
  
  - As a further extension, we could look at using the [PROV-O](https://www.w3.org/TR/prov-o/)
    ontology to model these versioning changes even more richly and explicitly.

- Use the `owl:versionIRI` property to provide an explicit, unique IRI for
  this specific version of the vocabulary (as recommended by the FAIR
  principles for vocabularies).

- There are other OWL predicates we might consider in relation to versioning 
  too, for example `owl:priorVersion`, `owl:backwardCompatibleWith`, and 
  `owl:incompatibleWith`.
  
- Also keep in mind that OWL defines `owl:deprecated`, `owl:DeprecatedClass` and
  `owl:DeprecatedProperty`, and we recommend using `owl:deprecated` when
  appropriate (see usage examples [here](https://www.w3.org/2007/OWL/wiki/Quick_Reference_Guide)).
  
- Consider labelling individual terms in your vocabulary with 'status' values
  using the `term_status` term from the
  [SemWeb Vocab Status ontology](http://www.w3.org/2003/06/sw-vocab-status/ns#),
  particularly if a term is intended for testing, or considered unstable.

    ```turtle
    prefix :         <http://example.com/vocab/test-new-idea/>
    prefix status:   <http://www.w3.org/2003/06/sw-vocab-status/ns#>
    
    :propertyForTesting status:term_status "testing" .  
    :ExperimentalClass  status:term_status "unstable" .
    ```
  
- Kurt Cagle provides an interesting example of versioning in this
  [article](https://www.bbntimes.com/science/torn-between-uuids-and-friendly-iris-use-both)
  from Feb 2021. Here he uses UUID-based subject IRI's to refer to immutable
  versions of entities, and simple `owl:sameAs` triples to refer to a fixed,
  friendly-IRI to represent the unchanging conceptual entity. It's an
  interesting use of mixing human-friendly and UUID-based IRI's.  

### Describe who created this vocabulary

- Provide a reference to who (i.e., the organisation, or person) created this
  vocabulary using the `dcterms:creator` property. This property is intended to
  have a IRI as it's value, and so if you (or your organisation) has a WebID,
  then that would be the perfect value for this property. If you don't have a
  meaningful IRI to provide here, a simple textual string is also fine.

    ```turtle
    my_vocab dcterms:creator <https://w3id.com/inrupt/namespace/webid/inrupt> .
    ```  

- Also, feel free to include contributors using the `dcterms:contributor` 
  predicate (using individual's WebID's if possible):

    ```turtle
    my_vocab dcterms:contributor 
      <https://w3id.org/inrupt/namespace/webid/employee/12345> ,
      <https://w3id.org/inrupt/namespace/webid/employee/i-am-not-a-number> , 
      "friendly-volunteer@example.com" .
    ```  

## Terms (the Classes, Properties, Individuals and Constants)

### Naming conventions for Classes and Properties

A very widely accepted naming convention in RDF is to use uppercase first
letters when naming Classes, and lowercase first letters for properties. We
absolutely endorse and prescribe these conventions too, but we also extend this
slightly to also include the following naming guidelines too: 

 - Most identifiers in vocabularies are singular nouns, including Categories.
   So, the category of all people is called `Person` (e.g., `schema:Person`) and
   of all organizations is called `Organization` (e.g., `schema:Organization`).

 - Classes begin with a capital letter and are always singular, e.g.
   `skos:Concept`.

 - Properties begin with a lowercase letter, e.g., `rdfs:label`.

 - Object properties should be verbs, e.g., `org:hasSite`.

 - Data type properties should be nouns, e.g., `dcterms:description`.

 - Use camel case if a term has more than one word, e.g., 
   `foaf:isPrimaryTopicOf`.

 - Also camel case acronyms, e.g., the class of 'XML HTTP requests' would be
   `XmlHttpRequest`, and the (nightmarish!) property of 'supporting IPv6
   on iOS' would be `supportsIpv6OnIos`.
   - This topic alone could ignite endless debate, but our guideline is loosely
     based on the 
     [Google naming convention](https://google.github.io/styleguide/jsguide.html#naming-camel-case-defined).
   
### Use `rdfs:isDefinedBy`

- Each term in your vocabulary should link back to the vocabulary itself using
  the `rdfs:isDefinedBy` property. This just makes it easy for anyone
  dereferencing an individual term in isolation to link back to the overall
  vocabulary that defines that term.

#### Use `dcterms:issued` and `dcterms:modified`

- Use `dcterms:issued` (with the value typed as `xsd:date`) for your vocabulary
to represent its official issue date (i.e., original date of release).

- Update `dcterms:modified` (with the value typed as `xsd:date`) for your
vocabulary each time the vocabulary is modified.

- Use `dcterms:issued` for individual terms in your vocabulary if they were
issued after the official issue date of the vocabulary itself.

- Update `dcterms:modified` (with the value typed as `xsd:date`) for each
vocabulary term each time you update any meta-data for that term.

    ```turtle
    my_vocab: a owl:Ontology ;
      dcterms:issued "2010-01-01"^^xsd:date ; 
      dcterms:modified "2015-01-01"^^xsd:date . 
    
    my_vocab:prop1 a rdf:Property ;
      rdfs:comment "My first property - issued in original vocabulary."@en ;
      dcterms:modified "2015-01-01"^^xsd:date . 
    
    my_vocab:prop2 a rdf:Property ;
      rdfs:comment "My new property - issued after the original vocabulary."@en ;
      dcterms:issued "2020-10-02"^^xsd:date . 
    ``` 

### Domain and range

- In general the concepts of `domain` and `range` can be very helpful in
  communicating the intent of vocabulary properties (see 
  [explanation](https://stackoverflow.com/questions/9065709/difference-between-domain-and-range-in-rdf-schema)
  (better explanation?)). However, the standard RDF properties of `rdfs:domain`
  and `rdfs:range` have very rigid semantics, meaning they can cause problems
  when a vocabulary using them is used with higher-level RDF tooling (such as
  inferencing or reasoning). For a relatively lightweight introduction to these
  consequences, see
  [Inferencing](https://www.w3.org/standards/semanticweb/inference), or more
  formally [here](https://www.w3.org/TR/rdf-schema/#ch_properties), from the
  official specification.

  - So where possible use `gist:domainIncludes` and `gist:rangeIncludes`
    (see [Schema.org's justification](https://www.w3.org/wiki/WebSchemas/SchemaDotOrgMetaSchema)
    for more details on the rationale here, as the `gist` vocabulary shares the
    same rationale).
    
    Note that Dublin Core also defines very similar terms (e.g., 
    [DC 'rangeIncludes'](https://www.dublincore.org/specifications/dublin-core/dcmi-terms/#http://purl.org/dc/dcam/rangeIncludes)
    ), but our guidance is to use the `gist` terms (as it's a far more actively
    maintained vocabulary). 

## Publishing your vocabulary

- W3C (from 2008): [Best Practice Recipes for Publishing RDF Vocabularies](https://www.w3.org/TR/swbp-vocab-pub/)
  - Definitely support Content negotiation if at all possible, and support as
  many RDF seralizations as possible (i.e., just use any common RDF library to
  perform the appropriate serializations dynamically at runtime).

## General style guidelines

- Avoid using the `base` directive, as it prevents the use of any relative IRI's
  in the remainder of the vocabulary.
 
  - This is just a general guideline when working with RDF in general, as
    relative IRI's should generally be avoided **_in vocabularies_**, since they
    make it harder to process the vocabulary in isolation (i.e., any processor
    will need to provide a base IRI to convert any relative IRI's into absolute
    IRI's, as mandated by RDF itself). 

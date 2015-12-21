=============================================
Provenance and Confidence for NIF annotations
=============================================

NIF 2.1 introduces additional vocabulary to express provenance and confidence information for
annotations. This section will present two possible approaches to assign provenance and confidence
information to annotations:

* a more compact representation using previously introduced or provided *companion properties*
* a simpler, but more verbose representation using the generic ``nif:provenance`` and
  ``nif:confidence`` properties

For a running example, assume the following short example sentence::

  @prefix nif: <http://persistence.uni-leipzig.org/nlp2rdf/ontologies/nif-core#> .

  @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
  @prefix ex: <http://example.org/nif-21/acq#> .

  ex:doc_offset_0_52
    a nif:OffsetBasedString, nif:Context ;
    nif:beginIndex "0"^^xsd:nonNegativeInteger ;
    nif:endIndex "52"^^xsd:nonNegativeInteger ;
    nif:anchorOf "Apple acquired Metaio, an Augmented Reality company."^^xsd:string .  

.. _sec-comp-prop:

Provenance and Confidence using Companion Properties
====================================================

Entity Spotting and Linking
---------------------------

Sending this NIF document to an entity spotting and linking service, e.g. possibly a future revision
of the `FREME e-Entity DBpeida Spotlight Service`_ could yield and RDF result similar to:

.. _listing-comp-prop-entities:

.. literalinclude:: includes/nif21-acquisition-companion-properties.ttl
   :lines: 1-61
           
.. note::
  The current implementations of FREME services do not produce data as described here, thus also
  ``example`` is used instead of an acutally valid API version number. The discussed RDF data
  should rather be interpreted as suggestion/basis for discussion.
                    
The service introduced two NIF substring resources that were spotted as potential named
entities. Each substing resource carries several pieces of annotation information:

spotting information
  The mere fact that a certain substring has been identified as a (likely) reference
  to a named entity. This expressed in NIF 2.1 by assigning the ``nif:EnitityOccurrence`` class to
  the substring resource.

entity linking information (Likely) references to Linked Data identifiers for mentioned named
  entities or classification or referenced enities into one or several categories. For referencing,
  the ``itsrdf:taIdentRef`` property from `ITSRDF`_ is used.
  
Accroding to the *companion properties* approach, for each of the used annotating properties and
``nif:TextSpanAnnotation`` subclasses a pair of specific and related subproperties of
``nif:provenance`` and ``nif:confidence`` were introduced:

+----------------------+------------------------+------------------------+
|annotation            |provenance property     |confidence property     |
|property/class        |                        |                        |
+======================+========================+========================+
|nif:TextSpanAnnotation|nif:entityOccurrenceProv|nif:entityOccurrenceConf|
+----------------------+------------------------+------------------------+
|itsrdf:taIdentRef     |nif:taIdentProv         |nif:taIdentConf         |
+----------------------+------------------------+------------------------+

Provenance properties reference either ``prov:Agent`` or ``prov:Activity`` resources providing
details on either just the annotator (be it man or machine) or also additionally on the annotation
process. An outline for an agent description for our example::

  @prefix doap: <http://usefulinc.com/ns/doap#>
  @prefix prov: <http://www.w3.org/ns/prov#>
  @prefix freme: <http://freme-project.eu/example/>
  @prefix freme-api: <http://api.freme-project.eu/example>
  
  freme-api:description%2Fe-entity%2Fdbpedia-spotlight%2Fdocuments
    a prov:SoftwareAgent, doap:Version ;
    doap:shortdesc "NIF REST API for entity recognition and linking us  ing DBPedia Spotlight engine" ;
    doap:revision "0.x (example)" .
    # [...]
  
  freme:description#project
    a doap:Project ;
    doap:release freme-api:description%2Fe-entity%2Fdbpedia-spotlight%2Fdocuments ;
    doap:vendor freme:description#consortium .
    # [...]
  
Confidence properties provide a numeric measure for the degree of certainty of annotating agent
when assigning the annotation as a rational number between 0 and 1.

Obviously only one property assertion for a specific companion property can be made for the same
``nif:String`` resource without causing ambiguity. Thus, whenever several alternative annotations on
the same aspect are to be expressed, additional ``nif:AnnotationUnit`` resources can be created and
linked to the ``nif:String`` annotated resource. In the example, such an ``nif:AnnotationUnit`` is
used for the alternative, less probable entity link for string ``ex:doc_offset_0_5`` to
``dbp:Apple_Bank_for_Savings``. The same ``nif:AnnotationUnit`` resource can be (re-)used to host
multiple annotation statements with provenance and confidence via companion properties, as long as
unequivocalness is ensured.

To ensure possibilities to validate such unambiguity and to ensure that provenance and confidence
information using companion properties is completely machine actionable, explicit links between
companion properties and their corresponding annotating vocabulary items, as in this excerpt of the
current NIF 2.1 Core ontology draft::

  nif:EntityOccurrence nif:confidenceProperty nif:entityOccurrenceConf ;
    nif:provenanceProperty nif:entityOccurrenceProv .

  nif:TermOccurrence nif:confidenceProperty nif:termOccurrenceConf ;
    nif:provenanceProperty nif:termOccurrenceProv .

  itsrdf:taIdentRef nif:confidenceProperty nif:taIdentConf ;
    nif:provenanceProperty nif:taIdentProv .

  itsrdf:taClassRef nif:confidenceProperty nif:taClassConf ;
    nif:provenanceProperty nif:taClassProv .

Terminology Annotation
----------------------

In a similar way as presented for named entities information about term recognition and referencing
can be provided, by APIs like the `FREME e-Terminology Service`_ a possible addtion to the result
listing in :ref:`the result listing of the previous section <listing-comp-prop-entities>` could be:

.. literalinclude:: includes/nif21-acquisition-companion-properties.ttl
   :lines: 62-

Using only Generic Provenance and Confidence Properties
=======================================================

Usage of compation properties allows to offer a default value for each annotation aspect and allows
two reduce the number of ``nif:AnnotationUnit`` resources that must be synthesized to prevent
ambiguities. However, they also increase technical complexity for consumption of provenance and
confidence information. Using exclusively the generic ``nif:provenance`` and ``nif:confidence``
properties directly simplifies generation and consumption of this information, at the cost of
additional RDF resources required to express equivalent data. Using only these generic properties to
express the same annotation as discussed in :ref:`sec-comp-prop`:


.. _listing-gen-prop:

.. literalinclude:: includes/nif21-acquisition-generic-properties.ttl
           
.. note::
  ``nif:confidence`` and ``nif:provenance`` can only be attatched to ``nif:AnnotationUnit``
  instances, not to ``nif:String`` instances directly.

           
.. _FREME e-Entity DBpeida Spotlight Service:
    http://api.freme-project.eu/doc/0.4/api-doc/full.html#!/e-Entity/execute
.. _FREME e-Terminology Service:
    http://api-dev.freme-project.eu/doc/api-doc/full.html#!/e-Terminology/e_terminology
.. _ITSRDF: https://github.com/w3c/itsrdf


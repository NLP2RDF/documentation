=========================================================
Representing Translated String and Translation Candidates
=========================================================

NIF 2.1 introduces a new kind of ``nif:Context``, ``nif:Translation`` that are used to represent
translations or translation candidates. These translation are linked bi-directionally with the
source context via ``nif:translation`` and ``nif:wasTranslatedFrom``. Use ``nif:predLand`` to
specify the target language of the translation process and ``nif:tranlationProv`` and
``nif:translationConf`` for additional provenance/confidence metadata for the translation activity.

The following simple example also contains entity links in source and target contexts, to
demonstrate that the whole expressivity of NIF struture and NIF-based annotation is equally
applicable for translation contexts:

.. literalinclude:: includes/nif21-translation.ttl

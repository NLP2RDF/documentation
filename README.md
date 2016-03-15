# documentation
sources for NIF documentation (host by [Read The Docs](readthedocs.org))

# setting up local preview
Get required Python libs using `pip`:
    pip install -r requirements.txt

Build documentation once:

    cd docs
    make html

Preview with automatic reload on changes:

    cd docs
    sphinx-autobuild -a -r _build . _build_auto

**NOTE:** Please use the issue tracker of this repository only for issues that
 are directly related to media content (text, figures) for the RTD documentation
 sites or implementation details for these sites.
 More specifically, please use the [NIF ontologies issue tracker](https://github.com/NLP2RDF/ontologies/issues/)
 for all discussions about the NIF vocabulary itself and it's usage.

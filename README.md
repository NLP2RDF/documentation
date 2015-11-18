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
    sphinx-autobuild . _build_html

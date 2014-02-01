alissapajer.github.io
=====================

luneysite

redirects to alissapajer.com

The `master` branch should never be edited by hand. All the code that generates this website lives in the `source` branch.

How to deploy:
From the `source` branch, run the following:

`$ ghp-import _site/ -b master -m "commit message"`

This will copy the contents of the `_site/` directory (where Hakyll generates its content) to the master branch. This can then be pushed manually.

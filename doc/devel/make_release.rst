.. _release-guide:

**********************************
Guide to making a datarray release
**********************************

A guide for developers making a datarray release.

.. _release-checklist:

Release checklist
=================

* Review the open list of `datarray issues`_.  Check whether there are
  outstanding issues that can be closed, and whether there are any issues that
  should delay the release.  Label them !

* Review and update the release notes.  Review and update the :file:`Changelog`
  file.  Get a partial list of contributors with something like::

      git shortlog -ns 0.6.0..

  where ``0.6.0`` was the last release tag name.

  Then manually go over ``git shortlog 0.6.0..`` to make sure the release
  notes are as complete as possible and that every contributor was recognized;

* Use the opportunity to update the ``.mailmap`` file if there are any
  duplicate authors listed from ``git shortlog -ns``;

* Add any new authors to the ``AUTHORS`` file.  Add any new entries to the
  ``THANKS`` file;

* Check the copyright years in ``doc/source/conf.py`` and
  ``datarray/LICENSE``;

* Check that the ``README.rst`` text is the same as the text in the
  ``long_description`` field in ``version.py``;

* If you have travis-ci_ building set up you might want to push the code in its
  current state to a branch that will build, e.g::

    git branch -D pre-release-test # in case branch already exists
    git co -b pre-release-test

* Clean::

    git clean -fxd

* Make sure all tests pass on your local machine (from the datarray root
  directory)::

    nosetests --with-doctest datarray

  Do this on a Python 2 and Python 3 setup.

* Consider running the same tests after installing into a virtualenv, to test
  that installing works correctly::

    mkvirtualenv datarray-test
    pip install nose wheel
    git clean -fxd
    python setup.py install
    mkdir for_test
    cd for_test
    nosetests --with-doctest datarray

* Check the documentation doctests::

    cd doc
    make doctest
    cd ..

* The release should now be ready.

Doing the release
=================

* Edit :file:`datarray/version.py` to set ``_version_*`` strings to the
  version you want.  Make ``_version_extra`` be the empty string for the
  release;

* Check you are getting the version / package name that you want by doing::

    git clean -fxd
    python setup.py sdist --formats=gztar,zip
    python setup.py bdist_wheel

  and checking the output filenames in ``dist/``;

* Make a signed tag for the release with tag of form ``0.6.0``::

    git tag -s -m 'Fifth public release' 0.6.0

* Once everything looks good, upload the source release to PyPi, using `twine
  <https://pypi.python.org/pypi/twine>`_::

    twine upload dist/datarray*

* Remember you'll need your ``~/.pypirc`` file set up right for this to work.
  See `setuptools intro`_.  The file should look something like this::

    [distutils]
    index-servers =
        pypi

    [pypi]
    username:your.pypi.username
    password:your-password

    [server-login]
    username:your.pypi.username
    password:your-password

* Check how everything looks on pypi - the description, the packages.

* Push the tag with ``git push origin 0.6.0``

* Push the documentation up to github with::

    cd doc
    make github

* Edit ``datarray/version.py`` to set to the next upcoming version.  Set
  ``_version_extra`` to ``dev``. Commit and push.

* Announce to the mailing lists.

.. datarray code stuff
.. _datarray github: http://github.com/bids/datarray
.. _datarray pypi: http://pypi.python.org/pypi/datarray
.. _datarray issues: http://github.com/bids/datarray/issues
.. _datarray travis-ci: https://travis-ci.org/bids/datarray

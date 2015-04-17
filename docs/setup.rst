.. _voevent-setup:

===================================
Finding and installing useful tools
===================================

We'll start by installing two useful tools for working with VOEvents in the
`Python`_ programming language. It's impossible to provide comprehensive
instructions for every possible operating system, but the good news is that
the tools are generally pretty easy to install. We'll discuss then general
principles, and go through step-by-step instructions for `Ubuntu`_ `14.04.2`_
step by step.

.. _Python: http://www.python.org/
.. _Ubuntu: http://www.ubuntu.com/
.. _14.04.2: http://releases.ubuntu.com/14.04.2/

Background and dependencies
===========================

In general, we suggest relying on package management tools where possible,
rather than attempting to compile everything from scratch. For Python
packages, we'll use `pip`_; for system libraries, use a package manager
appropriate to your operating system (`APT`_ for `Debian`_, Ubuntu and related
systems; `Macports`_ or `Homebrew`_ on `OS X`_, etc). If you don't have
permission to use one of these systems, ask your system administrator nicely
(or, better, get yourself a cheap-as-chips VM from `Amazon EC2`_ or `Digital
Ocean`_ or similar).

Note that we depend on Python 2.7; not all of the tools described have yet
been ported to Python 3. This is unfortunate, but `you can help`_.

We suggest using `Virtualenv`_ to create an isolated environment for
installing the Python packages we'll need. That means you don't need any
special privileges, and you can blow the whole thing away and start over
when you make a mistake simply by running ``rm``.

In addition to Python, pip and Virtualenv, you will need to install `libxml2
and libxslt`_. You'll need the header files for `zlib`_ (the library itself
was almost certainly installed along with your operating system). You'll also
find `IPython`_ useful for following along with our examples. On Ubuntu::

  # Install dependencies.
  $ sudo apt-get install libxml2-dev libxslt-dev zlib1g-dev python-dev python-pip python-virtualenv ipython
  [...]

  # Create a Virtualenv for working in and activate it. The name is arbitrary;
  # call it whatever you like.
  $ virtualenv voevent-venv
  New python executable in voevent-venv/bin/python
  Installing setuptools, pip...done.
  $ . voevent-venv/bin/activate

You will need to source the ``activate`` script in this way every time you
want to use this environment.

.. _pip: https://pip.pypa.io/
.. _apt: https://en.wikipedia.org/wiki/Advanced_Packaging_Tool
.. _Debian: http://www.debian.org/
.. _Macports: http://www.macports.org/
.. _Homebrew: http://brew.sh/
.. _OS X: http://apple.com/osx/
.. _Amazon EC2: https://aws.amazon.com/ec2/
.. _Digital Ocean: https://www.digitalocean.com/
.. _you can help: https://twistedmatrix.com/trac/wiki/Plan/Python3
.. _Virtualenv: https://virtualenv.pypa.io/
.. _libxml2 and libxslt: http://xmlsoft.org/
.. _zlib: http://zlib.net/
.. _IPython: http://ipython.org/

Reading and writing VOEvents: voevent-parse
===========================================

For manipulating the contents of VOEvents, we'll use the `voevent-parse`_
library. After you have set up and activated your Virtualenv, as above,
installing voevent-parse is easy using pip::

  $ pip install voevent-parse

The compilation will take a few moments. Then you can check if it is properly
installed by running::

  $ ipython
  [...]
  In [1]: import voeventparse

  In [2]: voeventparse.__version__
  Out[2]: 0.7.0

If you aren't familiar with the IPython shell, it's worth your while to spent
a few minutes getting used to it at this point.

This document will provide an introduction to using voevent-parse, but for the
full story you should refer to its `fine documentation`_.

.. _voevent-parse: https://github.com/timstaley/voevent-parse
.. _fine documentation: https://voevent-parse.readthedocs.org/

Sending and receiving VOEvents: Comet
=====================================

For transmitting VOEvents using VTP, we'll use `Comet`_ (:ref:`Swinbank, 2014
<swinbank-2014>`). Installation proceeds in just the same way as for
voevent-parse::

  $ pip install comet

Unlike voevent-parse, Comet is not a library you will import into Python, but
is rather invoked through ``twistd``, a stand alone tool. You can check that
it is installed by running::

  $ twistd comet --help
  Usage: twistd [options] comet [options]
  Options:
    -r, --receive                   Listen for TCP connections from authors.
    -b, --broadcast                 Re-broadcast VOEvents received.
  [...]

It's also worth running the Comet test suite to check that everything has been
installed properly::

  $ trial comet
  comet.handler.test.test_relay
    EventRelayTestCase
        test_interface ...                  [OK]
            test_name ...                   [OK]
  [...]
  Ran 144 tests in 0.443s

  PASSED (successes=144)

All tests should succeed.

Again, this guide will cover the basics of using Comet, but it is well worth
your while to read `its documentation`_.

.. _Comet: https://github.com/jdswinbank/Comet
.. _its documentation: http://comet.transientskp.org/

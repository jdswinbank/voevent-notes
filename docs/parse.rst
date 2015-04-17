.. _voe-parse:

=============================
Manipulating VOEvents in code
=============================

We've :ref:`seen <voevent-reading>` that VOEvents are just text documents, and
you can happily read and write them in your favourite text editor. That's fine
as far as it goes, but it's not a practical approach for building a large
scale event handling system. Luckily, one of the advantages of using XML to
encode VOEvents is that we can tap into a wide range of infrastructure and
tools which have been developed specifically for processing XML. Effectively
every mainstream programming language has a whole suite of libraries for
handling XML available---see, for example, the documentation for the `Python
standard library`_.

While it's possible to directly apply these generic XML handling tools to
VOEvents, it's even more convenient to use a libary which has been especially
developed to work with VOEvents. Here, we are using `voevent-parse`_ which
does just that. Voevent-parse builds on the popular `lxml`_ library: while
we'll cover all the material you need to get started here, we refer you to
`lxml's documentation`_ for details.

.. _Python standard library: https://docs.python.org/2/library/xml.html
.. _voevent-parse: https://github.com/timstaley/voevent-parse/
.. _lxml: http://lxml.de/
.. _lxml's documentation: http://lxml.de/index.html#documentation

Parsing a VOEvent
=================

Let's start by reading some basic data from a VOEvent. We'll save the example
we looked at :ref:`earlier <voevent-reading>` to a file---say,
``voevent.xml``---and read that into Python. You can follow along using
IPython.

First, we import the ``voeventparse`` library into Python, and use it to load
the ``voevent.xml`` file from disk::

   In [1]: import voeventparse as vp

   In [2]: with open('voevent.xml') as f:
      ...:     v = vp.load(f)
      ...:

   In [3]: v
   Out[3]: <Element VOEvent at 0x104747560>

The basic "attributes" (the ``role`` and the ``ivorn``, as well as some of the
XML boilerplate that we skipped over) of the root ``VOevent`` element are accessible as a
dictionary on ``v``::

   In [4]: v.attrib['ivorn']
   Out[4]: 'ivo://example/exciting_events#123'

   In [5]: v.attrib['role']
   Out[5]: 'test'

We can also descend into the sub-elements of the event and retrieve the key
information::

   In [6]: v.Who.Author.title
   Out[6]: 'Hotwired VOEvent Tutorial'

   In [7]: v.What.Group.Param.Description
   Out[7]: 'Peak Flux'

   In [8]: v.What.Group.Param.attrib['value']
   Out[8]: '0.0015'

Wait a minute---there are actually two ``Param`` elements inside ``What``. How
do we get the integrated flux? Two possibilities, actually. First, you can
iterate over the children of the parent element::

   In [9]: for element in v.What.Group.iterchildren():
      ...:     print element.attrib['name'], ": ", element.attrib['value']
      ...:
   peak_flux : 0.0015
   int_flux : 2.0e-3

Alternatively, you can search for particular elements::

   In [10]: v.find(".//Param[@name='int_flux']").attrib['value']
   Out[10]: '2.0e-3'

It's worth noting that the `VOEvent schema`_ does *not* guarantee that a
VOEvent will provide a ``Param`` with a name of ``int_flux``. The flexibility
of VOEvent is both a blessing and a curse here: since different sources of
events will likely report on quite different types of observation, it's hard
to make sense of them without already having some idea of what you expect to
find inside. The good news is that you'll probably have quite a good idea of
which event *streams* you're interested in, and you can expect (although it's
not guaranteed...) events with in a particular stream to follow a regular
structure.

On the subject of the schema: you'll come across all sorts of weird
constructs that people have stashed in VOEvents, and you might even be tempted
to come up with some yourself. Often, folks will be able to figure out what
you mean, and XML handling tools generally try to be quite liberal in what
they'll accept. However, the schema exists for a reason: if you stray outside
its boundaries, all bets are off regarding whether your events will be
interpreted in the way you intend. Indeed, many systems will simply refuse to
accept an event which isn't valid according to the schema. Conveniently,
voevent-parse can check this for you::

   In [11]: vp.valid_as_v2_0(v)
   Out[11]: True

Note that we are checking against the VOEvent version 2.0 schema, which is the
latest version and is definitely what you should be using.

.. _VOEvent schema: http://www.ivoa.net/xml/VOEvent/VOEvent-v2.0.xsd

Creating a VOEvent
==================

Having seen how we can use voevent-parse to read a VOEvent, the next step is
to create one. In order to make this a bit more interesting, let's use a
real transient. `Gaia`_ has recently started publishing `Photometric Science
Alerts`_ to a publicly-accessible website, but they aren't yet available as
VOEvents. Let's pick on the following:

+-----------+------------+------------+-----------+-------------+-----------+----------+----------+---------+-------------------------------+
| Name      | Observed   | Published  | RA (deg.) | Dec. (deg.) | Magnitude | Historic | Historic | Class   | Comment                       |
|           |            |            |           |             |           | mag.     | scatter  |         |                               |
+===========+============+============+===========+=============+===========+==========+==========+=========+===============================+
| Gaia14adi | 2014-11-07 | 2014-12-02 | 168.47841 | -23.01221   | 18.77     | 19.62    | 0.07     | unknown | Fading source on top of 2MASS |
|           | 01:05:09   | 13:55:54   |           |             |           |          |          |         | Galaxy (offset from bulge)    |
+-----------+------------+------------+-----------+-------------+-----------+----------+----------+---------+-------------------------------+

.. _Gaia: http://sci.esa.int/gaia/
.. _Photometric Science Alerts: http://gsaweb.ast.cam.ac.uk/alerts/

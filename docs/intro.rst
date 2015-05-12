.. _intro:

===========================================
What is a VOEvent and why would I want one?
===========================================

The transient deluge
====================

Systems for detecting astrophysical transients are becoming increasingly
ubiquitous and high volume: from space-based gamma-ray monitors like NASA's
`Fermi`_ and `Swift`_ missions, through present-day ground based surveys in
both the optical (e.g. the `Catalina Real-Time Transients Survey`_ and the
`Palomar Transients Factory`_) and radio (the `LOFAR Radio Sky Monitor`_ and
`ASKAP`_'s `Variables and Slow Transients`_ survey), to future facilities such
as the `Square Kilometre Array`_ (SKA) and `Large Synoptic Survey Telescope`_
(LSST). LSST alone promises to detect and announce up to 40 million transients
per night when its survey begins early in the 2020s.

Obtaining the best scientific results from these facilities requires timely
and accurate follow-up observations of the most relevant detections. However,
it is obviously humanly impossible---even given a *large* cohort of graduate
students---to read and understand millions of transient alerts per night, let
alone to select those which merit further observation. Even were it possible,
humans are slow: slow at reading, but, even more so, slow at negotiating with
their peers to organize telescope time.

.. _Fermi: http://fermi.gsfc.nasa.gov/
.. _Swift: http://swift.gsfc.nasa.gov/
.. _Catalina Real-Time Transients Survey: http://crts.caltech.edu/
.. _Palomar Transients Factory: http://www.ptf.caltech.edu/
.. _LOFAR Radio Sky Monitor: http://www.transientskp.org/
.. _ASKAP: http://www.atnf.csiro.au/projects/askap/index.html
.. _Variables and Slow Transients: http://www.physics.usyd.edu.au/sifa/vast/index.php/Main/HomePage
.. _Square Kilometre Array: http://www.skatelescope.org/
.. _Large Synoptic Survey Telescope: http://www.lsst.org/

.. _voevent-standard:

The VOEvent standard
====================

It is this problem which VOEvent seeks to alleviate. It provides a
standardized, machine-readable means of describing transient celestial events.
It enables the author to specify:

* *Who* they are;
* *What* they have observed;
* *Where and when* the observed it;
* *How* the observations were made;
* *Why* they think it is of general interest to the community.

In addition, it is possible for them to provide citations to previously
announced events, and to refer to other content outside the context of the
VOEvent itself. In short, the aim is to provide the reader not just with a
notification that something was observed, but to encapsulate as much
information about it as is practical, so that they can make an informed
decision about whether to perform a follow-up observation.

Let's emphasize that last sentence again: the *recipient* of a VOEvent makes a
decision about whether to perform any action in response to a VOEvent. The
event itself does not constitute a trigger or an instruction to the recipient,
and it does not suggest what sort of follow-up might be appropriate. It
describes only the event as seen by the observer.

VOEvents are `XML`_ documents. XML is a plain text "markup" language: it makes
it possible to annotate a document to show its structure in a way that a
computer can understand, while also being human readable (for a broad
definition of "readable" and a fairly tolerant human). The VOEvent standard
defines an XML "schema", which describes the subset of all possible constructs
which it is legal to use in a VOEvent document. As we'll see :ref:`later
<voevent-reading>`, it's perfectly possible to read and write VOEvents using
your favourite text editor. However, that's a lot of effort; there is a range
of tools and libraries you can deploy to make your life easier. We'll :ref:`cover
them too <voe-parse>`.

The VOEvent standard was defined by :ref:`Seaman et al. (2011)
<seaman-2011>` and it has an official stamp of approval from the `IVOA`_. It
is currently curated by the IVOA's `Time Domain Interest Group`_, or TDIG.

.. _IVOA: http://www.ivoa.net/
.. _Time Domain Interest Group: http://www.voevent.org/
.. _XML: http://www.w3.org/XML/

Distributing VOEvents
=====================

It's important to realise that the VOEvent standard says nothing of any
significance about what you should do with a VOEvent once you've got one. In
particular, it might seem reasonable to assume that once the discoverer of a
transient has encapsulated it in a VOEvent document, they might wish to
distribute it to the community, but the mechanisms by which they can do that
are *not* defined by the standard. It's perfectly to do this by any convenient
means: e-mail, post, semaphore, ...

This is both a blessing and a curse. It's good architecture to separate the
representation of events from their transmission, and it means the standard
can be relatively concise and manageable. Plus there's a great deal of
flexibility here: if a project adopts VOEvent, it doesn't tie them into some
particular networking infrastructure. However, for somebody who just wants to
find news on the latest transients, or who just wants to ensure that their
events are available to the community, there's no obvious place to start.

This situation is partially mitigated by the `VOEvent Transport Protocol`_
(VTP; :ref:`Allan & Denny, 2009 <allan-2009>`). This is an IVOA Note, rather
than a Recommendation; in other words, it is a suggestion which has not been
through the IVOA's rigorous review process. There is a `revised version`_ of
this protocol in preparation, with the hope of developing it into a
fully-fledged IVOA standard in future. The changes are intentionally being
kept minimal, and focus more on resolving ambiguities in the specification
than on changing the on-the-wire protocol.

VTP is minimal by design: it builds upon low-level networking primitives to
create something that gets the job done. Arguably, a more modern system would
be layered on top of higher level standards, like `HTTP`_, which would provide
a more robust basis and make it easier to take advantage of existing
infrastructure. However, VTP is the nearest we have to a standard at the
moment, and it certainly provides mechanisms for sending and receiving streams
of VOEvents with relatively low latency. This document :ref:`will describe
<voevent-receiving>` how to connect up to and participate in the existing VTP
network.

Higher level functionality
==========================

We conclude this section with a warning. All of the tools described here are
fairly low-level: they provide mechanisms for reading, writing and exchanging
individual VOEvent packets. This is necessary but not sufficient to service
future science projects. We need to build upon these lower level tools to
construct event aggregators and filters and classifiers and distribution
systems which make it possible for the astronomer to reason about and respond
to VOEvents in bulk. While some important work has been done on this (e.g.
:ref:`Williams et al, 2009 <williams-2009>`, :ref:`Poci et al, 2015
<poci-2015>`), there's still a long way to go. Maybe after reading this you
will be inspired to contribute.

We'll return to this point later in considering :ref:`some of the challenges
<voevent-future>` facing VOEvent in the future.

.. _VOEvent Transport Protocol: http://www.ivoa.net/documents/Notes/VOEventTransport/
.. _revised version: https://github.com/jdswinbank/vtp
.. _HTTP: http://www.w3.org/Protocols/


===========================================
What is a VOEvent and why would I want one?
===========================================

The transient deluge
====================

Systems for detecting astrophysical transients are becoming increasingly
ubiquitous and high volume: from space-based gamma-ray monitors like NASA's
`Fermi`_ and `Swift`_ missions, through present-day ground based surveys in
both the optical (e.g. the `Catalina Real-Time Transients Survey`_ and the
`Palomar Transients Factory`_) and radio (the `LOFAR Radio Sky Monitory`_ and
`ASKAP's`_ `Variables and Slow Transients`_ survey), to future facilities such
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

The VOEvent standard
====================

It is this problem which VOEvent seeks to alleviate. It provides a
standardized, machine-readable means of describing transient celestial events.
It enables the author to specify:

* *Who* they are;
* *What* they have observed;
* *Where and when* the observed it;
* *How* the observations were made;
* *Why* they think it is of general interst to the community.

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
of tools and libraries you can deploy to make your life easier. We'll cover
them too.

The VOEvent standard was defined by :ref:`Seaman et al. (2011)
<seaman-2011>` and it has an official stamp of approval from the `IVOA`_. It
is currently curated by the IVOA's `Time Domain Interest Group`_, or TDIG.

Distributing VOEvents
=====================


.. _Fermi: http://fermi.gsfc.nasa.gov/
.. _Swift: http://swift.gsfc.nasa.gov/
.. _Catalina Real-Time Transients Survey: http://crts.caltech.edu/
.. _Palomar Transients Factory: http://www.ptf.caltech.edu/
.. _LOFAR Radio Sky Monitor: http://www.transientskp.org/
.. _ASKAP: http://www.atnf.csiro.au/projects/askap/index.html
.. _Variables and Slow Transients: http://www.physics.usyd.edu.au/sifa/vast/index.php/Main/HomePage
.. _Square Kilometre Array: http://www.skatelescope.org/
.. _Large Synoptic Survey Telescope: http://www.lsst.org/
.. _IVOA: http://www.ivoa.net/
.. _Time Domain Interest Group: http://www.voevent.org/
.. _XML: http://www.w3.org/XML/

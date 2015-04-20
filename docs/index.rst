================
Notes on VOEvent
================

`VOEvent`_ is the `International Virtual Observatory Alliance`_ (IVOA)
recommended mechanism for describing astronomical transients. Specifically, the
VOEvent standard :ref:`(Seaman, 2011) <seaman-2011>`

  defines the content and meaning of a standard information packet for
  representing, transmitting, publishing and archiving information about a
  transient celestial event, with the implication that timely follow-up is of
  interest.

This document provides an introduction to working with VOEvents. It is not
normative; rather, it represents the authors' own particular biases and
experiences. This material is intended to accompany the "VOEvent Hands-On
Session" presented at `Hot-Wiring the Transient Universe IV`_ (henceforth
"Hotwired 4") in May 2015; we hope, though, that it will prove generally
useful.

We assume a working knowledge of `Python`_.

.. _VOEvent: http://www.voevent.org/
.. _International Virtual Observatory Alliance: http://www.ivoa.net/
.. _Hot-Wiring the Transient Universe IV: http://lcogt.net/hotwired-iv-welcome
.. _Python: http://www.python.org/

.. toctree::
   :maxdepth: 2

   intro
   setup
   reading
   parse
   receiving
   sending
   future
   bib

.. _voevent-future:

=================
Future challenges
=================

As described, VOEvent and its surrounding infrastructure meets the twin goals
of describing transient events in a machine readable format and of providing a
relatively low-latency system for distributing them to end users.  However,
the system is not without its drawbacks, and there remain substantial
challenges to be solved both in delivering results that are of the greatest
scientific relevance and in scaling to address next-generation surveys. Here,
we highlight just a few areas where we suggest more work is needed.

It's worth emphasizing that the aim here is not to dimiss the achievements of
the VOEvent community to date, or question its viability in the future.
Rather, see this as a call-to-arms, and please jump in when you have something
to contribute.

.. _voevent-auth:

Authentication and verifiability
================================

:ref:`We made a VOEvent <voevent-create>`. Further, we made VOEvent based on
information we read on the web somewhere; it's not something we observed, or
that we analysed ourselves. We were careful to indicate in that VOEvent that
we had no authority to speak on behalf of Gaia, but there was no check, no
requirement to prove our identity. If we'd written that we were the `Gaia
Photometric Science Alerts Team`_, nobody would have denied it. Of course,
actually :ref:`persuading a broker to accept <voevent-sending>` our event
might be harder, but that's entirely dependent on the owner of the broker.

So: are you going to trigger your `billion Euro telescope`_ based on something
that *might* be a genuine alert from Gaia? Well, it's your call...

Broadly, there are two ways you might try to tackle this problem. One is to
authenticate the transport mechanism: you know that this is a genuine event
because you got it directly from Gaia. That's possible using VTP, since Gaia
could run a broker and you could susbcribe to it directly. It nullifies the
advantages of the distributed VOEvent network, though, and it means that
effectively every project distributing events will have to run their own
broker, or find some way of delegating permissions.

An alternative is to authenticate the events themselves. Potentially, this
could be done by applying a `cryptographic signature`_ to the events when they
are generated. A couple of proposals have been made along these lines: one
involving `OpenPGP`_ signatures (:ref:`Denny, 2008 <denny-2008>`) and one
based on `XML Digital Signature`_ with `X.509 certificates`_ (:ref:`Allen,
2008 <allen-2008>`).

Unfortunately, neither of these systems have seen significant adoption by the
community. Partly, that's because on the small scales we have today, the risk
is minimal. That happy situation will not last. Further, both of the potential
solutions are complex. Applying a signature to an XML document is a fragile
process: XML Digital Signature attempts to make it robust by formalizing a
complex "canonicalization" procedure which must be carried out prior to
signing, while the OpenPGP approach adopts an ad-hoc series of contortions
which is simpler but error prone. Neither approach is ideal. And that's even
before we've started discussing how to set up an appropriate `public key
infrastructure`_...

.. _Gaia Photometric Science Alerts Team: http://gaia.ac.uk/selected-gaia-science-alerts
.. _billion Euro telescope: http://www.eso.org/public/teles-instr/e-elt/
.. _cryptographic signature: https://en.wikipedia.org/wiki/Digital_signature
.. _OpenPGP: http://openpgp.org
.. _XML Digital Signature: http://www.w3.org/TR/xmldsig-core/
.. _X.509 certificates: https://en.wikipedia.org/wiki/X.509
.. _public key infrastructure: https://en.wikipedia.org/wiki/Public_key_infrastructure

.. _voevent-discover:

Discoverability
===============

VTP makes it :ref:`easy <voevent-receiving>` to subscribe to a broker and get
streams of VOEvents. But... which broker (or brokers) are you going to
subscribe to? What events are they going to send you? How can you learn about
new brokers, or about changes to old ones, or about other capabilities that
brokers offer?

The brief answer is that, at the moment, there's no reliable way to do this.
They best you can hope is that you might find something on the list of brokers
on the `TDIG website`_, but event that is rather hit-or-miss.

A potential solution to this is to invoke the IVOA Registry (:ref:`Demleitner,
2014 <demleitner-2014>`). Work on `an extension`_ to the existing registry
standards to describe VOEvent and its supporting infrastructure is underway.
But even from there, it's a long way to an approachable, user-friendly way of
figuring out which resources area vailable to the end user.

.. _TDIG website: http://www.voevent.org/
.. _an extension: http://www.ivoa.net/documents/VOEventRegExt/20140513/index.html

.. _voevent-scale:

Scalability of VOEvents and VTP
===============================

We :ref:`started <intro>` by discussing the upcoming "deluge" of tens of
millions of events per day. This is far more data intensive regime than the
one we see today, and it's not one that the existing infrastructure has
particularly been designed to address. XML is a flexible but verbose: that
means it's slower than strictly necessary both to transmit and to parse. VTP
is a chatty protocol, which requires multiple network round trips to transmit
a single VOEvent. Expand this to a distributed network where transmitting an
event requires multiple hops, each with multiple round trips, and the latency
will rapidly start climbing.

In fact, :ref:`Swinbank (2014) <swinbank-2014>` shows that it's possible to
sustain transmission of reasonably high event volumes using VTP and Comet
under ideal conditions. Ultimately, though, a root-and-branch rethinking of
the problem may be necessary.

At the same time, other, completely unrelated, projects have addressed
technical problems that are, in some ways, similar to that faced by VOEvent.
For example, consider the `Twitter Streaming API`_ or `Pusher`_. They combine
an approach which is demonstrated to scale to high volume with a more modern
approach to protocol and API design. But... they look plausible in 2015. What
about when all these surveys are going live in 2025? Do we need to anticipate
what direction technology will have taken? Is the event format sufficiently
decoupled from the transport layer that it doesn't matter?

.. _Twitter Streaming API: https://dev.twitter.com/streaming/overview
.. _Pusher: https://pusher.com/

.. _voevent-format:

XML as an event format
======================

VOEvent is designed around representing transient events as XML documents. But
is XML really the most appropriate format? It does provide structure, and a
wide variety of publicly available tools. But it's an old technology,
apparently `well past its peak`_. It's :ref:`difficult to sign
<voevent-auth>`, :ref:`verbose <voevent-scale>` and---crucially!---unpopular
with developers. Should we consider adopting another format?  `JSON`_ is one
possibility, for example. But there's already infrastructure and tooling built
around XML-based VOEvents, and we can more easily integrate with the wide
range of existing IVOA services. Is the pain of switching worth it?

.. _well past its peak: http://www.google.com/trends/explore#q=xml
.. _JSON: http://www.json.org/

High level infrastructure and scientific understanding
======================================================

It's obvious that the tools shown here are building-blocks for a higher level
infrastructure: end users should not have to worry about the details of
network protocols or XML parsing in order to carry out their science. How can
we build upon VOEvent to increase our scientific productivity?

The challenges are various. How do you select the few events which might be
relevant to your science case from the multi-million event deluge? How do you
prevent all the follow-up telescopes from chasing the same target? How do you
cross-reference detections from multiple telescopes at different wavelengths
(or, indeed, using entirely `different types of messengers`_). How do you
present all this with a digestible user interface which is accessible to the
astronomer who just wants to get their science done?

.. _different types of messengers: http://www.ligo.org/

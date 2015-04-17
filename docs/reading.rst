.. _voevent-reading:

=======================
A look inside a VOEvent
=======================

As we've discussed, VOEvents are designed to be readable both by machines and
by humans. Let's start by looking at a real live VOEvent by eye and picking
out the important characteristics. Here's one:

.. literalinclude:: voevent.xml
   :language: xml
   :linenos:

The first thing to realise is that it's *not* important to understand all the
details. There's a lot of "boilerplate" markup language which isn't necessary
to getting a good overview of what's going on here. However, it should be
pretty easy to tease out a few salient points.

Overall structure
=================

The first thing that should be immediately obvious are the blocks referring to
the who, what, where & when, how, and why that we :ref:`previously discussed
<voevent-standard>`.

The ``Who`` block in lines 8 to 14 tells us that this event was generated back
in 1970 by an author who was eagerly anticipating this tutorial session.
``What`` that author observed is described by lines 15 through 26 it was a
radio source, for which we provide peak and integrated flux densities in
Janskys at a frequency between 100 and 200 MHz. ``WhereWhen`` (lines 27--50)
tells us that the observation was made at an observatory on the Earth's
surface, and provides an RA and dec with association uncertainties. ``How``
just provides a free-form text description of how the observation was made,
and ``Why`` identifies this with a GRB taking place in 2012 (nearly 43 years
after this observation was made---not bad!).

.. _voevent-roles:

Event roles
===========

While, in general, we're going to avoid considering the subtleties of the
markup, there are a few features it's worth pointing out. First, right up the
top of the event we have the string:

.. code-block:: xml

   role="test"

This is *important*. It serves as notice to recipients that the event does not
describe an actual astronomical evet. If you are experimenting with VOEvent,
you should *always* use the ``test`` role, even if you don't intend for your
events to be published: if one should leak of your sandbox, it could be
horribly expensive if somebody mistakes it for a notice of a genuine transient
and triggers their multi-hundred-million-dollar followup facility.

That said, there are three other possible roles we should mention.
``observation`` is the obvious one: it's reporting the results of a particular
observation. ``utility`` is informing the recipient about details of the
observing system; for example, a change in configuration. Finally,
``prediction`` indicates that the author is indulding in a little fortune
telling.

IVORNs and identifiers
======================

Next, let's talk about IVORNs, or "IVOA Resource Names". IVORNs provide
unique identifiers for entities known to the virtual observatory, where
"entity" is quite broadly defined. In particular, here we provide an
identifier both for the author of the event:

.. code-block:: xml

   <AuthorIVORN>ivo://hotwired.org</AuthorIVORN>

and for the event itself:

.. code-block:: xml

   ivo://example/exciting_events#123

The latter is particularly worth noting, as the VOEvent standard assigns
specific meanings to each part. In this case, we are considering event number
123 published to the "exciting_events" stream by "example.org". In general, we
expect events to be grouped into streams in this way, where a stream
represents a specific source of events---a given instrument or a particular
way of processing the data, say.

We can refer to other events by specifying their IVORN. For example, this
event specifies:

.. code-block:: xml

   <Citations>
     <EventIVORN cite="followup">ivo://example/exciting_events#1</EventIVORN>
   </Citations>

This means that this VOEvent is reporting a follow-up to a previous
observation, which was described by event 1 in the same stream. As well as
``followup``, it is also possible for the current event to use ``supersedes``
to provide a corrected version of a previous event, or ``retraction`` to
withdraw it altogether. Ultimately, an event aggregation service could group
together VOEvents which cite each other to build up a full description of a
particular astronomical event.

In theory, one can go and look up an IVORN in the IVOA registry to find out
details of the author, the available event streams, and the events themselves.
In practice, that functionality isn't yet generally available (but it's
`coming`_).

.. _coming: http://www.ivoa.net/documents/VOEventRegExt/20140513/index.html

Unified Content Descriptors
===========================

Depending on the source of the event, it might be "obvious" to the human that
"peak flux" measured in Janskys likely refers to some sort of electromagnetic
radiation. However, the computer which might be automatically processing and
making decisions based on this VOEvent needs all the help it can get. To that
end, we can annotate the event with `Unified Content Descriptors`_, or UCDs
(:ref:`Derriere et al, 2005 <derriere-2005>`). For example, we write

.. code-block:: xml

   ucd="em.radio.100-200MHz"

to indicate the type of measurement that ``peak_flux`` and ``int_flux`` refer
to. A `very similar idea`_ applies to the scientific inference:

.. code-block:: xml

   <Concept>process.variation.burst;em.radio</Concept>

.. _Unified Content Descriptors: http://cdsweb.u-strasbg.fr/UCD/
.. _very similar idea: http://cdsweb.u-strasbg.fr/UCD/

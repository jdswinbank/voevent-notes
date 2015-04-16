.. _voevent-reading:

=======================
A look inside a VOEvent
=======================

As we've discussed, VOEvents are designed to be readable both by machines and
by humans. Let's start by looking at a real live VOEvent by eye and picking
out the important characteristics, then show how to do so programmatically.
Here we go:

.. literalinclude:: voevent.xml
   :language: xml
   :linenos:

The first thing to realise is that it's *not* important to understand all the
details. There's a lot of "boilerplate" markup language which isn't necessary
to getting a good overview of what's going on here. However, it should be
pretty easy to tease out a few details.

The first thing that should be immediately obvious are the blocks referring to
the who, what, where & when, how, and why that we :ref:`previously discussed
<voevent-standard>`_.

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

Looking a little closer, there are some subtleties we should consider. First,
the IVORN, or "IVOA Resource Name". This provides a unique identifier for
entities known to the virtual observatory, where "entity" is quite broadly
defined. In particular, here we provide an identifier both for the author:

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

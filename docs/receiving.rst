.. _voevent-receiving:

==============================
Subscribing to VOEvent streams
==============================

We now have a fairly good grasp of what a VOEvent is and what we can do with
it when we've got one. However, we've still not discussed how we can actually
go about getting our hands on some events.

A VOEvent Transport Protocol primer
===================================

At time of writing, the easiest way to go about this is by using the VOEvent
Transport Protocol, or VTP, that we :ref:`mentioned previously <intro>`. Before
proceeding, let's take a few moments to discuss how VTP works. For a more
detailed description, the reader is referred to :ref:`Swinbank (2014)
<swinbank-2014>`.

The VTP system facilitates transmission of events from their *author* to one
or more *subscribers*. Since it's impractical to suggest that a single author
contact each subscriber individually, and it's likely that subscribers want to
receive events from more than one author, we introduce a *broker* as an
intermediary. When a subscriber is interested in receiving events, it connects
to the broker to register its interest, and then it waits. When the author has
an event to distribute, it uploads it to the broker, which then redistributes
it to all connected subscribers.

We can expand this system by simply adding another broker, and causing our
brokers to subscribe to each others outputs. Given a well connected network of
brokers of this form, it doesn't ultimately matter to which broker an author
sends their event, or to which broker a subscriber connects: all events are
ultimately distributed to all entities in the network. This is shown
schematically in the figure below.

.. figure:: network.pdf
   :align: center

   An overview of a VTP network. Arrows indicate the direction of the data
   flow.

Such a network is robust: there is no single point of failure. If a particular
broker fails, the authors and subscribers directly connected to it are cut
off, but the rest of the network is not disrupted. Further, there's no
downside to authors and subscribers connecting to multiple brokers
simultaneously: the protocol is smart enough to eliminate duplicate events, so
everybody only receives one copy of everything.

Of course, given the putative :ref:`transient deluge <intro>`, one copy of
everything is likely far more than the subscriber is interested in (or,
indeed, capable of ingesting). We'll discuss some simple filtering below, but
this is certainly a problem for the :ref:`future <voevent-future>`.

Subscribing to a broker
=======================

We'll start by using `Comet`_ (:ref:`Swinbank, 2014 <swinbank-2014>`) to
subscribe to a feed of events from a broker.  Assuming you've got Comet
installed (see our :ref:`instructions <voevent-setup>`), you can control Comet
using the ``twistd`` command. By default, ``twistd`` assumes you want to run
Comet as a *daemon* (that is, a long running background process, which
communicates with the outside world by log message rather than through your
terminal). That's convenient a lot of the time, but it's something we want to
avoid for the sake of this terminal. We can do so by giving ``twistd`` a
``-n`` flag, then telling it we want to run Comet::

   $ twistd -n comet --help
   Usage: twistd [options]
   Options:
     -r, --receive                   Listen for TCP connections from authors.
     -b, --broadcast                 Re-broadcast VOEvents received.
   [...]

As you can see from the help message, Comet has a lot of options. That's
partly because it can act (simultaneously!) as both broker and subscriber on a
VTP network. The good news is that we can skip over most of them for now: if
you're curious, you can always refer to the Comet documentation for details.

There are just a couple of options we need to get started. The first is to
decide on an identifier for our local system. Just like :ref:`VOEvents
themselves <voevent-reading>`, entities on the network are identified by means
of a URL-like ``ivo://`` identifier. It doesn't matter much what you choose,
as long as you follow the required format. Let's go with
``ivo://hotwired.org/test`` for now.

Secondly, we need a broker to subscribe to. There are several listed at the
`TDIG site`_. For the purposes of this tutorial, we'll use
``voevent.4pisky.org``, hosted by the `4 Pi Sky`_ project.

Given that, we can start Comet running::

   $ twistd -n comet --local-ivo=ivo://hotwired.org/test --remote=voevent.4pisky.org
   2015-04-17 15:38:36-0400 [-] Log opened.
   2015-04-17 15:38:36-0400 [-] twistd 13.2.0 (/opt/local/Library/Frameworks/Python.framework/Versions/2.7/Resources/Python.app/Contents/MacOS/Python 2.7.9) starting up.
   2015-04-17 15:38:36-0400 [-] reactor class: twisted.internet.selectreactor.SelectReactor.
   2015-04-17 15:38:36-0400 [INFO -] Subscribing to remote broker voevent.4pisky.org:8099

You'll see it print some information to the screen saying that it's starting
up and subscribing to the broker... and then it will sit there, waiting to
receive an event. Unfortunately, we can't guarantee when an event will be
received: usually there's something happening every few minutes, but for now
we'll just have to be patient. Then, finally::

   2015-04-17 15:44:51-0400 [INFO VOEventSubscriber,client] VOEvent ivo://nasa.gsfc.gcn/Fermi#GBM_Test_Pos_2015-04-17T19:44:43.00_099999_1-186 received from IPv4Address(TCP, '152.78.192.87', 8099)

Acting on events received
=========================

Of course, just getting a message to tell us that an event was received is
only marginally thrilling. Even better is if we can actually see what that
event contains. Conveniently, you can ask Comet to print a copy of events it
receives using the ``--print-event`` option::

   $ twistd -n comet --local-ivo=ivo://hotwired.org/test --remote=voevent.4pisky.org --print-event
   [...]
   2015-04-17 15:44:51-0400 [INFO VOEventSubscriber,client] VOEvent ivo://nasa.gsfc.gcn/Fermi#GBM_Test_Pos_2015-04-17T19:44:43.00_099999_1-186 received from IPv4Address(TCP, '152.78.192.87', 8099)
   2015-04-17 15:44:51-0400 [-] <voe:VOEvent xmlns:voe="http://www.ivoa.net/xml/VOEvent/v2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ivorn="ivo://nasa.gsfc.gcn/Fermi#GBM_Test_Pos_2015-04-17T19:44:43.00_099999_1-186" role="test" version="2.0" xsi:schemaLocation="http://www.ivoa.net/xml/VOEvent/v2.0 http://www.ivoa.net/xml/VOEvent/VOEvent-v2.0.xsd">
   2015-04-17 15:44:51-0400 [-]   <Who>
   2015-04-17 15:44:51-0400 [-]     <AuthorIVORN>ivo://nasa.gsfc.tan/gcn</AuthorIVORN>
   2015-04-17 15:44:51-0400 [-]     <Author>
   2015-04-17 15:44:51-0400 [-]       <shortName>Fermi (via VO-GCN)</shortName>
   2015-04-17 15:44:51-0400 [-]       <contactName>Julie McEnery</contactName>
   [...]

Those with real ambition might find even this underwhelming, so there exists a
``--save-event`` option which will dump the received events to files (by
default in the current working directory; use the ``--save-event-directory``
option to tweak this).

Realistically, of course, you'll want to be able to take action when you
receive an event. Comet gives you a couple of options here. One is to invoke a
command whenever an event is received, passing it that event on standard
input. You can use this to perform whatever logic you require. For example, we
could make a quick & dirty VOEvent-to-e-mail gateway using a script like this:

.. literalinclude:: mail.sh
   :language: bash

Then simply::

   $ twistd -n comet [...] --cmd=$(pwd)/mail.sh

Note that we have to provide the full path to the command to be executed, and
be aware that this assumes you have an appropriate ``/usr/bin/mail`` set up on
your local system.

This is obviously a pretty trivial example: the `fourpiskytools`_ package
contains something `rather more elaborate`_.

The really ambitious can go further still. Comet makes it possible to add
"plug-in" code which is executed directly within Comet itself to handle events
received. In fact, this is exactly how the ``--print-event`` and
``-save-event`` commands we used above are implemented. For more details, see :ref:`Comet's documentation on Event Handlers
<comet:sec-handlers>`, or refer to the the `source of those commands`_ for
inspiration.

.. _Comet: http://comet.readthedocs.org/
.. _TDIG site: http://www.voevent.org/
.. _4 Pi Sky: http://www.4pisky.org/
.. _fourpiskytools: https://github.com/timstaley/fourpiskytools
.. _rather more elaborate: https://github.com/timstaley/fourpiskytools/blob/master/examples/process_voevent_from_stdin.py
.. _source of those commands: https://github.com/jdswinbank/Comet/blob/master/comet/plugins/eventwriter.py

Filtering event streams
=======================

The VTP standard does not provide for a way to select which events you
receive: brokers distribute all events received to all of their subscribers.
That's OK as long as the volume of events remains relatively low and the
subscribers are all willing to get their hands dirty writing scripts locally
to extract the information they want. Ultimately, though, it's more efficient
for subscribers to be able to select only those events they are interested in
receiving from the broker.

Although this is not possible using vanilla VTP, Comet introduces its own
extension to the protocol which enables the subscriber to ask the broker to
send only events which match certain criteria to the subscriber. For this, we
use the `XPath`_ XML query language. For example, to select only those events
which were issued by `VO-GCN`_ (ie, originate from the NASA `Gamma-ray
Coordinates Network`_), we can use::

   $ twistd -n comet [...] --filter="//Who/Author[shortName='VO-GCN']"

Of course, as usual when processing VOEvents, we need to know something about
the structure of the events we're interested in. If we know, for example, that
the event contains a ``Sun_Distance`` parameter, we can make selections based
on that::

   $ twistd -n comet [...] --filter="//Param[@name='Sun_Distance' and @value>40]"

Quite complex expressions are possible: for more examples, refer to the
:ref:`Comet documentation on filtering <comet:sec-filtering>` and
:ref:`Swinbank (2014) <swinbank-2014>`. Unfortunately, the XPath language is
complex, and, as mentioned, you need to have a good understanding of the event
stream you are trying to filter before you can construct useful queries.
Furthermore, this is a Comet specific extension: it requires that *both* the
subscriber *and* the broker be running Comet. If that's not the case, it won't
do any harm to the network, but the filtering will not be applied.

However, there's a more far-reaching issue than the problems described above:
although XPath is powerful, it doesn't ultimately provide the sort of
filtering and selection capability that will be needed to handle the event
volumes and complexities expected from future surveys. We'll :ref:`return
<voevent-future>` to this point. But first, let's discuss how to send events,
as well as receive them.

.. _XPath: http://www.w3.org/TR/xpath/
.. _VO-GCN: http://gcn.gsfc.nasa.gov/admin/voevent_version20_available.txt
.. _Gamma-ray Coordinates Network: http://gcn.gsfc.nasa.gov/

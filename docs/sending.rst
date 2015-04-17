=====================
Distributing VOEvents
=====================

We're now in a position not to just receive and act upon other VOEvents
created by others, but to actually start giving back to the network by
contributing our own events.

.. warning::

   The tools described in this document make it possible for you broadcast
   events to all interested parties connected to the network. Please be
   considerate of their resources. In particular, tests should be *clearly*
   marked as such using the notation described :ref:`earlier
   <voevent-roles>`.

The good news is that Comet includes a tool that makes submitting your events
to the network really straightforward. You can simply compose your
event---using voevent-parse as :ref:`previously demonstrated <voevent-create>`
if you wish---and save it to a file on disk. Then use the ``comet-sendvo``
script included with Comet to submit it to a broker of your choice::

   $ comet-sendvo -f voevent.xml

If you prefer, you can also feed your event to ``comet-sendvo`` on standard
input::

   $ <voevent.xml comet-sendvo

Easy as pie, right?

Well, sort of. Actually, we've skipped over the hard part, which isn't about
running scripts at all: you need to find a broker which is willing to accept
and redistribute the events you send them to the network. In general, broker
owners (or, at least, the responsible ones) will be conservative about who
they give access to. There are good reasons for this: nobody wants to be
responsible for acting as a gateway for useless events which will congest the
network.

Once you have found a broker which is willing to accept your events, you can
simply tell ``comet-sendvo`` about it with the ``--host`` option (you can also
specify ``--port`` if necessary, but generally it isn't)::

   $ <voevent.xml comet-sendvo -h my.friendly.broker.org

For demo purposes, rather than spewing useless events onto the network, let's
run our own broker. Comet makes this pretty trivial. Open a new terminal and
run::

   $ twistd -n comet -r --local-ivo=ivo://hotwired.org/test

The ``-r`` instructs Comet to listen for submissions from authors. We've not
told it to either act on events it receives or to distribute them to
subscribers, so it won't actually do anything with the events it receives, but
we can, at least, demonstrate that the transmission works.

In your original terminal, go ahead and send the event to the broker running
on ``localhost``::

   $ <voevent.xml comet-sendvo --host=localhost
   2015-04-17 19:26:44-0400 [-] Log opened.
   2015-04-17 19:26:44-0400 [-] Starting factory <__main__.OneShotSender instance at 0x1025743b0>
   2015-04-17 19:26:44-0400 [INFO VOEventSender,client] Acknowledgement received from IPv4Address(TCP, '127.0.0.1', 8098)
   2015-04-17 19:26:44-0400 [VOEventSender,client] Stopping factory <__main__.OneShotSender instance at 0x1025743b0>
   2015-04-17 19:26:44-0400 [INFO VOEventSender,client] Event was sent successfully
   2015-04-17 19:26:44-0400 [-] Main loop terminated.

Meanwhile, at the broker end, you should see something like::

   2015-04-17 19:26:44-0400 [INFO comet.utility.whitelist.WhitelistingFactory] New connection from IPv4Address(TCP, '127.0.0.1', 59635)
   2015-04-17 19:26:44-0400 [INFO VOEventReceiver (ProtocolWrapper),0,127.0.0.1] VOEvent ivo://hotwired.org/gaia_demo#1 received from IPv4Address(TCP, '127.0.0.1', 59635)

Watch out, though: if you try to send the same event again, you'll get a
failure::

   $ <voevent.xml comet-sendvo --host=localhost
   2015-04-17 19:28:07-0400 [WARNING VOEventSender,client] Nak received: IPv4Address(TCP, '127.0.0.1', 8098) refused to accept VOEvent (Event rejected: Previously seen by this broker)
   2015-04-17 19:28:07-0400 [VOEventSender,client] Stopping factory <__main__.OneShotSender instance at 0x10a8923b0>
   2015-04-17 19:28:07-0400 [WARNING VOEventSender,client] Event was NOT sent successfully

What's going on? Well, recall that brokers can subscriber to each other's
outputs. What we don't want is a single event to circulate on the network
indefinitely, being passed back and forth between brokers. For that reason, a
broker will refuse to process an event that it has seen previously.

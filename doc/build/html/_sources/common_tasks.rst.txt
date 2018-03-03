############
Common tasks
############

These are some things that you might need to do often when using decent.py.


Receiving messages
^^^^^^^^^^^^^^^^^^

Create a function named ``on_message`` with a single argument ``message``, and
decorate it with :py:meth:`Server.event`. ::

    @server.event
    def on_message(message):
        print("Received message with text", message.text)

::

    def on_message(message):
        print("Received message with text", message.text)
    server.event(on_message)


Sending messages
^^^^^^^^^^^^^^^^

Just use the :py:meth:`Channel.send` method. ::


    channel = server.channels[0]
    channel.send("I'm alive! 🎉")

::

    @server.event
    def on_message(message):
        message.channel.send("Received a message")

::

    for channel in server.channels:
        channel.send("IMPORTANT ALERT")


Getting recent messages
^^^^^^^^^^^^^^^^^^^^^^^

Use the :py:meth:`Channel.latest_messages` method::

    channel = server.channels[0]
    for message in channel.latest_messages():
        print(message.text)

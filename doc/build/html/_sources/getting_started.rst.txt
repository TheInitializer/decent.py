###############
Getting started
###############


Installation
============

Normally, I would tell you to install something with pip, but since this isn't
quite finished yet, I don't have a PyPI package. It'll happen eventually!

To get started, clone from the GitHub repo::

    git clone https://github.com/TheInitializer/decent.py.git decent

You can put this in site_packages if you want::

    mv decent <python installation dir>/Lib/site_package


Creating your first program with decent.py
==========================================

Start by importing decent::

    import decent

Next, create a :py:class:`Server` object with server details, and connect to it. (The
connection process is done separately, because you may need to reconnect in
the future if you get disconnected!) ::

    my_server = decent.Server("https://my-server.com", "my_username", "my_password")
    my_server.connect()

Now you can create an event callback that triggers when certain events happen
on the server. Here, we're using ``on_message``, which will get called whenever
a message is sent. ::

    @my_server.event
    def on_message(message):
        print("New message from {}: {}".format(message.author, message.text))

Note that decent.py fully supports Python 2 as well as Python 3, in case you
need to use an older version for legacy reasons.

We can also send messages with the :py:meth:`Channel.send` method::

        message.channel.send("Received a message!")

Here, we're getting the channel ``message`` was sent in with
``message.channel``, then sending a new message to that channel with the
``send`` method.

Finally, we need to call the :py:meth:`Server.mainloop` method. This will receive
websocket events from the server and call our callbacks, as well as letting
the server know we're still online and auto-update our class with useful
data. ::

    while True:
        my_server.mainloop()

Here's the finished program::

    import decent

    my_server = decent.Server("https://my-server.com", "my_username", "my_password")
    my_server.connect()


    @my_server.event
    def on_message(message):
        print("New message from {}: {}".format(message.author, message.text))
        message.channel.send("Received a message!")


    while True:
        my_server.mainloop()

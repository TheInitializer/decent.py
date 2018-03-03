#############
API Reference
#############

This is a list of every function decent.py provides, along with all the
information you'll need to use them.

Server
======

.. py:class:: Server(url, username, password)
   
   A connection to a Decent server.

   :param str url: The URL of the Decent server
   :param str username: The username of the user being logged in
   :param str password: The password of the user being logged in

   :var list[Channel] channels: A list of all channels in the server
   :var list[Emote] emotes: A list of all emotes in the server
   :var str pretty_url: The URL without a protocol or trailing slash
   :var str session_id: The ID of the current session
   :var str url: The URL of the Decent server
   :var User user: The currently logged in user
   :var list[User] users: A list of all users on the server


   .. py:method:: connect()

      Connects to the Decent server, logs in with the given username and
      password, and opens a websocket connection.

      :raises DecentError: if the server is invalid or if there is an error
                           logging in


   .. py:method:: create_channel(name)

      Creates a new channel in the server.

      :param str name: The name of the channel
      :returns: the newly created :py:class:`Channel` object


   .. py:method:: get_channel(cid)

      Gets a channel from a channel ID.

      :param str cid: The channel ID
      :returns: a :py:class:`Channel` object with the given ID


   .. py:method:: get_message(mid)

      Gets a message from a message ID.

      :param str mid: The message ID
      :returns: a :py:class:`Message` object with the given ID


   .. py:method:: get_user(uid)

      Gets a user from a user ID.

      :param str uid: The user ID
      :returns: a :py:class:`User` object with the given ID


   .. py:method:: mainloop()

      Receives websocket data from the server and does stuff with it. Call this
      method regularly (usually in a while loop) and it will call your event
      functions decorated with :py:meth:`Server.event`, respond to ping events,
      and update the class when new channels or users are created.

      :raises DecentError: if the Server object has not been properly
                           initialized (via :py:meth:`Server.connect`) before
                           calling mainloop.


   .. py:decoratormethod:: event

      Sets up the decorated function to be called by :py:meth:`Server.mainloop`.

      Possible functions (must have one of these names):

      * ``on_message(message)`` - gets called whenever a new message is sent.
        ``message`` is a :py:class:`Message` object.
      * ``on_edit(message)`` - gets called whenever a message is edited.
        ``message`` is a :py:class:`Message` object.
      * ``on_channel_new(channel)`` - gets called whenever a new channel is
        created. ``channel`` is a :py:class:`Channel` object.
      * ``on_channel_update(channel)`` - gets called whenever a channel is
        updated (renamed, marked as read, etc.) ``channel`` is a
        :py:class:`Channel` object.
      * ``on_channel_delete(channel)`` - gets called whenever a channel is
        deleted. ``channel`` is a :py:class:`Channel` object.
      * ``on_pin_add(message)`` - gets called whenever a message is pinned to
        a channel. ``message`` is a :py:class:`Message` object.
      * ``on_pin_remove(mid)`` - gets called whenever a message is unpinned
        from a channel. ``mid`` is a string.
      * ``on_user_new(user)`` - gets called whenever a new user joins the
        server or is authorized (on a server that requires authorization).
        ``user`` is a :py:class:`User` object.
      * ``on_user_gone(uid)`` - gets called whenever a user leaves the server
        or is deauthorized (on a server that requires authorization).
        ``uid`` is a string.
      * ``on_user_online(user)`` gets called whenever a user who was offline
        before becomes online. ``user`` is a :py:class:`User` object.
      * ``on_user_offline(user)`` gets called whenever a user who was online
        before becomes offline. ``user`` is a :py:class:`User` object.
      * ``on_user_update(user)`` - gets called whenever a user is updated.
        ``user`` is a :py:class:`User` object.
      * ``on_user_mention(message)`` - gets called whenever a user is mentioned
        in a message. ``message`` is a :py:class:`Message` object.
      * ``on_user_unmention(mid)`` - gets called whenever a message mentioning
        a user is edited or deleted to unmention the user. ``mid`` is a string.
      * ``on_emote_new(emote)`` - gets called whenever a new emote is created.
        ``emote`` is a :py:class:`Emote` object.
      * ``on_emote_delete(shortcode)`` - gets called whenever an emote is
        deleted. ``shortcode`` is a string.


Channel
=======

.. py:class:: Channel(server, cid, name[, unread_count])

   A Decent channel. Belongs to a server, and messages can be sent and received
   in channels. You'll rarely if ever use the constructor - Channel objects are
   normally initialized for you automatically.

   :param Server server: The server that the channel belongs to
   :param str cid: The channel ID
   :param str name: The name of the channel
   :param str unread_count: The number of unread messages in the channel

   :var str cid: The channel ID
   :var str name: The name of the channel
   :var str unread_count: The number of unread messages in the channel


   .. py:attribute:: pins

      The pinned messages in the channel.


   .. py:classmethod:: from_json(server, json)

      Constructs a Channel object from a JSON object returned by a Decent
      server.

      :param Server server: The server that the channel belongs to
      :param dict json: The JSON object from the Decent server.


   .. py:method:: delete()

      Deletes the channel.


   .. py:method:: latest_messages([before[, after[, limit]]])

      Gets the latest messages in the channel. If ``before`` is specified, it
      will only return messages sent before the message ID specified in
      ``before``, and if ``after`` is specified, it will only return messages
      sent after the message ID specified in ``after``.

      :param str before: A message ID - only return messages **before** this
                         message
      :param str after: A message ID - only return messages **after** this
                        message
      :param int limit: The maximum number of messages to fetch. Default is 50,
                        must be ``1 <= limit <= 50``.
      :returns: a list of :py:class:`Message` objects.


   .. py:method:: mark_read()

      Marks the channel as read.


   .. py:method:: rename(new_name)

      Renames the channel to ``new_name``.

      :param str new_name: The new name of the channel.


   .. py:method:: send(text)

      Sends a new message in the channel.

      :param str text: The text of the message to send.
      :returns: the newly created :py:class:`Message` object.


   .. py:staticmethod:: by_id(server, cid)

      Gets a channel by its ID in server ``server``.

      :param Server server: The server object to look in
      :param str cid: The channel ID to search for


Message
=======

.. py:class:: Message(server, mid, author, channel, text, date, edit_date, reactions, mentions)

   A message sent in a Decent channel. Again, the constructor will rarely, if
   ever, be used.

   :param Server server: The server that the message belongs to
   :param str mid: The message ID
   :param User author: The author of the message
   :param Channel channel: The channel that the message was sent in
   :param str text: The text of the message
   :param datetime date: The date/time the message was sent
   :param datetime edit_date: The date/time the message was edited
   :param list[Emote] reactions: The reactions attached to the message
   :param list[User] mentions: The users mentioned in the message

   :var Server server: The server that the message belongs to
   :var str mid: The message ID
   :var User author: The author of the message
   :var Channel channel: The channel that the message was sent in
   :var str text: The text of the message
   :var datetime date: The date/time the message was sent
   :var datetime edit_date: The date/time the message was edited
   :var list[Emote] reactions: The reactions attached to the message
   :var list[User] mentions: The users mentioned in the message


   .. py:classmethod:: from_json(server, json)

      Constructs a Message object from a JSON object returned by a Decent
      server.

      :param Server server: The server that the message belongs to
      :param dict json: The JSON object from the Decent server


   .. py:method:: edit(newtext)

      Edits the message and replaces the text with ``newtext``.

      :param str newtext: The new text of the message


User
====

.. py:class:: User(server, uid, username, perm_level, flair, online, avatar_url[, email[, authorized]])

   A user on a Decent server. Again, the constructor is rarely, if ever, used.

   :param Server server: The server that the user belongs to
   :param str uid: The user's ID
   :param str username: The user's username
   :param str perm_level: The permission level (``"admin"`` or ``"member"``)
   :param str flair: The user's flair
   :param bool online: The online/offline status of the user
   :param str avatar_url: The URL of the user's avatar
   :param str email: The user's email
   :param bool authorized: The user's authorization status

   :var Server server: The server that the user belongs to
   :var str uid: The user's ID
   :var str username: The user's username
   :var str perm_level: The permission level (``"admin"`` or ``"member"``)
   :var str flair: The user's flair
   :var bool online: The online/offline status of the user
   :var str avatar_url: The URL of the user's avatar
   :var str email: The user's email
   :var bool authorized: The user's authorization status


   .. py:classmethod:: from_json(server, json)

      Creates a User object from a JSON object returned by a Decent server.

      :param Server server: The server that the user belongs to
      :param dict json: The JSON object from the Decent server

   
   .. py:method:: authorize()

      Authorizes the user.

   
   .. py:method:: deauthorize()

      Deauthorizes the user.


   .. py:staticmethod:: by_id(server, uid)

      Returns a user from server ``server`` with ID ``uid``.

      :param Server server: The server to search in
      :param str uid: The user ID to search for

      :returns: a :py:class:`User` object

      :raises DecentError: if a user with the given ID is not found in the
                           server.


Emote
=====

.. py:class:: Emote(server, shortcode, image_url)

   An emote on a Decent server.

   :param Server server: The server that the emote belongs to
   :param str shortcode: The ``:shortcode:`` of the emote
   :param str image_url: The URL of the emote's image

   :var Server server: The server that the emote belongs to
   :var str shortcode: The ``:shortcode:`` of the emote
   :var str image_url: The URL of the emote's image


   .. py:classmethod:: from_json(server, json)

      Creates an Emote object from a JSON object returned by a Decent server.

      :param Server server: The server that the emote belongs to
      :param dict json: The JSON object from the Decent server


   .. py:staticmethod:: by_shortcode(server, shortcode)

      Returns an emote from server ``server`` with shortcode ``shortcode``.

      :param Server server: The server to search in
      :param str shortcode: The shortcode to search for

      :returns: a :py:class:`Emote` object

      :raises DecentError: if an emote with the given shortcode is not found 
                           in the server.

======
Bleach
======

Bleach is an HTML sanitizing library that escapes or strips markup and
attributes based on a white list. Bleach can also linkify text safely, applying
filters that Django's ``urlize`` filter cannot, and optionally setting ``rel``
attributes, even on links already in the text.

Bleach is intended for sanitizing text from *untrusted* sources. If you find
yourself jumping through hoops to allow your site administrators to do lots of
things, you're probably outside the use cases. Either trust those users, or
don't.

Because it relies on html5lib_, Bleach is as good as modern browsers at dealing
with weird, quirky HTML fragments. And *any* of Bleach's methods will fix
unbalanced or mis-nested tags.

The version on `github <http://github.com/jsocol/bleach>`_ is the most
up-to-date and contains the latest bug fixes.


Basic Use
=========

The simplest way to use Bleach is::

    >>> import bleach

    >>> bleach.clean('an <script>evil()</script> example')
    u'an &lt;script&gt;evil()&lt;/script&gt; example'

    >>> bleach.linkify('an http://example.com url')
    u'an <a href="http://example.com" rel="nofollow">http://example.com</a> url

*NB*: Bleach always returns a ``unicode`` object, whether you give it a
bytestring or a ``unicode`` object, but Bleach does not attempt to detect
incoming character encodings, and will assume UTF-8. If you are using a
different character encoding, you should convert from a bytestring to
``unicode`` before passing the text to Bleach.


Customizing Bleach
==================

``clean()`` and ``linkify()`` can take several optional keyword arguments to
customize their behavior.


``clean()``
-----------

``bleach.clean()`` is the primary tool in Bleach. It uses html5lib_ to parse a
document fragment into a tree and does the sanitization during tokenizing,
which is incredibly powerful and has several advantages over regular
expression-based sanitization.

``tags``
  A whitelist of HTML tags. Must be a list. Defaults to
  ``bleach.ALLOWED_TAGS``.
``attributes``
  A whitelist of HTML attributes. Either a list, in which case all attributes
  are allowed on all elements, or a dict, with tag names as keys and lists of
  allowed attributes as values ('*' is a wildcard key to allow an attribute on
  any tag). Or it is possible to pass a callable instead of a list that accepts
  name and value of attribute and returns True of False. Defaults to
  ``bleach.ALLOWED_ATTRIBUTES``.
``styles``
  A whitelist of allowed CSS properties within a ``style`` attribute. (Note
  that ``style`` attributes are not allowed by default.) Must be a list.
  Defaults to ``[]``.
``strip``
  Strip disallowed HTML instead of escaping it. A boolean. Defaults to
  ``False``.
``strip_script_content``
  Strip content between ``<script>`` tags. Defaults to ``False``.
``strip_comments``
  Strip HTML comments. A boolean. Defaults to ``True``.


``linkify()``
-------------

``bleach.linkify()`` turns things that look like URLs or (optionally) email
addresses and turns them into links. It does this smartly, only looking in text
nodes, and never within ``<a>`` tags.

There are options that affect output, and some of these are also applied to
links already found in the text. These are designed to allow you to set
attributes like ``rel="nofollow"`` or ``target``, or push outgoing links
through a redirection URL, and do this to links already in the text, as well.

``nofollow``
  Add ``rel="nofollow"`` to non-relative links (both created by ``linkify()``
  and those already present in the text). Defaults to ``True``.
``filter_url``
  A callable through which the ``href`` attribute of links (both created by
  ``linkify()`` and already present in the text) will be passed. Must accept a
  single argument and return a string.
``filter_text``
  A callable through which the text of links (only those created by
  ``linkify``) will be passed. Must accept a single argument and return a
  string.
``skip_pre``
  Do not create new links inside ``<pre>`` sections. Still follows
  ``nofollow``. Defaults to ``False``.
``parse_email``
  Linkify email addresses with ``mailto:``. Defaults to ``False``.
``target``
  Set a ``target`` attribute on links. Like ``nofollow``, if ``target`` is not
  ``None``, will set the attribute on links already in the text, as well.
  Defaults to ``None``.


.. _html5lib: http://code.google.com/p/html5lib/

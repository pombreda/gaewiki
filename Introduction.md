**GAEWiki** is a simple wiki engine which written in Python runs in [Google App Engine](http://code.google.com/appengine/).  It does not have as many features as [some other wikis](http://en.wikipedia.org/wiki/Comparison_of_wiki_software) have, but it's sufficient to run a personal or a small community web site.  But it is very easy to set up (requires no server).

The wiki is customizable with user style sheets and scripts, has some access control, labels to organize pages in categories, history of edits, RSS feeds and other features, described below.  Customization does not require any code changes; all settings are stored in special wiki pages.

If you have any questions or problems, use the [issue tracker](http://code.google.com/p/gaewiki/issues/list) or email `hex@umonkey.net`.



# Getting started #

To have your own GAEWiki you need to [create a new application](https://appengine.google.com/start/createapp), [fetch the source code](http://code.google.com/p/gaewiki/source/checkout) and [upload it](http://code.google.com/appengine/docs/python/gettingstarted/uploading.html) to your new application.  That's it, you can start using your new wiki.  You can [use a custom domain name](http://code.google.com/appengine/articles/domains.html) to access your application, if you want.

If you don't want to install the application, you can use [the demo installation](http://gaewiki-demo.appspot.com/).  It has the latest code and [a separate database for every domain name](http://code.google.com/appengine/docs/python/multitenancy/).  Find GAEWiki in Google Apps.


## Creating and editing pages ##

The default GAEWiki theme displays tabs in the top of the page.  Use the "Edit" tab to edit the page you're viewing.  You will be prompted to log in if the wiki requires so.  If you have a [Universal Edit Button](http://www.universaleditbutton.org/) extension in your browser, you can use it.

The best way to create a page is to edit an existing one, add a link, then follow that link.  This prevents creation of orphan pages which are hard to find, thus useless.  However, you can just type whatever you want in the URL bar, you'll be prompted to create a new page.

To delete a page, start editing it, then check the checkbox below the text field.


## Text formatting syntax ##

GAEWiki uses the Markdown syntax, which means good readability even when the page is unprocessed.  You format your text as you would in a plain text email message.  If you need something more complex, you can use HTML.

To understand Markdown in details, refer to the [official documentation](http://daringfireball.net/projects/markdown/syntax).  Here's an example that will cover most features:

```
Page title
==========

Long lines can be wrapped.
Multiple lines are combined to one paragraph.

An empty line separates different paragraphs.
You can write *bold* and _italic_ text easily.

List formatting (this is a subheader btw)
-----------------------------------------

Here is a simple bullet list:

- Hello, world.
- Goodbye, world.

Here is a numbered list:

1. Hello, world.
2. Goodbye, world.


Links can be [[other page|internal]] or [external](http://example.com/).
Long links can be added [this way][1], to prevent text pollution.

And here's how you embed an image:

![image title](http://example.com/test.jpg)

[1]: http://www.example.com/
```

This is rather simple, but for your convenience GAEWiki has a built-in WYSIWYG editor.


## Special page properties ##

In addition to the text, pages can have special headers (properties) which are added this way:

```
key1: value
key2: value
---
Your regular page contents goes here...
```

The following properties are supported:

  * `public`: when set to "yes", everybody can see this page even if the wiki is not public (see below).
  * `private`: when set to "yes", the page can only be accessed by admins.
  * `locked`: when set to "yes", only admins can edit this page.
  * `labels`: a comma-separated list of labels (case sensitive).  Use labels to organize pages in categories.
  * `redirect`: the name of the page that this one should redirect to.  This is added automatically when pages are renamed, but you can use it manually.  Redirects are performed internally, without actually sending the user to a new location; if a page redirects more than 10 times, an error message is shown.

You can comment out properties by starting a line with the pound sign, e.g.:

```
#key1: value
key1: a different value
key2: value
```


## Organizing pages using labels ##

Labels are usually used to organize pages in categories.  They are edited this way:

```
labels: People, Writers
---
# Isaac Asimov

*Isaac Asimov* was ...
```

After you save the page, in the bottom of it there'll be a block with links: "Labels: People, Writers."  Links will follow to pages "Label:People" and "Label:Writers", where you can describe the purpose of the label.  You can also list all pages that have a label using this syntax:

```
Pages about writers:

[[List:Writers]]

Know more?  Create a new page!
```

If you don't like the "Label:Writers" page name, you can redirect somewhere else or just change the display name (see above).

There is an RSS feed for each label, which can be accessed at `/w/pages.rss?label=xyz`.


## Organizing pages in a hierarchy ##

Another way to organize pages is to use slashes in their names.  Most people understand the concept of folders, and pages with slashes in their names look like folders.

GAEWiki lets you list immediate children (think of subfolders) using this syntax:

```
Time zones for Europe:

[[ListChildren:Europe]]
```

This would produce a linked list of pages which names start with "Europe/", e.g.:

  * Europe/TZ1
  * Europe/TZ2

If you want to list subpages of the current page, you can use a shorter version:

```
[[ListChildren:]]
```

Remember that even though this looks like folders, this is only a visual trick.  Normally, if you delete what looks like a parent page, subpages are not deleted; a page "Europe/TZ1" can exist even when there's no page named "Europe".

To forbid adding subpages to pages that don't exist, set the "parent-must-exist" setting to "yes" (this does not apply to admins).  This option only controls page creation, it doesn't prevent deletion of pages that have parents.


## Privacy ##

Users that don't want to show their email address to others can set a custom nickname and public email address to whatever they want.  This can be done at the `/w/profile` page (the first link in the top right page corner after you log in):

![http://wiki.gaewiki.googlecode.com/hg/images/profile.png](http://wiki.gaewiki.googlecode.com/hg/images/profile.png)


# Advanced features #

## Podcasting ##

You can add files to pages and describe them using properties `file`, `file_type` (will be guessed if not set) and `file_length` (not used if not set).  These properties will be used to add enclosures to RSS feeds.  Example:

```
file: http://example.com/ep42.mp3
file_type: audio/mpeg
file_size: 1024
display_title: Episode 42
---
# podcast/42

...
```


## Redirecting pages ##

You can redirect one page to another using the `redirect` property, e.g.:

```
redirect: page2
---
# page1

This page will only be shown if page2 does not exist.
```

In this situation the user will see page2 when page1 was accessed.  If page1 is a label page, then page2 will be linked to in the lists.  Example:

```
redirect: List of examples
---
# Label:example
```


## Embedded MP3 player ##

To embed an MP3 player use the following syntax:

```
file: http://example.com/test.mp3
---
# Test page

[[gaewiki:mp3player]]
```

This page will have a player which will play the file specified in the "file" property.  To play a different file the following syntax could be used:

```
[[gaewiki:mp3player;url=http://example.com/alternative.mp3]]
```


# Advanced configuration #

In this section "settings" are often mentioned.  Settings are properties of the page named "gaewiki:settings".  To change settings you need to open that page for editing and add the values to the page header, e.g.:

```
favicon: http://example.com/favicon.ico
---
# Edit me.
```


## Interwikis ##

The concept of "interwikis" simplifies linking to sites that users are linking to frequently, if the target URLs are of a known form (long URLs that only differ in one parameter).

Interwikis are configured by adding "`interwiki-*`" headers to the `gaewiki:settings` page, e.g.:

```
interwiki-g: http://www.google.ru/search?ie=UTF-8&q=%s
interwiki-wp: http://en.wikipedia.org/wiki/Special:Search?search=%s
interwiki-issue: http://code.google.com/p/gaewiki/issues/detail?id=%s
```

You can then use this syntax to link:

```
- [[g:robots|Search for "robots" with Google]]
- [[wp:robots|See what Wikipedia has to say about robots]]
- [[issue:36|Issue 36 in the GAEWiki issue tracker]]
```


## Page templates ##

When a user opens a new page for editing, some built-in text is offered for editing.  This text can be changed to better suit your needs.  This text can also be different for admins, authenticated users and anonymous ones.  To use templates, create pages with these names:

  * `gaewiki:admin page template`
  * `gaewiki:user page template`
  * `gaewiki:anon page template`

The "anon" template applies to all users, "user" applies to registered users and admins, "admin" applies to admins only.

Templates support two special keywords:

  * PAGE\_TITLE, which is replaced with the title of the created page,
  * USER\_EMAIL, which is replaced with the email address of the logged in user (works well with the "editors:" page property to make the page editable by its author only).

To make pages created by admins only editable by them, you would create a page named "gaewiki:admin page template" with the following contents:

```
locked: yes
---
# gaewiki:admin page template

This page is locked by default.
```

To make pages created by registered users only editable by themselves, you would create a page named "gaewiki:user page template" with the following contents:

```
editors: USER_EMAIL
---
# gaewiki:user page template

This page belongs to USER_EMAIL, only (s)he can edit it.
```


## Restricting page names ##

If you need to let users edit only certain pages, or restrict pages by name, you can use page name black- and white-listing.  For example, if you want to only let users edit pages which are named like "Usa/Fl/Orlando/Something", but "Something" must not be "Foobar", you need to add these properties to the "gaewiki:settings" page:

```
page-whitelist: ^Usa/Fl/Orlando/[^/]+$
page-blacklist: /Foobar$
```

Note:

  * White-listing has a higher priority.
  * Black-listed pages can only be edited by wiki admins, not even by editors.


## Customizing the visual look ##

If you don't like the built-in style of GAEWiki, you can change it by adding the following settings:

  * `extra_styles`: a comma-separated list of URLs to additional CSS style sheets.
  * `extra_scripts`: a comma-separated list of URLs to additional JavaScript files.
  * `extra_init_script`: some inline JavaScript code (to define a variable or something).
  * `favicon`: the URL of your site icon.
  * `footer`: the name of the page whose contents is displayed at the bottom of your wiki.  Commonly used for copyright information and so on.  Defaults to "gaewiki:footer".
  * `labels_text`: custom heading for the list of labels in the bottom of the page.  Defaults to "Labels".  HTML can be used.
  * `sidebar`: the name of the page whose contents is displayed in the left part of your wiki.  Commonly used for navigation.  Defaults to "gaewiki:sidebar".
  * `start_page`: the name of the start page, defaults to "Welcome".
  * `title`: the name of your web site, used in page titles.


## Custom error pages ##

You can have custom pages for errors 400, 403, 404 and 500 by creating pages named "gaewiki:error-400" etc.  To have pretty page titles, use the `display_title` property, e.g.:

```
display_title: Forbidden
---
# gaewiki:error-403

You have no access to this page, try logging in.
```


## Integration with Google ##

There are two special settings to help integrate your web site with Google:

  * `owner_meta`: the value of the google-site-verification meta tag (see [Google Webmaster Tools](https://www.google.com/webmasters/tools/home)).
  * `gaid`: your [Google Analytics](http://analytics.google.com/) profile identifier (e.g., "UA-123456-7").


## Using Markdown extensions ##

You can enhance the Markdown syntax by enabling additional extensions.  List them in the "markdown-extensions" settings.  Available options are: abbr, codehilite, def\_list, extra, fenced\_code, footnotes, headerid, html\_tidy, imagelinks, meta, rss, tables, toc, wikilinks (see [the official documentation](http://www.freewisdom.org/projects/python-markdown/Available_Extensions) for detailed description).

If you intend to use the fenced\_code extension, you might also want some syntax highlighting.  The easiest way would be to use the [CDN version](http://softwaremaniacs.org/soft/highlight/en/download/) of the [Highlight.js](http://softwaremaniacs.org/soft/highlight/en/).  It can be enabled by the following settings:

```
markdown-extensions: fenced_code
extra_styles: http://yandex.st/highlightjs/5.14/styles/default.min.css
extra_scripts: http://yandex.st/highlightjs/5.14/highlight.min.js
extra_init_script: hljs.initHighlightingOnLoad();
```


## Access control ##

By default everybody can read all pages, and most pages can be edited by everybody after they log in.  The following settings can be used to change this situation:

  * `open-reading`: set to "login" to restrict reading to logged-in users; set to "no" to restrict reading to admins, editors and privileged readers (see below).
  * `open-editing`: set to "login" to restrict editing to logged-in users; set to "no" to restrict editing to admins and editors.
  * `editors`: a comma-separated list of email addresses of people who can edit all pages.
  * `readers`: a comma-separated list of email addresses of people who can read all pages.

Depending on the site-wide access settings, you can use these properties on individual pages:

  * `public`: set to "yes" to allow anonymous reading even when the whole wiki is restricted (typically this is done to the welcome page).
  * `private`: set to "yes" to make the page available to privileged users even when the whole wiki is public.
  * `locked`: set to "yes" to make the page only editable by admins and editors.


## Page comments ##

To use an external comment system, such as [Disqus](http://disqus.com/) or [IntenseDebate](http://intensedebate.com/), add its code to the "comments\_code" setting (make sure it's one line), then add the "comments: yes" property to pages that you want your visitors to be able to comment.

Example `gaewiki:settings`:

```
title: GAEWiki Sandbox
comments_code: <div id="disqus_thread"></div>(function() { var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true; dsq.src = 'http://example.disqus.com/embed.js'; document.getElementsByTagName('head')[0].appendChild(dsq); })();</script>
```

Example `Some_page`:

```
comments: yes
---
# Some page
```


# Other tasks #

## Backing up ##


To download current versions of all pages as one big JSON file, go to the `/w/data/export` page.  To upload that file back, go to the `/w/data/import` page.  You can import only new pages, i.e. the ones that don't exist already.

These pages are only accessible by admins.  Useful for merging wikis, moving data between SDK and the cloud, when domain names change and your wikis look empty because of [multitenancy](http://code.google.com/appengine/docs/python/multitenancy/).


# Internals #

## How caching works ##

Conversion from Markdown to HTML takes CPU cycles.  Caching saves these cycles.  Pages are cached individually, cache is updated when you edit the page.  In case anything goes wrong, admins can add `?nocache` to page address to force cache update.


## Special pages ##

If you plan to interact with the wiki in non-standard ways, here's the list of all special pages:

| **URI** | **Description** |
|:--------|:----------------|
| `/w/backlinks?page=name` | Shows a list of pages that link to the specified one. |
| `/w/changes` | Shows a list of recently changed pages.  Most recent changes are at the top. |
| `/w/changes.rss` | A feed for recently changed pages. |
| `/w/edit?page=name` | Shows a page edit form. |
| `/w/data/export` | Used to download current versions of all articles in JSON for backup purposes. |
| `/w/data/import` | Adds back what was exported with `/w/data/export`. |
| `/w/history?page=name` | Shows revisions of a page. |
| `/w/index.rss` | A feed for all pages. |
| `/w/users` | Shows a list of users who were editing the wiki. |
| `/robots.txt` | A special page for [robots](http://www.robotstxt.org/). |
| `/sitemap.xml` | A standard [site map](http://www.sitemaps.org/).  Only works when the wiki is public. |


# See also #

If you didn't like GAEWiki, here's some links:

  * [gddwiki](http://code.google.com/p/gddwiki/).  GAEWiki was forked off it, but all code was rewritten with time.
  * [Search Google Code for more projects](http://code.google.com/hosting/search?q=label:gae,appengine+label:wiki+label:python&projectsearch=Search+projects)
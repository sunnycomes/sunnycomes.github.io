---
layout: post
title: "Markdown Syntax Guide"
date: 2013-11-30 21:35:48 +0800
comments: true
categories: 
---
This post is from [here](http://sourceforge.net/p/forge/documentation/markdown_syntax/#md_ex_links "thanks to the origin author."). Thanks to the origin author.
Markdown was created to be easy to read, easy to write, and still readable in plain text format.

1. [Links](#links)
1. [Reference Links](#reference_links)
1. [Artifact Links](#artifact_links)
1. [Basic Text Formatting](#basic_text_formatting)
1. [Blockquotes](#blockquotes)
1. [Preformatted Text](#preformatted_text)
1. [Lists](#lists)
1. [Tables](#tables)(We will talk about this feature)
1. Headers(Removed)
1. [More Headers](#more_headers)
1. [Horizontal Rules](#horizontal_rules)
1. [Images](#images)
1. Videos(Removed)
1. [Escapes and HTML](#escapes_and_html)
1. Table of Contents(Removed)
1. [Code Highlighting](#code_highlighting)
1. Includes(Removed)
1. Neighborhood Notifications(Removed)
1. Project Info Macros(Removed)

-----------------------

##<a name='links'></a>Links

Most URLs will automatically be turned into links. To be explicit, just write it like this:

	<http://someurl>  
	<somebbob@example.com>

Output:


<!-- more -->

><http://someurl>  
<somebbob@example.com>

To use text for the link, write it:

	[like this](http://someurl)

You can add a *title* (which shows up under the cursor):

	[like this](http://someurl "this title shows up when you hover")

Output:

>[like this](http://someurl)

>You can add a *title* (which shows up under the cursor):

>[like that](http://someurl "this title shows up when you hover")

-----


##<a name='reference_links'></a>Reference Links

	You can also put the [link URL][1] below the current paragraph like [this][2].  
	[1]: http://url  
	[2]: http://another.url "A funky title"  

Output:
   [1]: http://url
   [2]: http://another.url "A funky title"

>You can also put the [link URL][1] below the current paragraph [like][2] this.  

Here the text "link URL" gets linked to "http://url", and the lines showing "[1]: http://url" won't show anything.

	[shortcut]: http://goes/with/the/link/name/text

Or you can use a [shortcut][] reference, which links the text "shortcut" to the link named "[shortcut]" on the next paragraph.

Output:

[shortcut]: http://goes/with/the/link/name/text
>Or you can use a [shortcut][] reference, which links the text "shortcut" to the link named "[shortcut]" on the next paragraph.

------------------


##<a name='artifact_links'></a>Artifact Links

Any forge resource (artifact) can be linked with surrounding square brackets, e.g.

	[MyPage] 
	or 
	[#123]. 

These artifact links can take several forms.

------------------

##<a name='simple_links'></a>Simple Links

Most commonly, the artifact identifier can simply be surrounded with square brackets. Here are some examples:

	[MyWikiPage]       # Wiki - name of wiki page  
	[#123]             # Tracker - ticket number  
	[r10721]           # SVN - revision number  
	[3b9d48]           # Git & Mercurial - first 6 characters of revision hash  
	[2012/02/my-post]  # Blog - post slug, including YYYY/MM/ prefix  
	[a6d38f98]         # Discussion Thread - thread id  
	[a6d38f98#42f8]    # Discussion Post - thread_id#post_id  

###Two-part Links###

To link to an artifact in a specific tool, use the form: `[tool:artifact]`, where `tool` is the name of the tool as it appears in the URL. Two-part links are useful when you have two tools of the same type installed. For example, let's say you have a 'bugs' tracker and a 'features' tracker installed, and you want to link to the first ticket in each:

	[bugs:#1]  
	[features:#1]

###Three-part Links###

To link to an artifact in another project, use the form: `[project:tool:artifact]`, where `project` is the name of the project as it appears in the URL. For example:

	[allura:wiki:Home]

To link to an artifact in a subproject, use the form: `[project/subproject:tool:artifact]`, where `subproject` is the name of the subproject as it appears in the URL. For example:

	[allura/sub:code:3b9d48]

---------------------

##<a name='basic_text_formatting'></a>Basic Text Formatting

Use * or _ to emphasize things:

	*this is in italic*  and _so is this_  
	**this is in bold**  and __so is this__  
	***this is bold and italic***  and ___so is this___

Output:

>*this is in italic*  and _so is this_  
**this is in bold**  and __so is this__  
***this is bold and italic***  and ___so is this___  

You can strike through text using HTML like this:

	<s>this is strike through text</s>

Output:

><s>this is strike through text</s>

Two blank spaces at the end of the line do the same work as <br/> in _html_, making a line break. *(A little bit different from the origin post)*.  
Two carriage returns make a new paragraph.

--------------------

##<a name='blockquotes'></a>Blockquotes

Use the > character in front of a line, just like in email

	> Use it if you're quoting a person, a song or whatever.

	> You can use *italic* or lists inside them also.
	And just like with other paragraphs,
	all of these lines are still
	part of the blockquote, even without the > character in front.

To end the blockquote, just put a blank line before the following
paragraph.

Output:

>Use it if you're quoting a person, a song or whatever.

>You can use italic or lists inside them also.
And just like with other paragraphs,
all of these lines are still
part of the blockquote, even without the > character in front.

To end the blockquote, just put a blank line before the following paragraph.

----------------

##<a name='preformatted_text'></a>Preformatted Text

If you want some text to show up exactly as you write it, without Markdown doing anything to it, just indent every line by at least 4 spaces (or 1 tab). As an alternative to indenting, you can use 4 or more tildes before and after the text. See examples in the Code Highlighting section(_xxxxx_ at the head of line is a placeholder for one tab):

	This line won't *have any markdown* formatting applied.  
	I can even write <b>HTML</b> and it will show up as text.  
	This is great for showing program source code, or HTML or even  
	Markdown. <b>this won't show up as HTML</b> but  
	exactly <i>as you see it in this text file</i>.  

Within a paragraph, you can use backquotes to do the same thing.`This won't be *italic* or **bold** at all.`

Output:

>This line won't *have any markdown* formatting applied.  
I can even write <b>HTML</b> and it will show up as text.  
This is great for showing program source code, or HTML or even  
Markdown. <b>this won't show up as HTML</b> but  
exactly <i>as you see it in this text file</i>.

Within a paragraph, you can use backquotes to do the same thing.`This won't be *italic* or **bold** at all.`

##<a name='lists'></a>Lists  
(Here is some place different from the origin counterpart, accutally not powerful as it described, and pay attention that the next line must be blank, even two blank spaces(break line) don't work.)

    * an asterisk starts an unordered list
    * and this is another item in the list
    + or you can also use the + character
    - or the - character

To start an ordered list, write this:

	1.this starts a list *with* numbers  
	1. this will show as number "2."  
	1. this will show as number "3."  

Output:

>* an asterisk starts an unordered list
* and this is another item in the list
+ or you can also use the + character
- or the - character

To start an ordered list, write this:

>1. this starts a list *with* numbers
2. this will show as number "2."
3. this will show as number "3."

###Multi-level List###  
(Newly added, 2013.12.26 21:15)

	* First Level
	 * Second Level, add one more space than first level
	      * Third Level, add six more spaces than first level
Output:

>* First Level
 * Second Level, add one more space than first level
      * Third Level, add six more spaces than first level

---------

##<a name='tables'>Tables  
(We will talk about this feature later)

-----------------

##<a name='more_headers'>More Headers

More ways of doing headers:

	# this is a huge header #  
	## this is a smaller header ##  
	### this is even smaller ###  
	#### more small ####  
	##### even smaller #####  
	###### smallest still: `<h6>` header  

Output:
># this is a huge header  
>## this is a smaller header  
>### this is even smaller  
>#### more small  
>##### even smaller  
>###### smallest still: `<h6>` header  

You can use up to 6 \# characters at the beginning of the line.

-----------

##<a name='horizontal_rule'></a>Horizontal Rule

Just put three or more "*" or "-" on a line:

	----------------

Output:

>----------------

Or, you can use single spaces between them, like this:

	* * *

Output:

--------------------  
or  
* * * * * *

Make sure you have a blank line above the dashes, though, or else:  
you will get a header
-------

----------

##<a name='images'></a>Images

To include an image, just put a "!" in front of a text link:

	![alternate text](https://sourceforge.net/images/icon_linux.gif)

You can also use a title if you want, like this:

	![tiny arrow](https://sourceforge.net/images/icon_linux.gif "tiny arrow")

Output:

>![alternate text](https://sourceforge.net/images/icon_linux.gif)
The "alternate text" will show up if the browser can't load the image.

You can also use a title if you want, like this:

>![tiny arrow](https://sourceforge.net/images/icon_linux.gif "tiny arrow")

Some other features like macro is not covered here since not support in my lib.

What if you want to just show asterisks, not italics?

	* this shows up in italics: *a happy day*  
	* this shows the asterisks: *a happy day*

Output:

>* this shows up in italics: *a happy day*  
* this shows the asterisks: *a happy day*

What is you want to show the angle brackets `"<xxx>"`, not for special purposes? This example is newly added.

	<b>place `` on the left of the close bracket.</b>  
	<b>place back quotes between the brackets</b>

Output:

><b>xxxx</b>  
<b>place back quotes between the brackets</b>

The backslashes will disappear and leave the asterisks. You can do the same with any of the characters that have a special meaning
for Markdown.

Many simple HTML tags are allowed, for example `<b>` And unknown tags will be dropped. To show a literal <b> or an unknown tag like <foobar> you need escape it with HTML entities: :

	<b>this will be bold</b>  
	you should escape <unknown> tags  

Output:

><b>this will be bold</b>  
you should escape <unknown> tags  

HTML tags that are block-level like <div> can be used, but if there is markdown formatting within it, you must add a "markdown" attribute: `<div markdown>` Some safe attributes are also allowed, permitting basic styling and layout: <div markdown style="float:left">

Individual ampersands (&) and less-than signs (<) are fine, they will be shown as expected.

-----------

##<a name='table_of_contents'></a>Table of Contents

You can display a list of links to jump to headers in a document. Sub-headers will be nested.

[TOC]

	# Section 1  
	## Sub-section 1  
	# Section 2  

Output:

># Section 1  
>## Sub-section 1  
># Section 2  

--------------

##<a name='code_highlighting'></a>Code Highlighting

The code highlighting syntax uses CodeHilite and is colored with Pygments. It follows the same syntax as regular Markdown code blocks, except that there are two ways to tell the highlighter what language to use for the code block.

If the first line of the codeblock contains a shebang, the language is derived from that and line numbers are used(actually not).

	#!/usr/bin/python  
	# Code goes here ...

Output:

>#!/usr/bin/python  
# Code goes here ...

If the first line contains a shebang, but the shebang line does not contain a path (a single / or even a space) or If the first line begins with three or more colons, the text following the colons identifies the language. In both cases, the first line is removed from the code block before processing.

	:::python  
	# Code goes here ...

Output:

>:::python  
# Code goes here ...
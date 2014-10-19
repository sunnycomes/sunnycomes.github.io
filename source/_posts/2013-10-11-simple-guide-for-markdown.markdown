---
layout: post
title: "Simple Guide for Markdown"
date: 2013-10-11 21:35:48 +0800
comments: true
---

Markdown was created to be easy to read, easy to write, and still readable in plain text format.

1. [Links](#links)
1. [Reference Links](#reference_links)
1. [Artifact Links](#artifact_links)
1. [Basic Text Formatting](#basic_text_formatting)
1. [Blockquotes](#blockquotes)
1. [Preformatted Text](#preformatted_text)
1. [Lists](#lists)
1. [Tables](#tables)
1. [More Headers](#more_headers)
1. [Horizontal Rules](#horizontal_rules)
1. [Images](#images)
1. [Escapes and HTML](#escapes_and_html)
1. [Table of Contents](#table_of_contents)

<!-- more -->

-----------------------

##<a name='links'></a>Links

Most URLs will automatically be turned into links. To be explicit, just write it like this:

	<http://www.iovi.com>  
	<somebbob@iovi.com>

Output:

><http://www.iovi.com>  
<somebbob@iovi.com>

To use text for the link, write it:

	[like this](http://www.iovi.com)

You can add a *title* (which shows up under the cursor):

	[like this](http://www.iovi.com "this title shows up when you hover")

Output:

>[like this](http://www.iovi.com)

>You can add a *title* (which shows up under the cursor):

>[like that](http://www.iovi.com "this title shows up when you hover")

-----


##<a name='reference_links'></a>Reference Links

	You can also put the [link URL][1] below the current paragraph like [this][2].  
	[1]: http://www.iovi.com  
	[2]: http://www.iovi.com "I/O VIsion"  

Output:
   [1]: http://www.iovi.com
   [2]: http://www.iovi.com "I/O VIsion" 

>You can also put the [link URL][1] below the current paragraph [like][2] this.  

Here the text "link URL" gets linked to `http://www.iovi.com`, and the lines showing `[1]: http://www.iovi.com` won't show anything.

	Or you can use a [shortcut][] reference, which links the text "shortcut" to the link named "[shortcut]" on the next paragraph.
	[shortcut]: http://www.iovi.com/goes/with/the/link/name/text

Output:

[shortcut]: http://www.iovi.com/goes/with/the/link/name/text
>Or you can use a [shortcut][] reference, which links the text "shortcut" to the link named "[shortcut]" on the next paragraph.

------------------


##<a name='artifact_links'></a>Artifact Links

Any forge resource (artifact) can be linked with surrounding square brackets, e.g. `[MyPage] ` or `[#123]`. These artifact links can take several forms(However, some of them haven't been tested yet).

###Simple Links

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

Two blank spaces at the end of the line do the same work as `<br/>` in _html_, making a line break. *(A little bit different from the origin post)*.  
Two carriage returns make a new paragraph.

--------------------

##<a name='blockquotes'></a>Blockquotes

Use the > character in front of a line, just like in email

	> Use it if you're quoting a person, a song or whatever.

	> You can use *italic* or lists inside them also.
	And just like with other paragraphs, all of these lines are still part of the blockquote, even without the > character in front.

To end the blockquote, just put a **blank line** before the following paragraph.

Output:

>Use it if you're quoting a person, a song or whatever.

>You can use italic or lists inside them also.  
And just like with other paragraphs, all of these lines are still part of the blockquote, even without the > character in front.


----------------

##<a name='preformatted_text'></a>Preformatted Text

If you want some text to show up exactly as you write it, without Markdown doing anything to it, just indent every line by at least 4 spaces (or 1 tab). As an alternative to indenting, you can use 4 or more tildes before and after the text. See examples in the Code Highlighting section(_xxxxx_ at the head of line is a placeholder for one tab):

	This line won't *have any markdown* formatting applied.  
	I can even write <b>HTML</b> and it will show up as text.  
	This is great for showing program source code, or HTML or even  
	Markdown. <b>this won't show up as HTML</b> but  
	exactly <i>as you see it in this text file</i>.  

Within a paragraph, you can use backquotes to do the same thing.
	`This won't be *italic* or **bold** at all.`

Output:

>This line won't *have any markdown* formatting applied.  
I can even write <b>HTML</b> and it will show up as text.  
This is great for showing program source code, or HTML or even  
Markdown. <b>this won't show up as HTML</b> but  
exactly <i>as you see it in this text file</i>.

Within a paragraph, you can use backquotes to do the same thing.
`This won't be *italic* or **bold** at all.`

##<a name='lists'></a>Lists

    * an asterisk starts an unordered list
    * and this is another item in the list
    + or you can also use the + character
    - or the - character

To start an ordered list, write this:

	1. this starts a list *with* numbers
	+  this will show as number "2"
	*  this will show as number "3."
	9. any number, +, -, or * will keep the list going.
	    * just indent by 4 spaces (or tab) to make a sub-list
	        1. keep indenting for more sub lists
	    * here i'm back to the second level

Output:

>* an asterisk starts an unordered list
* and this is another item in the list
+ or you can also use the + character
- or the - character

To start an ordered and multi-level list, write this:

>1. this starts a list *with* numbers
+  this will show as number "2"
*  this will show as number "3."
9. any number, +, -, or * will keep the list going.
    * just indent by 4 spaces (or tab) to make a sub-list
        1. keep indenting for more sub lists
    * here i'm back to the second level

---------

##<a name='tables'></a>Tables

A little bit different from the origin one, alignment attribute added.

	First Header  		| Second Header 	| Third Header  |Fourth Header
	------------- 		| :------------- 	| --------: 	| :----------:
	Align Default 		|Align Left 		| Align Right 	| Align Center
	**Bold**			|*Italic* 			| _Italic_ 		| <s>Strike</s>

Output:


First Header  		| Second Header 	| Third Header  |Fourth Header
------------- 		| :------------- 	| --------: 	| :----------:
Align Default 		|Align Left 		| Align Right 	| Align Center
**Bold** 		|*Italic* 		| _Italic_ 	| <s>Strike</s>

-----------------

##<a name='more_headers'></a>More Headers

More ways of doing headers:

	# this is a huge header #  
	## this is a smaller header ##  
	### this is even smaller ###  
	#### more small ####  
	##### even smaller #####  
	###### smallest still: `<h6>` header  

Output:

># this is a huge header  
## this is a smaller header  
### this is even smaller  
#### more small  
##### even smaller  
###### smallest still: `<h6>` header  

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

>-------------------- 
 
Make sure you have a blank line above the dashes, though, or else:

	you will get a header
	-------

Output:

>you will get a header
---------

----------

##<a name='images'></a>Images

To include an image, just put a "!" in front of a text link:

	![alternate text](https://sourceforge.net/images/icon_linux.gif)

The "alternate text" will show up if the browser can't load the image.

You can also use a title if you want, like this:

	![tiny arrow](https://sourceforge.net/images/icon_linux.gif "tiny arrow")

Output:

>![alternate text](https://sourceforge.net/images/icon_linux.gif)

You can also use a title if you want, like this:

>![tiny arrow](https://sourceforge.net/images/icon_linux.gif "tiny arrow")

Some other features like macro is not covered here since not support in my lib.

What if you want to just show asterisks, not italics?

	* this shows up in italics: *a happy day*  
	* this shows the asterisks: \*a happy day\*

Output:

>* this shows up in italics: *a happy day*  
* this shows the asterisks: \*a happy day\*

The backslashes will disappear and leave the asterisks. You can do the same with any of the characters that have a special meaning for Markdown.

Many simple HTML tags are allowed, for example `<b>` And unknown tags will be dropped. To show a literal `<b>` or an unknown tag like <foobar> you need escape it with HTML entities: 

	<b>this will be bold.</b>  
	you should escape <unknown\> tags with "\" by placing which on the left of the close bracket.  
	&copy; special entities work.  
	\&copy; if you want to escape it.

Output:

><b>this will be bold.</b>  
you should escape <unknown\> tags with "\" by placing which on the left of the close bracket.  
&copy; special entities work.  
\&copy; if you want to escape it.

HTML tags that are **block-level** like `<div>` can be used, but if there is markdown formatting **within** it, you must add a `markdown` attribute: `<div markdown>`.

Some safe attributes are also allowed, permitting basic styling and layout: `<div markdown style="float:left">`. 

Individual ampersands (&) and less-than signs (<) are fine, they will be shown as expected.

-----------

##<a name='table_of_contents'></a>Table of Contents

You can display a list of links to jump to headers in a document. Sub-headers will be nested. This function is quite like anchor, however more flexible and less labor. The following example is from the [Markdown Syntax Guide](http://sourceforge.net/p/forge/documentation/markdown_syntax/), however not functions well for me, more details about TOC [Table of Contents](http://pythonhosted.org/Markdown/extensions/toc.html).

	[TOC]
	#Section 1  
	##Sub-section 1  
	#Section 2  

	#Section 1  
	
	##Sub-section 1  
	
	#Section 2  

Output:

[TOC]  

# Section 1

## Sub-section 1

# Section 2


# Section 1

## Sub-section 1

# Section 2



**Alternative** solution to achieve such function is using hyer links if the above does not work for your case.

	#[Section 1](#section1)
	##[Sub-section 1](#sub-section1)
	#[Section 2](#section2)

	#<a name="section1"></a>Section 1  
	
	##<a name="sub-section1"></a>Sub-section 1  
	
	#<a name="section2"></a>Section 2  

Output:

>#[Section 1](#section1)
##[Sub-section 1](#sub-section1)
#[Section 2](#section2)

#<a name="section1"></a>Section 1  
	
##<a name="sub-section1"></a>Sub-section 1  
	
#<a name="section2"></a>Section 2  

--------------


References:

* [Markdown Syntax Guide](http://sourceforge.net/p/forge/documentation/markdown_syntax/)
* [Markdown Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)
* [Table of Contents](http://pythonhosted.org/Markdown/extensions/toc.html)

# Markdown Syntax

Markdown is a language used by many platforms to specify basic text formatting. This page describes its syntax, with a focus on features that work well within CiviCRM's documentation guide books.


## Platform differences

Many platforms which use markdown have expanded or modified the [original syntax](https://daringfireball.net/projects/markdown/syntax), which has created subtle differences among various platforms.

Platform-specific markdown references:

* [Mattermost markdown](https://docs.mattermost.com/help/messaging/formatting-text.html)
* [Stack Exchange markdown](http://stackoverflow.com/editing-help)
* [GitHub markdown](https://help.github.com/categories/writing-on-github/)
* CiviCRM's guide book markdown:
    * Books are built with MkDocs which parses markdown with [Python-Markdown](https://pythonhosted.org/Markdown/).
    * **But** the extra markdown features available depend heavily on the *theme* and *markdown extensions* used within the book.
    * [Material](http://squidfunk.github.io/mkdocs-material/) is the recommended theme to be used for CiviCRM guide books
    * [Extensions included within Python-Markdown](https://pythonhosted.org/Markdown/extensions/) can be enabled in the book's `mkdocs.yml` file without installing anything.
    * [PyMdown Extensions](http://facelessuser.github.io/pymdown-extensions/) is a 3rd party package that provides many additionally useful extensions.

The remainder of this page will focus on the markdown syntax which can be used within MkDocs books with the Material theme and common markdown extensions.


## CiviCRM markdown code standards {:#standards}

To maintain some consistency and peace of mind for documentation content editors, we've [agreed](https://github.com/civicrm/civicrm-dev-docs/issues/43) to *recommend* the following syntax as markdown code standards. These are not hard rules though.

* **Line length:** write long lines (i.e. one line per paragraph) and set your text editor to view them with a "soft wrap".
* **Ordered lists:** use `1.` as delimiters.
* **Unordered lists:** use `*` to delimiters.
* **Headings:** use hashes like `## Heading 2`.


## Basic inline formatting

| Code | Result | Extension required |
| :-- | :-- | :-- |
| `*italics*` | *italics* |  |
| `**bold**` | **bold** |  |
| `***bold and italic***` | ***bold and italic*** |  |
| `` `monospace` `` | `monospace` |  |
| `~~strikethrough~~` | ~~strikethrough~~ | [Tilde](http://facelessuser.github.io/pymdown-extensions/extensions/tilde/) |
| `==highlight==` | ==highlight== | [Mark](http://facelessuser.github.io/pymdown-extensions/extensions/mark/) |

Alternate syntax: Underscores for `_italics_` and `__bold__` also work on most
platforms.


## Hyperlinks

### External hyperlinks

A basic external hyperlink in a sentence:

```
Try [CiviCRM](https://civicrm.org) for your database.
```

### Internal hyperlinks

An internal hyperlink on mkdocs. Both of the following syntaxes work because the `.md` is optional. *Make sure to use an absolute path and precede the path with a slash, as shown below.*

```
[extensions](/extensions/basics.md)
[extensions](/extensions/basics.md)
```

### Named hyperlinks

If you're using a one link in many place throughout a page, you can define the URL in one place as follows

```
See [CRM-1234] for more details.

My favorite issue is [CRM-1234].

[CRM-1234]: https://issues.civicrm.org/jira/browse/CRM-1234
```

(The third line can be placed anywhere in the file.)

### Named hyperlinks with custom text

```
After learning [how to foo a bar][foobar], then you can party!

[foobar]: https://example.com/foobar
```


## Line breaks and whitespace

**Single line breaks** in markdown code are eliminated in display:

```
This text will all show up
on the
same
line.
```

This makes it easy to avoid very long lines in markdown code. As a rule of
thumb, keep your markdown code free of lines longer than 80 characters
where possible.

**Double line breaks** create separate paragraphs:

```
This is
one paragraph.

This is a second.
```


## Headings

```md
# Heading 1

## Heading 2

### Heading 3

#### Heading 4
```

The above syntax is [called](http://pandoc.org/MANUAL.html#headers)
"ATX style headers" in markdown terminology, and is the [preferred](#standards)
syntax within the CiviCRM community.
An alternate syntax called "setext style headers" works for h1 and h2 as
follows (but please avoid creating new content with this syntax).

```
Heading 1
=========

Heading 2
---------
```

### Custom heading IDs

!!! tip "Extension required"
    To use custom heading IDs in MkDocs, insert the following code in `mkdocs.yml` to enable the [Attribute Lists](https://pythonhosted.org/Markdown/extensions/attr_list.html) extension:

    ```yml
    markdown_extensions:
      - markdown.extensions.attr_list
    ```

Custom heading IDs allow you to link to specific sections in a page by appending the heading ID to the page URL. Most markdown platforms (e.g. MkDocs, GitHub) automatically set a heading ID for every heading and do so using the text of the heading itself. Sticking with the default is great is most cases, however sometimes you want to override it.

Setting a custom ID:

```
## How to foo a bar {:#foo}
```

This is helpful when you think that readers are likely to frequently link
to this section in the future.

* Custom heading IDs will remain the same (thus preserving incoming links) even after the text of the heading is edited.
* Custom heading IDs create shorter URLs.


## Lists

### Unordered lists

```
* My first item is here.
* My second item is here and a
  bit longer than the first.
* Then, a third.
```

Alternate syntax:

* Unordered lists also recognize `-` and `+` as item delimiters.
* Markdown is somewhat flexible with the quantity and position of spaces when making lists.


### Ordered lists

```
1. Item
1. Item
1. Item
```

Alternate syntax:

* Ordered lists items are automatically re-numbered sequentially upon display which means all items can begin with `1`, or they can be ordered sequentially in code.


### Nested lists

List sub-items must be indented 4 spaces:

```
1.  Item
    1.  Item
    1.  Item
1.  Item
    * Item
    * Item
    * Item
1. Item
```


## Code

### Inline code

Use backticks to create inline monospace text:

```
Some `monospace text` amid a paragraph.
```

And if you need to put a backtick inside your monospace text, begin and end
with two backticks:

```
Some ``monospace text with `backticks` inside``, and all amid a paragraph.
```


### Code blocks

A block of **"fenced code"** with three or more backticks on their own line.

````
```
CODE
BLOCK
```
````

*Fenced code can use more thank three backticks when necessary to represent code that contains 3 backticks (which is what you'd see in the source for this page).*

Alternate syntax: For fenced code, the tilde `~` character also works
in place of the backtick character but should be avoided for consistency.


A block of **"indented code"** with four spaces at the start of each line:

```
    CODE
    BLOCK
```


### Syntax highlighting for code

*   For code blocks, some platforms (e.g. GitHub) will guess guess the language of the code and automatically apply syntax highlighting to the display.
*   To force a particular type of syntax highlighting, use fenced code with a keyword (like `javascript` in this case) as follows:

    ````
    ```javascript
    var cj = CRM.$ = jQuery;
    ```
    ````

*   Available language keywords:
    *   Differ slightly by markdown platform
    *   Common language keywords that work on most platforms: `bash`, `css`, `docker`, `html`, `javascript`, `js`, `json`, `markdown`, `md`, `perl`, `php`, `python`, `ruby`, `scss`, `sh`, `smarty`, `sql`, `xhtml`, `xml`, `yaml`
    *   The Material theme for MkDocs will use the Pygments python library when possible, and in this case provide syntax highlighting for [over 300 languages](http://pygments.org/docs/lexers).

*   Syntax highlighting cannot be forced for indented code.
*   Syntax highlighting for inline code is possible with [InlineHilite](http://facelessuser.github.io/pymdown-extensions/extensions/inlinehilite/) but not recommended.
*   [Stack Exchange syntax highlighting][stack exchange syntax highlighting] is
    done differently.

[stack exchange syntax highlighting]: http://stackoverflow.com/editing-help#syntax-highlighting



### Code blocks within lists

#### Fenced code within lists

!!! tip "Extension required"
    To use fenced code within lists in MkDocs, install [PyMdown Extensions](http://facelessuser.github.io/pymdown-extensions) and then insert the following code in `mkdocs.yml` to enable the [Superfences](http://facelessuser.github.io/pymdown-extensions/extensions/superfences/) extension:

    ```yml
    markdown_extensions:
      - pymdownx.superfences
    ```

Then insert fenced code into a list as follows:

````md
*   First item
*   Look at this code:

    ```md
    code
    block
    ```

*   More list items
````

#### Indented code within lists

You can use indented code within lists without needing any markdown extensions. Keep a blank line above and below the code and indent the code *4 spaces more than your list content*, like this:

```md
*   First item
*   Look at this code:

        CODE BLOCK WITHIN
        TOP LEVEL LIST ITEM

*   More list items
*   A nested list is here:
    1.  Alpha
    1.  Beta, with some code

            CODE BLOCK WITHIN
            SUB-LIST ITEM

    1. Gamma

*   Fun, right?
```



## Admonitions

!!! tip "Extension required"
    To use admonitions in MkDocs, insert the following code in `mkdocs.yml` to enable the [Admonitions](https://pythonhosted.org/Markdown/extensions/admonition.html) extension:

    ```yml
    markdown_extensions:
      - markdown.extensions.admonition
    ```

### Syntax

Simple example:

```md
!!! note
    Here is a note for you.
```

Add a custom title (make sure to quote the title):

```md
!!! danger "Don't try this at home!"
    Stand back. I'm about to try science!
```

(You can also add an admonition *without* a title by passing an empty string `""` in place of the title.)

### Types

The types of admonitions available for use in MkDocs depend on the theme being used. The Material theme [supports](http://squidfunk.github.io/mkdocs-material/extensions/admonition/#types) the following types:

!!! note
    I am a "note" admonition and look the same as "seealso".

!!! tip
    I am a "tip" admonition and look the same as "hint" and "important".

!!! warning
    I am a "warning" admonition and look the same as "attention" and "caution".

!!! danger
    I am a "danger" admonition and look the same as "error".

!!! summary
    I am a "summary" admonition and look the same as "tldr".

!!! success
    I am a "success" admonition and look the same as "check" and "done".

!!! failure
    I am a "failure" admonition and look the same as "fail" and "missing".

!!! bug
    I am a "bug" admonition.


## Images

Images function mostly the same as hyperlinks, but preceded by an exclamation
point and with alt text in place of the link text.

```
![Alt text](/directory/image.png)
```

or

```
![Alt text][id]

[id]: /directory/image.png
```


## Other markdown syntax

*   [Tables] (to be avoided when possible)
*   [Emojis] (great for Mattermost)
*   Blockquotes

        > This text is a blockquote, typically used
          to represent prose written by a person. It
          will be displayed slightly indented.

[Emojis]: http://www.webpagefx.com/tools/emoji-cheat-sheet/
[Tables]: https://help.github.com/articles/organizing-information-with-tables




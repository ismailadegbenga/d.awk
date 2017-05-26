d.awk
=====

An [Awk][] script to generate documentation from [Markdown][]-formatted
comments in C, C++, JavaScript and any other language that uses `/* */` for 
multiline comments in its source code.

For example, add a comment like this to your source file:

    /**
     * My Project
     * ==========
     *
     * This is some _Markdown documentation_ in a `source
     * file`.
     */

Then use Awk to run the `d.awk` script on it like so:

    $ ./d.awk file.c > doc.html
	$ # or:
    $ awk -f d.awk file.c > doc.html

The text within the `/** */` comment blocks are parsed as Markdown, and
rendered as HTML.

A typical use case to bundle the `d.awk` script with your project's source and
to then add a `docs` target to the Makefile:

    docs: api-doc.html
    api-doc.html: header.h d.awk
        $(AWK) -f d.awk $< > $@

It supports most of Markdown:
* **Bold**, _italic_ and `monospaced` text.
* Both header styles
* Horizontal rules
* Ordered and Unordered lists
* Code blocks and block quotes
* Hyperlinks and images
* A large number of HTML tags can be embedded in a document

It also supports a number of extensions, mostly based on GitHub syntax:
* ```` ``` ````-style code blocks
* GitHub-style task lists
* MultiMarkdown style footnotes and abbreviations.
* Backslash at the end of a line forces a line break.

There are additional scripts in the distribution:

 * `mdown.awk` - Generates HTML from a normal Markdown file.
 * `xtract.awk` - Extracts the Markdown comments of a source file.
 * `wrap.awk` - Formats a Markdown text file to fit on a page.

[Awk]: https://en.wikipedia.org/wiki/AWK
[Markdown]: https://en.wikipedia.org/wiki/Markdown

## Motivation

`d.awk` is a inspired by the [Javadoc][] and [Doxygen][] tools which generate
HTML documentation from comments in source code.

It is meant for programming languages like C, C++ or JavaScript that use the
`/* */` syntax for comments (it will work with Java and C#, though the
existence of bundled documentation tools for those languages makes it
redundant).

It has two distinguishing features:

Firstly, it is written in the ubiquitous Awk language. You can distribute the
`d.awk` script with your project's source code and your users will be able to
generate documentation without requiring additional 3rd party tools.

Secondly, the documentation use Markdown for text formatting, which has several
advantages:

 * It is well known and widely used.
 * It reads easily and won't clutter your code comments with markup tokens.

[Javadoc]: https://en.wikipedia.org/wiki/Javadoc
[Doxygen]: http://www.stack.nl/~dimitri/doxygen/

## Usage

### d.awk

Comments must start with `/**`, and each line in the comment must start with a
`*` - this is so you can control which comments are included in the
documentation.

To generate documentation from a file `demo.c`, run the `d.awk` script on it
like so:

    $ awk -f d.awk demo.c > doc.html

The file `demo.c` in the distribution provides a demonstration of all the
features and the supported syntax.

Configuration options can be set in the `BEGIN` block of the script, or passed
to the script through Awk's `-v` command-line option:
- `-v Title="My Document Title"` to set the `<title/>` of the HTML
- `-v stylesheet="style.css"` to use a separate file as style sheet.
- `-v TopLinks=1` to have links to the top of the document next to headers.
- `-v classic_underscore=1` words_with_underscores behave like old markdown
  where the underscores in the word counts as emphasis. The default behaviour
  is to have `words_like_this` not contain any emphasis.

The stylesheet for the output HTML can also be modified at the bottom of the
script.

### mdown.awk

Creates an HTML document from a Markdown file.

For example, to generate HTML from this `README.md` file, type:

    $ awk -f mdown.awk README.md > README.html

The command line options are the same as `d.awk`'s.

### xtract.awk

This script extracts the comments from a source file, without processing it as
Markdown.

    $ awk -f xtract.awk demo.c

A use case is to extract the comments from a source file into a new Markdown
document, such as a GitHub wiki page.

### wrap.awk

`wrap.awk` makes a Markdown document more readable by word wrapping long lines
to fit into 80 characters.

For example, to use it on this `README.md` file, run

    $ awk -f wrap.awk README.md

To specify a different width, use `-v Width=60` from the command line.

## License

The license is officially the MIT license, but the individual files can be
redistributed with this notice:

    (c) 2016 Werner Stoop
    Copying and distribution of this file, with or without modification,
    are permitted in any medium without royalty provided the copyright
    notice and this notice are preserved. This file is offered as-is,
    without any warranty.



---
title: Why write plain text?
date: 2023-02-12
permalink: /posts/2023/12/why-plain-text/
tags:
  - plain text
  - workflows
  - writing
---

Here's why:

- *A writing environment that models an argument, not a sheet of paper.*
Plain-text documents decouple writing environment from distribution format.
In my plain-text documents, each sentence gets a line of its own: sentences always begin at the left margin.
This makes it easier to see and edit the sequence of argument within paragraphs.
Lines of text are re-flowed into paragraphs for distribution --- i.e., by the program that converts my plain-text document into `pdf` or `docx` or `html`.

- *Persistent private comments.*
Like programming languages, plain-text markup languages have a syntax for comments --- that is, for designating text that will not be read by the engine that processes the file.
Plain-text comments can be used like the comment function in Microsoft Word or Google Docs.
The difference is that comments in plain-text files are, by default, masked off and omitted from the `pdf` and `docx` files generated from them.
Because the comments do not render in the publication formats, I can retain them in my working document<!--like this--> throughout the whole life-cycle of the document, or as long as they remain useful to me.
I can also [comment out] whole sentences or paragraphs if, for instance, I need to reduce the word count in one version of a document, but think I may re-instate the suppressed material in a later version.
<!--Here is another sentence, suppressed from the publication version.-->

- *Persistent lightweight file history.*
Plain-text files can be placed under `git` version control, just like program code.

- *Automated formatting of citations and bibliography.*
In a plain-text environment, I never keyboard citations.
Citations are formatted by script, drawing on machine-readable bibliographies that I curate.
The output must be proofed.
This progressively improves the quality of my bibliographical data and debugs the scripts that format citations.

- *Exposure to command-line utilities.*
As text files, my documents can be manipulated with the same command-line utilities and applications that I use to interact with any other text file in my system.

For an example of a simple plain-text document, view the [Markdown source] for this post.

[Markdown source]: https://raw.githubusercontent.com/icornelius/icornelius.github.io/master/docs/_posts/2023-02-12-why-plain-text.md
[comment out]: https://en.wiktionary.org/wiki/comment_out

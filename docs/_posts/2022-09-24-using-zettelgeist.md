---
title: Using ZettelGeist
date: 2022-09-24
permalink: /posts/2022/09/using-zettelgeist/
tags:
  - ZettelGeist
  - workflows
  - notetaking
  - writing
---

Teachers and researchers need to create and retrieve notes.
For the past two years, I have used [ZettelGeist](https://zettelgeist.github.io/) for these functions.
This post explains my approach.
(For a quick-start guide and documentation, see the ZettelGeist [Wiki](https://github.com/ZettelGeist/zettelgeist/wiki).)

ZettelGeist is a plain-text system for creating and retrieving notes.
The initial aim was to emulate the data structure of a paper notecard (the so-called [Zettelkasten method](https://en.wikipedia.org/wiki/Zettelkasten)), but the current version of ZettelGeist is more powerful.
It will index and retrieve any text file in Markdown format:
research notes, yes, but also
lecture notes,
course assignments,
conference papers,
peer review reports,
grant proposals,
cuttings from article drafts --- any text document that might be useful in subsequent work.
From the perspective of ZettelGeist all these documents are just so many zetteln.

# One directory with good filenames
I keep my zetteln in a single directory, named `zetteln` and placed under `git` version control.
Currently there are 992 files in this directory, totaling about 450,000 words.
The number of files does not matter, for I never scroll through the full list looking for the file I want.
When I want to view or edit a file, I use a ZettelGeist query.

Identifying a file by query means that filenames need not be descriptive, but good naming conventions remain important.
There are two considerations:

1. Filenames must be unique.
The easiest way to ensure uniqueness is by writing a timestamp into the filename: the `zettel` command does this with the option `--name timestamp`.

2. Filenames should be recognizable to a human.
Although I can pipe filenames directly from query results to a text editor, I often want to call up a file by name --- for instance, when I am writing an essay in one file and need to consult notes stored as other files.
For these operations, it is helpful to have filenames that can be logged in human short-term memory.
The `--id` and `--counter` options and their arguments achieve this aim.

A recent zettel has the name `ONeilEnglishOrthography-0506-20220912212619.md`.
This filename was written with the command

```
zettel --id ONeilEnglishOrthography --counter default --name id counter timestamp
```

Uniqueness is ensured by the timestamp.
The value of the counter is that a four-digit hyphen-delimited number is easier than the timestamp to parse visually.
If I have two or more notes from O'Neil's essay, I will be able to call the files with the `id` and `counter` and let tab completion fill in the rest.
When I am not taking notes from a particular source, I may use a topical `id` or even a dummy (e.g., `note`).
The `id` only needs to be recognizable, not descriptive, for the contents of the file are indicated by a metadata block within the file, not by the filename.

# The metadata block

Every file indexed by ZettelGeist begins with a [yaml](https://en.wikipedia.org/wiki/YAML)-encoded metadata block.
ZettelGeist offers a variety of [metadata fields](https://github.com/ZettelGeist/zettelgeist/wiki/Manual#zettelgeist-fields), all of which are optional.
My metadata block for `ONeilEnglishOrthography-0506-20220912212619.md` reads as follows:

```
---
title: Orthography of modern English
summary: Lecture notes for ENGL 390, fall 2022
bibkey: '@ONeilEnglishOrthography1980'
tags:
- 000 lecture notes
- English
- writing systems
- spelling
- phonology
mentions:
- O'Neil, Wayne
---
```

The `bibkey` field is generated by [Better BibTeX](https://retorque.re/zotero-better-bibtex/), a plugin for Zotero.
It links this file to full bibliographical details for the source on which I have taken notes.
The fields `title` and `summary` identify the contents of the file; `tags` and `mentions` facilitate retrieval of the file.
A typical query might read as follows:

```
zfind --database index.db --query-string 'tags:"writing systems" & tags:English & tags:"lecture notes"' --show-title --show-summary --show-filename
```

This query returns the title, summary, and filename of all files in the database with tags containing the terms "writing systems," "English," and "lecture notes."
(For more on querying with `zfind`, see [here](https://github.com/ZettelGeist/zettelgeist/wiki/Manual#retrieving-cards)).

# Tags and mentions

To write effective queries, you need accurate lists of tags and mentions used in the database.
Such lists are easily generated with the `zfind` options `--get-all-tags` and `--get-all-mentions`, respectively.
I use the following script to update the lists of tags and mentions every time I rebuild the database:

```
#!/bin/bash

if [ -d ~/zenv ]; then
  source ~/zenv/bin/activate
fi

cp tags.txt{,.orig}
cp mentions.txt{,.orig}

zimport --database index.db --create --fullpath --dir .

zfind --database index.db --get-all-tags | sort | uniq > tags.txt

zfind --database index.db --get-all-mentions | sort | uniq > mentions.txt

echo "Comparing tags..."
comm tags.txt.orig tags.txt -3

echo "Comparing mentions..."
comm mentions.txt.orig mentions.txt -3
```

This script

1. creates backup copies of the current lists of tags and mentions
2. rebuilds the database, incorporating any new or changed files
3. regenerates the list of tags and mentions, and, finally
4. compares the new lists of tags and mentions with the old ones

The comparison is useful because it shows me what tags are added (or deleted) by the latest changes to the database.
This helps me curate the tag-list and identify errors and redundancies.
(I could also use `git` for this purpose.
I use `comm` instead --- and add `tags.txt*` and `mentions.txt*` to my `.gitignore` --- because it seemed excessive to permanently record snapshots of these files.)


The two files `tags.txt` and `mentions.txt` function as indexes of subjects and persons, respectively.
Currently `tags.txt` has over a thousand lines.
I never scroll through this file.
Instead, when I am looking for appropriate search terms for a query, I use `grep`.
For instance, `grep verse tags.txt` returns the following:

```
alliterative verse
didactic verse
octosyllabic verse
quantitative verse
syllabic verse
verse syntax
```

Tags like `000 lecture notes` are special: in my tagging system, `000` is prefixed to tags that indicate the type or genre of the document.
For instance, the query

```
zfind --database index.db --query-string 'tags:"000 translation"' --show-title
```

returns the titles of documents containing translations I have made, omitting documents on the subject of translation.
Some other genre tags in my database are
`000 conference paper`,
`000 cutting`,
`000 draft`,
`000 peer review`,
`000 proposal`,
`000 transcription`, and
`000 url bookmark`.


# Working with Pandoc

Finally, I note that ZettelGeist reads and writes only the first yaml-encoded metadata block in a document.
This is useful because it leaves subsequent metadata blocks available for other uses, including by [Pandoc](https://pandoc.org/).
A file that contains a conference paper that I intend to print for delivery might have two consecutive metadata blocks.
For instance:

```
---
title: 'PPEA 3.0: The Piers Plowman Electronic Archive and CEDAR'
summary: Script for 2022 STS conference
comment: This text reworks CEDAR grant proposal and notes for presentation at Marta
  Werner's textual studies seminar and at STS
tags:
- CEDAR
- textual criticism
- Society for Textual Scholarship
- 000 conference paper
- PPEA Piers Plowman Electronic Archive
mentions:
- Stinson, Timothy
- Duggan, Hoyt N.
---
---
author:
- Ian Cornelius
- Timothy Stinson
date: 26 May 2022
fontsize: 12pt
geometry: margin=1in
mainfont: Junicode
linestretch: 1.5
---
```

The first of these blocks is used by ZettelGeist to index and retrieve the document.
The second is used by Pandoc to generate a nice pdf that I can print or share.
(In fact, Pandoc also picks up the `title`-value from the first block, since that key is defined by Pandoc.
For more on the way that Pandoc parses yaml metadata blocks, see [here](https://pandoc.org/MANUAL.html#extension-yaml_metadata_block)).
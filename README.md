# shbib

A BibTeX-centric bibliography manager written in POSIX shell

- Minimal (*only require **POSIX compliant shell***)
- Search BibTeX on **crossref** and **google scholar** (Key press: `s`, `p`, `w`)
- Create and modify bib file on the fly (Key press: `i`, `e`)
- Automatically and manually *rename* and *encode metadata* to pdf file (Key press: `B`, `b`)
- Create and view sublibrary (Key press: `R`, `r`)
- Write notes for BibTeX entry

## Table of Content

<!-- vim-markdown-toc GFM -->

* [Preview](#preview)
* [Introduction](#introduction)
* [Dev's note](#devs-note)

<!-- vim-markdown-toc -->

## Preview

Search function:

[![search](https://asciinema.org/a/dAqUiKCVwkxcS0vNUyqd8fVnP.png)](https://asciinema.org/a/dAqUiKCVwkxcS0vNUyqd8fVnP)

Note function:

[![note](https://asciinema.org/a/JLILWWhC0PaAiEL69dTRbhlb0.png)](https://asciinema.org/a/JLILWWhC0PaAiEL69dTRbhlb0)

## Introduction

The prerequisites for `shbib` is:
1. Dependencies including `read`, `curl`, `sort`, `pdfinfo`, `gs`, `grep`, `curl`, `pdfinfo`, `xclip/xsel/pbcopy (Mac OS)`
2. Create two environmental variables: `$BIB` and `$BIB_PDF_PATH`. `$BIB` is the path to your BibTeX file, and `$BIB_PDF_PATH` is the path to your pdf directory. You can add these three variables by modifying the paths and running the following pseudo code in terminal:

```sh
printf '%s\n' "export BIB='path/to/your/BibTeX/file'" >> $HOME/.profile
printf '%s\n' "export BIB_PDF_PATH='path/to/your/pdf/directory'" >> $HOME/.profile
printf '%s\n' "export BIB_UNI_KEY='Key to access your journals by your university'" >> $HOME/.profile	# Not required
```

To run `shbib`, you just need open your terminal, type `git clone https://github.com/huijunchen9260/shbib` to download this project, `cd` to the directory with `shbib` script, and run `shbib` by typing `./shbib`.

Type `?` to understnad the keybinding corresponding to each action:

```
k/↑ - up
j/↓ - down
l/→ - right
h/← - left
Ctrl-f/PageDown - PageDown
Ctrl-u/PageUp - PageUp
g/Home - go to top
G/End - go to bottom
s - search online by text
p - search online by metadata in PDF file
w - search google scholar by text
c - copy BibTeX entry from your $BIB
o - open PDF file by BibTeX entry
i - insert new BibTeX entry
e - edit existing BibTeX entry
b - manually build database by rename and encode metadata into PDF file
B - automatically build database by rename and encode metadata into PDF file
R - create new BibTeX entry sublibrary
    store at: $BIB_PDF_PATH/Libs
r - open existing BibTeX entry sublibrary
    store at: $BIB_PDF_PATH/Libs
n - write notes for BibTeX entry
    store at: $BIB_PDF_PATH/Notes
/ - search the current page
    default: case-insensitive search
    contain uppercase: case-sensitive search
? - show keybinds
```

Nice! You are all set!

## Dev's note

This project is highly inspired by [shfm](https://github.com/dylanaraps/shfm). The skeleton is borrowed from that project, while the content is optimized from my old project [dmenubib](https://github.com/huijunchen9260/dmenubib).

Writing in POSIX shell is an adventure. What you need to do is to force yourself to NOT use external program , to NOT create sub-shell by command substitution `$()`, and furthermore, to NOT utilize regular expression but pathname globbing instead. I find a lot of useful technique that can basically replace the usage of `sed`, `awk`, and most external program while developing this project. Those technique are:

1. `set -- $var` to create the only ["array"](http://www.etalabs.net/sh_tricks.html) in POSIX shell, and use `for line do ... done` to manipulate each item in this array.
2. Manipulating internal field separator (`$IFS`) to decide how `set -- $var` would separate the array. E.g., we can create newline character `$nl` to force `set -- $var` to separate each line as an element in array.
3. `case` statement globbing matching -> replace `sed` and `awk` regular expression matching
4. Flow control using exit value (`ref_search` function and shell special parameters `$?`)
5. Create a newline and tab character, `$nl` an `$tab`, by create a "hard" character:
    ```sh
    nl='
    '
    tab='	'
    ```
6. Json file manipulation is also possible by pure POSIX shell. Look into `shbib` for `json_pretty_print` function for detail information.
7. Case-insensitive search is done by transforming both searching string adn searched item to lower case, then match them together. Hope someone can provide a more elegant way to do so.

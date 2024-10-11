# Preface

This plugin provides an elegant way to manage and invoke text filters. A filter is a program that accepts text at standard input, changes it in some way, and sends it to standard output.

Vim natively support the [filter commands](https://vimhelp.org/change.txt.html#filter) to modify the selected text with external filter programs, but when the command becomes complex, typing it out one character at a time becomes tedious. Additionally, wrapping each command into a new Vim command or map is not very practical if you have to deal with many filters.

By using this plugin, you can encapsule complex filter commands into shell/python/awk scripts in a centralized directory and enumerate or run them with a `:{range}TP[!] {name}` command:

![](https://skywind3000.github.io/images/p/textproc/tp1.gif)

When bang '!` is included, you can preview changes in a separated window without modifying current text:

![](https://skywind3000.github.io/images/p/textproc/tp2.gif)

This is useful when debugging your filter script.


## Features

- Command line completion for filter names.
- Script files got execution privilege in the root (`g:textproc_root`) directory will be listed.
- Script files with  in the root directory will be listed by default.


## Installation

```VimL
Plug 'skywind3000/vim-text-process'

" text-processor search path, a comma separated string
let g:textproc_root = '~/.vim/text'

" preview window split method: auto/vert/horizon
let g:textproc_split = 'auto'

" text-processor script runner
let g:textproc_runner = {
    \ 'py': 'python',
    \ 'sh': '/usr/bin/bash',
    \ 'awk': '/usr/bin/gawk -f',
    \ }
```

## Documentation

This plugin provides a command:

```viml
:{range}TP[!] {name}
```

It will filter {range} lines through the external program {name}, the external program will be found in a "text" sub-directory inside every runtimepath, eg.:

     ~/.vim/text
     ~/.config/nvim/text

or a comma separated search path list defined in `g:textproc_root`.

Command line completion can be utilized to query available filters. It can be done by pressing `<TAB>` after the "`:TP`" command.

When a "`!`" is included, the selected text will not be replaced, and you can preview the result from a split window. The text filter programs are hard to debug, and it can mess up the selected text unexpectly due to defects. Using "`TP! xxx`" is helpful for debugging.

## Example

Create a new file `remove_markdown_links.py` inside `~/.vim/text`:

```python
import sys
import re
import codecs

sys.stdin.reconfigure(encoding = 'utf-8')
sys.stdout.reconfigure(encoding = 'utf-8')

content = sys.stdin.read()

pattern = r'\[(.*?)\]\(.*?\)'
new_content = re.sub(pattern, r'\1', content)

sys.stdout.write(new_content)
```

It can be used by:

```viml
:{range}TP remove_markdown_links
```

Another example, `markdown_to_dokuwiki.sh`:

```bash
#! /usr/bin/bash
pandoc -f markdown-simple_tables-multiline_tables+pipe_tables -t dokuwiki
```

Use it by `:{range}TP markdown_to_dokuwiki`.

One last example, an awk script `passwd_name_home.awk`:

```awk
BEGIN {
    print "Users and thier corresponding home"
    print " UserName \t HomePath"
    print "___________ \t __________"

    FS=":"
}

{
    print $1 "  \t  " $6
}

END {
    print "The end"
}

```

Awk scripts are incredibly useful for processing columnar data. This script can read the content of `/etc/passwd` and output user names along with their home directories.


## Searching

Text filter programs will be searched from directories below:

- The "`text`" sub-directory inside every runtimepath.
- The "`text/{filetype}`" sub-directory inside every runtimepath.
- Every directory defined in the list of "`g:textproc_root`"
- Every "`{filetype}`" sub-directories of "`g:textproc_root`"

A file inside each search path will be considered as a valid filter:

- If the file has got the execution privilege.
- if the file has a `.[py|sh|pl|lua|sh|awk|js|cmd|bat]` extension name.

## Settings

```
g:textproc_home    - sub-directory name, default to "text"
g:textproc_root    - a list of script search path.
b:textproc_root    - local list of script search path.
g:textproc_split   - preview split mode: "auto", "vert" or ""
g:textproc_runner  - a dictionary of filter runners
```

If a filter program name is starting with a underscore "`_`" it will not be included, like "`_textlib.py`", disable this behavior by changing "`g:textproc_underscore`" to zero.



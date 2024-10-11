# Preface

Text Processing Plugin for Vim/NeoVim

## Installation

```VimL
Plug 'skywind3000/vim-text-process'
```

## Configuration

```VimL
" text-processor search path, a comma separated string
let g:textproc_root = '~/.vim/textprocess'

" preview window split method: auto/vert/horizon
let g:textproc_split = 'auto'

" text-processor script runner
let g:textproc_runner = {
    \ 'py': 'python',
    \ 'pl': 'perl',
    \ 'awk': '/usr/bin/gawk',
    \ 'js': 'node',
    \ }
```

## Documentation

This plugin provides a command:

```viml
:{range}TP[!] {name}
```



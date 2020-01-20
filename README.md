# oldfugitive.vim

## Background

This repository stores an old version of fugitive.vim with the old `:Git diff`
behavior. See https://github.com/tpope/vim-fugitive/issues/1415 for more
information.

I don't know when exactly the new `:Git diff` was added, but based on
https://github.com/tpope/vim-fugitive/graphs/contributors the new activity on
fugitive happened starting in early 2018, so I am picking a
[commit](https://github.com/tpope/vim-fugitive/tree/b82abd5bd583cfb90be63ae12adc36a84577bd45)
from around that period. Also, until around June 2018, fugitive was still a
single file (i.e. no separate autoload file, etc.), which makes the
substitutions particularly easy to perform.

## Installation

Install using any of the package managers. For example in vim-plug, add the line:

```
Plug 'riceissa/vim-oldfugitive'
```

Then call `:PlugInstall vim-oldfugitive`

## Steps to reproduce

Note: If you just want to use this plugin, use the installation instructions
above. This section is just gives information on how this plugin was created
(mostly as notes to myself).

The basic idea is pretty simple: find an old copy of fugitive, disable all the
commands (because we want the latest version of fugitive to handle those), and
make available a single command that has the old behavior of `:Git diff`.
Finally, we don't want naming clashes between the real fugitive and this old
version (vim stores lots of things in a global namespace), so we rename all
instances of "fugitive" appearing in the plugin to "oldfugitive".

Run the following in a shell:

```bash
mkdir -p vim-oldfugitive/plugin
cd vim-oldfugitive
curl https://raw.githubusercontent.com/tpope/vim-fugitive/b82abd5bd583cfb90be63ae12adc36a84577bd45/plugin/fugitive.vim > plugin/oldfugitive.vim
```

Open `plugin/fugitive.vim` in vim.

Run the following substitutions:

```vim
:%s/\Cfugitive/oldfugitive/g
:%s/\CFugitive/OldFugitive/g
```

Delete all lines starting with `call s:command(` (this can almost be done with
a `:g/.../d` but there are line continuations that make it annoying).

After the line

```
" Section: Git
```

add the following line:

```
call s:command("-bang -bar -nargs=* -complete=customlist,s:EditComplete GD :execute s:Git(<bang>0,'diff '.<q-args>)")
```

The above removes all fugitive commands (like `:Git`, `:Gstatus`, etc.) and
replaces it with a single command, `:GD` which behaves like fugitive's old
`:Git diff`.

## License

Copyright (c) Tim Pope.  Distributed under the same terms as Vim itself.
See `:help license`.

# Neovim Python IDE Manual

A comprehensive guide to turning **Neovim into a powerful Python
development environment**.\
Designed for workflows using **Linux + tmux + Neovim**.

This document is both:

-   a **tutorial**
-   a **reference manual**

Assumption: you already know basic Vim motions.

------------------------------------------------------------------------

# Table of Contents

1.  Philosophy of Neovim
2.  Core Editing Grammar (motions + operators)
3.  Navigation at Scale
4.  Editing Power Techniques
5.  Search and Replace
6.  Registers and Macros
7.  Buffers / Windows / Tabs
8.  Essential Productivity Hacks
9.  Plugin Manager Installation
10. Essential Plugins for Python Development
11. LSP Setup (Pyright)
12. Autocomplete
13. Formatting (Black + Ruff)
14. Debugging with nvim-dap
15. Treesitter syntax parsing
16. Telescope fuzzy finding
17. Git integration
18. tmux + Neovim workflow
19. Complete init.vim configuration
20. Recommended Python toolchain

------------------------------------------------------------------------

# 1. Philosophy of Neovim

Neovim editing is based on **composable grammar**.

General structure:

    operator + motion

Examples

    dw      delete word
    d2w     delete two words
    ci"     change inside quotes
    dap     delete paragraph

Operators

  operator   meaning
  ---------- ----------
  d          delete
  c          change
  y          yank
  \>         indent
  \<         unindent

------------------------------------------------------------------------

# 2. Core Motions

## Character motions

    h left
    j down
    k up
    l right

## Word motions

    w next word
    b previous word
    e end of word
    ge end previous word

## Line motions

    0 beginning
    ^ first non-space
    $ end of line

## File motions

    gg start of file
    G end of file

## Screen motions

    Ctrl-d half page down
    Ctrl-u half page up
    Ctrl-f page down
    Ctrl-b page up

------------------------------------------------------------------------

# 3. Text Objects

Text objects operate on syntax structures.

Examples

    ci( change inside ()
    di" delete inside quotes
    yap yank paragraph

Objects

  object   meaning
  -------- --------------------
  iw       inner word
  aw       around word
  ip       inner paragraph
  ap       around paragraph
  i(       inside parentheses
  i"       inside quotes

------------------------------------------------------------------------

# 4. Navigation at Scale

Jump list

    Ctrl-o jump backward
    Ctrl-i jump forward

Marks

    ma set mark a
    'a jump to mark a

Search navigation

    /pattern
    n next
    N previous

------------------------------------------------------------------------

# 5. Editing Power Techniques

Repeat last command

    .

Join lines

    J

Replace char

    r

Indent block

    >ap

Auto-indent entire file

    gg=G

------------------------------------------------------------------------

# 6. Search and Replace

Search

    /pattern

Replace

    :%s/foo/bar/g

Replace with confirmation

    :%s/foo/bar/gc

------------------------------------------------------------------------

# 7. Registers

Registers store copied text.

Example

    "ayy

Paste

    "ap

System clipboard

    "+y
    "+p

------------------------------------------------------------------------

# 8. Macros

Record macro

    qa

Stop

    q

Execute

    @a

Repeat

    @@

------------------------------------------------------------------------

# 9. Buffers / Windows / Tabs

Buffers = files.

    :buffers
    :b filename
    :bd

Splits

    :split
    :vsplit

Move between splits

    Ctrl-w h
    Ctrl-w j
    Ctrl-w k
    Ctrl-w l

Tabs

    :tabnew
    gt
    gT

------------------------------------------------------------------------

# 10. Essential Productivity Hacks

Open file under cursor

    gf

Navigate error list

    :cnext
    :cprev

View help

    :help topic

------------------------------------------------------------------------

# 11. Plugin Manager Installation

Recommended plugin manager: **vim-plug**

Install

    curl -fLo ~/.local/share/nvim/site/autoload/plug.vim \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

Explanation

curl\
download file from internet

-fLo\
fail silently + output file

plug.vim\
plugin manager script

Install plugins

    :PlugInstall

------------------------------------------------------------------------

# 12. Essential Plugins

## Core plugins

    vim-plug            plugin manager
    nvim-treesitter     syntax parser
    telescope.nvim      fuzzy finder
    plenary.nvim        dependency
    lualine.nvim        statusline

------------------------------------------------------------------------

# Python development plugins

    nvim-lspconfig
    nvim-cmp
    cmp-nvim-lsp
    cmp-buffer
    cmp-path
    LuaSnip
    telescope.nvim
    nvim-dap
    nvim-dap-python
    vim-fugitive
    gitsigns

------------------------------------------------------------------------

# 13. LSP Setup

Install language server

    pip install pyright

Capabilities

-   go to definition
-   references
-   autocomplete
-   diagnostics

Key commands

    gd go to definition
    gr references
    K hover docs

------------------------------------------------------------------------

# 14. Autocomplete

Plugin

    nvim-cmp

Sources

-   LSP
-   buffer
-   filesystem

------------------------------------------------------------------------

# 15. Formatting

Recommended tools

    black
    ruff
    isort

Install

    pip install black ruff isort

------------------------------------------------------------------------

# 16. Debugging

Plugin

    nvim-dap

Python adapter

    nvim-dap-python

Capabilities

-   breakpoints
-   step over
-   inspect variables

------------------------------------------------------------------------

# 17. Treesitter

Treesitter provides advanced parsing.

Install grammars

    :TSInstall python

Benefits

-   better syntax highlighting
-   incremental selection
-   code navigation

------------------------------------------------------------------------

# 18. Telescope

Telescope is fuzzy finder.

Commands

    :Telescope find_files
    :Telescope live_grep
    :Telescope buffers

------------------------------------------------------------------------

# 19. Git Integration

Plugins

    vim-fugitive
    gitsigns

Commands

    :Git status
    :Git blame

------------------------------------------------------------------------

# 20. tmux + Neovim Workflow

Recommended layout

    tmux

    window 1 → editor
    window 2 → tests
    window 3 → git

Split inside tmux

    Ctrl-b %
    Ctrl-b "

------------------------------------------------------------------------

# 21. Complete init.vim

Below is a **full working configuration**.

``` vim
set number
set relativenumber
set expandtab
set tabstop=4
set shiftwidth=4
set clipboard=unnamedplus
set mouse=a

" wrapped line navigation
nnoremap j gj
nnoremap k gk

" fast escape
inoremap jj <Esc>
inoremap jk <Esc>

call plug#begin('~/.config/nvim/plugged')

Plug 'neovim/nvim-lspconfig'
Plug 'nvim-treesitter/nvim-treesitter'
Plug 'nvim-telescope/telescope.nvim'
Plug 'nvim-lua/plenary.nvim'
Plug 'nvim-lualine/lualine.nvim'

Plug 'hrsh7th/nvim-cmp'
Plug 'hrsh7th/cmp-nvim-lsp'
Plug 'hrsh7th/cmp-buffer'
Plug 'hrsh7th/cmp-path'

Plug 'L3MON4D3/LuaSnip'

Plug 'tpope/vim-fugitive'
Plug 'lewis6991/gitsigns.nvim'

Plug 'mfussenegger/nvim-dap'
Plug 'mfussenegger/nvim-dap-python'

call plug#end()
```

------------------------------------------------------------------------

# 22. Recommended Python Toolchain

Install

    pip install \
    black \
    ruff \
    isort \
    pyright \
    debugpy

------------------------------------------------------------------------

# 23. Daily Workflow Example

Open project

    nvim .

Find file

    :Telescope find_files

Search code

    :Telescope live_grep

Go to definition

    gd

Run tests

    pytest

------------------------------------------------------------------------

# 24. Performance Tips

Avoid too many plugins.

Use lazy loading.

Prefer Lua plugins.

------------------------------------------------------------------------

# End of Manual

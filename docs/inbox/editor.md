# editor ideas

## generic and modular

there should be 2 default configurations: vscode-like (mouse first) and vim-like
(keyboard first) and they should be provided as plugins (to be able to create
new. e.g. helix, emacs, ...)

plugins should be a core feature and easy to create and share (maybe a lua or
lisp api)

plugins can depend on other plugins

## multiple frontends

editor is just a server. you can access it from web, terminal, gui, ..., all
with same session allowing also for collaborative editing. that also means split
configuration for server/frontend

## speed

should be able to handle big files

## treesitter plugin

leverage existing tree-sitter parsers

## per-filetype configuration

and there may be inline languages (e.g. js in html, code blocks in markdown)

## good defaults

should be usable out of the box. that means a lot of opt-out plugins.

allow configuration to be switched while running

## features

### folds

### view-only modes

e.g. log files, presentations,
